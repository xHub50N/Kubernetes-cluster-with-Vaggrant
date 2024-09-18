# Dokumentacja stworzenia własnego klastra Kubernetes z wykorzystaniem narzędzi Vagrant, Ansible i Kubespray

<p style="text-align:center;">
Dokumentacja zawiera informacje jak krok po kroku zainstalować odpowiednie oprogramowanie oraz jak poprawnie przygotować środowisko do pracy z plikami Vagrantfile oraz maszynami wirtualnymi w VirtualBox firmy Oracle. Dodatkowo pokażę jak w bezpieczny sposób podłączyć się do urządzeń z wykorzystaniem ssh i klucza publicznego w celu zabezpieczenia komunikacji pomiędzy hostem sterującym a hostami w klastrze.
</p>

- [1. WSL](#1-wsl)
- [2. Oracle VirtualBox](#2-Oracle-VirtualBox)
- [3. Vagrant](#3-Vagrant)


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

## 3. Vagrant 
Vagrant to narzędzie do automatyzacji tworzenia i zarządzania środowiskami wirtualnymi. Umożliwia szybkie uruchamianie wirtualnych maszyn na różnych platformach, takich jak VirtualBox, Docker czy AWS, przy użyciu prostych skryptów. Vagrant automatyzuje proces konfiguracji, co ułatwia tworzenie spójnych i powtarzalnych środowisk programistycznych, minimalizując problemy wynikające z różnic w konfiguracji systemów.

W naszym przypadku zainstalujemy Vagranta z poziomu systemu Ubuntu. Więcej informacji o instalacji można znaleźć w dokumentacji [Instalacja Vagranta na Linuxie](https://developer.hashicorp.com/vagrant/install#linux)
`wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg`
`echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list`
`sudo apt update && sudo apt install vagrant`
