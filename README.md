# Dokumentacja stworzenia własnego klastra Kubernetes z wykorzystaniem narzędzi Vagrant, Ansible i Kubespray

<p style="text-align:center;">
Dokumentacja zawiera informacje jak krok po kroku zainstalować odpowiednie oprogramowanie oraz jak poprawnie przygotować środowisko do pracy z plikami Vagrantfile oraz maszynami wirtualnymi w VirtualBox firmy Oracle. Dodatkowo pokażę jak w bezpieczny sposób podłączyć się do urządzeń z wykorzystaniem ssh i klucza publicznego w celu zabezpieczenia komunikacji pomiędzy hostem sterującym a hostami w klastrze.
</p>

- [1. WSL](#1-wsl)
- [2. Oracle VirtualBox](#2-Oracle-VirtualBox)
- [3. Podłączenie VirtualBox do WSL](#3-Podłączenie-VirtualBox-do-WSL)
  - [3.1. Instalacja Vagrant](#31-Instalacja-Vagrant)
  - [3.2. Instalacja Ansible](#32-Instalacja-Ansible)
  - [3.3. Konfiguracja Ubuntu](#33-Konfiguracja-Ubuntu)
  - [3.4. Import pliku Vagrantfile](#34-Import-pliku-Vagrantfile)
- [4. Konfiguracja wirtualnych maszyn](#4-Konfiguracja-wirtualnych-maszyn)
  - [4.1. Generowanie klucza prywatnego i publicznego](#41-Generowanie-klucza-prywatnego-i-publicznego)
  - [4.2. DNS](#42-DNS)
- [5. Kubespray](#5-Kubespray)
  - [5.1. Wstępna konfiguracja](#51-Wstepna-konfiguracja)
  - [5.2. Konfiguracja Kubectl](#52-Konfiguracja-Kubectl)


## 1. WSL
W realizacji projektu wykorzystam WSL czyli Windows Subsystem for Linux. Jest to część systemu operacyjnego Windows, które zostało wprowadzone w wersji 10. W moim przypadku wykorzystam dystrybuję Ubuntu która domyślnie jest instalowana w ramach WSL.

Aby zainstalować oprogramowanie wystarczy wykorzystać polecenie: `wsl --install`, zostanie zainstalowana dystrybucja Ubuntu, jeśli chcemy wykorzystać inną dystrybucję musimy wykorzystać to polecenie: `wsl --install -d <Nazwa-dystrybucji>`. Istnieje jeszcze polecenie `wsl -o -l`, które wyświetla wszystkie dostępne dystrybucje Linuxa wspierające WSL.

Po instalacji warto sprawdzić czy wszystko się poprawnie pobrało: `wsl -l`

![image](https://github.com/user-attachments/assets/44a1f80a-d9ba-4937-bf79-282856ad8e1a)

Aby przejść do systemu ubuntu wystarczy wpisać komendę: `wsl -d Ubuntu` i automatycznie zostaniemy zalogowani do systemu. 

![image](https://github.com/user-attachments/assets/918644af-509d-4bd9-b4f8-7391da661102)

## 2. Oracle VirtualBox
Oracle VirtualBox to darmowe oprogramowanie do wirtualizacji, które umożliwia uruchamianie wielu systemów operacyjnych na jednym fizycznym komputerze. Dzięki VirtualBox można tworzyć i zarządzać maszynami wirtualnymi, co pozwala na testowanie różnych systemów operacyjnych, aplikacji oraz symulowanie różnych środowisk bez potrzeby instalacji ich bezpośrednio na komputerze.

Aby zainstalować oprogramowanie należy przejść do strony [VirtualBox Download](https://www.virtualbox.org/wiki/Downloads), następnie wybieramy wersję dla naszego hosta. W moim przypadku jest to Windows.

## 3. Podłączenie VirtualBox do WSL
W tym kroku wykonam podłączenie VirtualBox-a pod system WSL. Ten krok jest bardzo ważny w dalszym działaniu naszego środowiska. Do naszego projektu wykorzystam oprogramowanie **Ansible** i jest ono dostępne tylko z poziomu Linuxa. Dzięki WSL możemy zainstalować na nim Ansible, który będzie wykonywać zadania na wirtualnych maszynach dostępnych z poziomu VirtualBoxa który jest zainstalowany na Windows.

### 3.1. Instalacja Vagrant 
Vagrant to narzędzie do automatyzacji tworzenia i zarządzania środowiskami wirtualnymi. Umożliwia szybkie uruchamianie wirtualnych maszyn na różnych platformach, takich jak VirtualBox, Docker czy AWS, przy użyciu prostych skryptów. Vagrant automatyzuje proces konfiguracji, co ułatwia tworzenie spójnych i powtarzalnych środowisk programistycznych, minimalizując problemy wynikające z różnic w konfiguracji systemów.

W naszym przypadku zainstalujemy Vagranta z poziomu systemu Ubuntu. Więcej informacji o instalacji można znaleźć w dokumentacji [Instalacja Vagranta na Linuxie](https://developer.hashicorp.com/vagrant/install#linux)

`wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg`

`echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list`

`sudo apt update && sudo apt install vagrant`

Sprawdzenie czy Vagrant został zainstlowany:
![image](https://github.com/user-attachments/assets/537feb4e-8f44-4f8c-ae71-2477ef6f110e)

### 3.2 Instalacja Ansible
Ansible to narzędzie do automatyzacji zarządzania konfiguracją, wdrażania aplikacji i orkiestracji systemów. Umożliwia zarządzanie wieloma serwerami z jednego miejsca, używając prostych plików konfiguracyjnych (tzw. playbooków) zapisanych w formacie YAML. Ansible działa bez agentów, wykorzystując SSH do komunikacji, co czyni go łatwym w implementacji i bardzo elastycznym narzędziem do automatyzacji procesów IT.

Aby zainstalować Ansible musimy wykonać odpowiednie polecenia:

`sudo apt-add-repository ppa:ansible/ansible`

`sudo apt update`

`sudo apt install ansible`

Sprawdzenie poprawności instalacji Ansible

![image](https://github.com/user-attachments/assets/32863f4a-d236-4d63-870b-a823e533ede4)

### 3.3 Konfiguracja Ubuntu
Aby podłączyć VirtualBox do Ubuntu należy wykonać te polecenia:

`echo 'export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"' >> ~/.bashrc`

`echo 'export PATH="$PATH:/mnt/c/Program Files/Oracle/VirtualBox"' >> ~/.bashrc`

`echo 'export VAGRANT_WSL_WINDOWS_ACCESS_USER_HOME_PATH="/mnt/c/Users/huber"'`

`source ~/.bashrc`

Oraz dodatkowo musimy zainstalować plugin do vagranta
`vagrant plugin install virtualbox_WSL2`

### 3.4 Import pliku Vagrantfile
Zalecam aby wykorzystać lokalizację którą podaliśmy jako domowy katalog użytkownika w Windows tak jak u mnie czyli /mnt/c/Users/huber **Warto zwrócić uwagę na to że nie podajemy ścieżki tak jak w Windows czyli C:\Users\huber tylko korzystamy z Linuxowego schematu. Pliki które są współdzielone z Linuxem znajdują się w katalogu /mnt**

Plik Vagrantfile jest w repozytorium i tam szczegółowo jest opisane w pliku się znajduje :)

![image](https://github.com/user-attachments/assets/88ace485-d0b4-4415-9e2b-cbcc69834237)

W taki sposób prezentuje się moja struktura katalogów. 
Aby móc uruchomić maszyny wirtualne należy wykorzystać polecenie `vagrant up` **WAŻNA INFORMACJA!! Jeśli wykonujemy operacje na pliki Vagrantfile musimy znajdować się w katalogu gdzie jest plik do którego będziemy się odnosić**

![image](https://github.com/user-attachments/assets/76808e21-064f-4203-9927-067dc8fc7af2)

Ten widok pokazuje nam że zadania dla maszyny srv3 (te same zadania zostały wykonane dla maszyny srv1 i srv2) zostały poprawnie wykonane oraz że wszystkie maszyny zostaly uruchomione i system został zainstalowany

![image](https://github.com/user-attachments/assets/84b06313-5f6b-427f-b063-ced9036ed198)

## 4. Konfiguracja wirtualnych maszyn
Na początku musimy się upewnić że komunikacja pomiędzy WSL a wirtualnymi maszynami przebiega sprawnie. W tym celu wykorzystamy polecenie `vagrant ssh <nazwa_maszyny>`. **Należy pamiętać aby wykonywać polecenia w folderze w którym znajduej się plik Vagrantfile**

![image](https://github.com/user-attachments/assets/06d1b64b-93c2-48be-8fad-e1434e8d87b1)

![image](https://github.com/user-attachments/assets/6d9c0540-5153-4f1d-9ff7-f702835e3a7f)

![image](https://github.com/user-attachments/assets/dd0ee38d-374d-4ffe-b6cb-1c8e507c6b1c)

Do wszystkich maszyn mamy dostęp poprzez SSH. Warto zaznaczyć że na każdej z maszyn istnieje użytkownik **vagrant**

### 4.1 Generowanie klucza prywatnego i publicznego
Nie zawsze będziemy mogli albo chcieli wykorzystać polecenie `vagrant ssh <nazwa_maszyny>`. W tym celu wykorzystamy mechanizm generowania kluczy publicznych i prywatnych za pomocą oprogramowania OPENSSH.

Polecenie do generowania kluczy to `ssh-keygen -t ed25519 -b 521`, następnie zostaniemy poproszeni o nazwanie pliku i nadanie hasła (obie opcje nie są wymagane, chociaż zalecam aby nadać hasło :) )

![image](https://github.com/user-attachments/assets/5866d16f-52c4-4a0a-828b-205ad1c2debb)

Następnie musimy skopiować wartość klucza publicznego aby można było go wkleić do zdalnych hostów.

![image](https://github.com/user-attachments/assets/2e1691af-87fb-4da2-a91a-968fe9480d58)

Za pomocą naszego ulubionego edytora tekstu edytujemy plik /home/vagrant/.ssh/authorized_keys

![image](https://github.com/user-attachments/assets/958cfd43-0ed5-488d-a506-51a48c80b705)

Tą samą czynność wykonujemy na hoscie srv2 i srv3.

### 4.2 DNS

Z pewnością przyda nam się aby nasze hosty widziały się po nazwach DNS aby nam ułatwić pracę z zapamiętywaniem nazw a nie całych adresów IP. Musimy zedytować plik `/etc/hosts`

![image](https://github.com/user-attachments/assets/2489c275-561d-4b75-936e-3cb0c7ad15e5)

Dodajemy adresy IP naszych zdalnych hostów i ich nazwy. Wykonujemy to na systemie WSL i na hoscie srv1, srv2 i srv3.

![image](https://github.com/user-attachments/assets/9965021c-bf50-46af-8040-7ea0016ec753)

Jak widać udało się podłączyć do hosta srv1 za pomocą klucza!

## 5. Kubespray
Kubespray to narzędzie open-source do automatyzacji wdrażania i zarządzania klastrami Kubernetes. Oparte na Ansible, umożliwia szybkie uruchomienie klastrów Kubernetes na różnych infrastrukturach (chmura, serwery fizyczne lub wirtualne). Kubespray wspiera funkcje, takie jak konfiguracja sieci, bezpieczeństwa i monitorowania, zapewniając elastyczność i gotowe do produkcji środowiska Kubernetes.

Repozytorium Kubespray można znaleźć na githubie [Kubespray-repo](https://github.com/kubernetes-sigs/kubespray/tree/master)

Wpierw musimy ściągnąć repozytorium na naszego WSL. To on będzie hostem nadzorującym cały klaster także na nim wykonamy instalacje i konfigurację Kubespray.

![image](https://github.com/user-attachments/assets/7c10aca1-b668-4134-976f-5456f3ed4c0c)

**UWAGA! Polecenia dotyczące Kubespray musimy wykonać jednocześnie będąć w katalogu repozytorium**

### 5.1 Wstępna konfiguracja 
W pierwszej kolejności musimy pobrać manager pakietów pip. Ponieważ Kubespray opiera się na Ansible a Ansible jest technologią opartą na pythonie. W tym celu wykorzystamy polecenie: 

`sudo apt install python3-pip python3-distutils python3-apt`

Następnie musimy zainstalować odpowiednie zależności z pliku `pip3 install -r requirements.txt`

Ostanimi z poleceń konfiguracyjnych są: 

`cp -rfp inventory/sample inventory/mycluster` - inventory/{nazwa_folderu}, w moim przypadku zostało domyślne mycluster **Musimy pamiętać aby własną nazwę klastra stosować wszędzie**

`declare -a IPS=(192.168.1.110 192.168.1.111 192.168.1.112)`

`CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}` **Tutaj można wykorzystać własną nazwe katalogu klastra**

![image](https://github.com/user-attachments/assets/7f615efa-638e-47e9-9726-185c4d70fa89)

Musimy zedytować plik konfiguracyjny `~/kubespray/inventory/mycluster/hosts.yaml` dotyczący hostów ponieważ domyślne nazwy DNS nie pasują do rzeczywistych nazw hostów 

![image](https://github.com/user-attachments/assets/22e4dd85-10fd-4f26-8722-ba9f125ff78d)

Na samym końcu musimy wykonać polecenie:

`ansible-playbook -i inventory/mycluster/hosts.yaml --become --become-user=root -u vagrant --private-key=~/.ssh/id_ed25519 cluster.yml` 

Ono sprawi że nasz klaster zostanie stworzony na hostach. Instalacja może trochę potrwać więc można zrobić sobie przerwę na kawę, oczywiście warto pilnować jakie komunikaty są wyświetlane na ekranie!

Po instalacji powinien wyświetlić się taki komunikat:
![image](https://github.com/user-attachments/assets/aea4af38-5181-4aa0-b179-0464729c29e0)

Oznacza to że nasz klaster został utworzony!

### 5.2 Konfiguracja Kubectl
Na maszynie referencyjnej musimy zainstalować oprogramowanie Kubectl aby można było komunikować się z całym klastrem Kubernetes.

Musimy wykonać te polecenia aby zainstalować Kubectl.

`curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"`

`sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl`

Na sam koniec możemy sprawdzić czy oprogramowanie zostało zainstalowane:

![image](https://github.com/user-attachments/assets/55380165-d397-4548-a49d-6a877e615341)

Kolejnym krokiem będzie przekopiowanie folderu .kube z maszyny srv1 która służy jako kube control plane. Kube control plane jest to swego rodzaju manager który zarządza innymi węzłami w klastrze.

W pierwszej kolejności musimy przekopiować katalog .kube do domowego katalogu użytkownika vagrant `cp -r /root/.kube /home/vagrant/`, musimy zmienić właściciela folderu w celu przesłania go przez SSH na system referencyjny `chown -R vagrant:vagrant /home/vagrant.kube`

Na WSL musimy zainstalować dodatek do Vagranta a mianowicie `vagrant plugin install vagrant-scp` jest to wtyczka która ułatwia pobieraniem i wysyłaniem plików pomiędzy maszynmi działającymi w środowisku vagrant.

Następnie ściagamy odpowiedni folder .kube na system WSL `vagrant scp srv1:/home/vagrant/.kube /root`

Ostatnią rzeczą którą musimy wykonać to edycja pliku /root/.kube/config a mianowicie musimy podać adres IP hosta który służy jako control plane

![image](https://github.com/user-attachments/assets/d6e5737a-9618-49c6-a065-cca9acb9cefe)

Na sam koniec sprawdzimy czy WSL widzi wszystkie węzły z poziomu kubectl

![image](https://github.com/user-attachments/assets/a606d29d-f9d8-494f-a009-01f8bd296358)

Jak widać wszystko działa jak należy!


