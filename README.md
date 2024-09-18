# Dokumentacja stworzenia własnego klastra Kubernetes z wykorzystaniem narzędzi Vagrant, Ansible i Kubespray

<p style="text-align:center;">
Dokumentacja zawiera informacje jak krok po kroku zainstalować odpowiednie oprogramowanie oraz jak poprawnie przygotować środowisko do pracy z plikami Vagrantfile oraz maszynami wirtualnymi w VirtualBox firmy Oracle. Dodatkowo pokażę jak w bezpieczny sposób podłączyć się do urządzeń z wykorzystaniem ssh i klucza publicznego w celu zabezpieczenia komunikacji pomiędzy hostem sterującym a hostami w klastrze.
</p>

- [1. WSL](#1-wsl)


## 1. WSL
W realizacji projektu wykorzystam WSL czyli Windows Subsystem for Linux. Jest to część systemu operacyjnego Windows, które zostało wprowadzone w wersji 10. W moim przypadku wykorzystam dystrybuję Ubuntu która domyślnie jest instalowana w ramach WSL.

Aby zainstalować oprogramowanie wystarczy wykorzystać polecenie: `wsl --install`, zostanie zainstalowana dystrybucja Ubuntu, jeśli chcemy wykorzystać inną dystrybucję musimy wykorzystać to polecenie: `wsl --install -d <Nazwa-dystrybucji>`

Po instalacji warto sprawdzić czy wszystko się poprawnie pobrało: `wsl -l`

![image](https://github.com/user-attachments/assets/44a1f80a-d9ba-4937-bf79-282856ad8e1a)
