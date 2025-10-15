# 📌 **Środowisko lokalne do pracy z Pythonem w kontenerze**

Na tych zajęciach skonfigurujemy środowisko do pracy z Pythonem w kontenerze **Docker**. Dzięki temu każdy będzie miał **spójne, odizolowane środowisko**, w którym można instalować pakiety, uruchamiać skrypty i pracować z Pythonem **interaktywnie z poziomu IDE**.

---

## 🏁 1. Połączenie z repozytorium w GitHub

### 1.1 Instalacja git

Jeśli nie masz jeszcze gita, możesz go zainstalować:

- **Linux (Ubuntu/Debian):**
  ```sh
  sudo apt update
  sudo apt install git
  ```

- **Windows:** Pobierz instalator ze strony [git-scm.com](https://git-scm.com/) i zainstaluj.

- **macOS:**
  ```sh
  brew install git
  ```

Po instalacji sprawdź wersję w terminalu:
```sh
git --version
```

### 1.2 Klonowanie repozytorium zdalnego

!!! danger "Link do Twojego repozytorium zdalnego dostaniesz od prowadzącego zajęcia!"

Stwórz lokalną kopię repozytorium, użyj komendy:
```sh
git clone https://github.com/uzytkownik/nazwa-repozytorium.git
```
Lub jeśli używasz SSH:
```sh
git clone git@github.com:uzytkownik/nazwa-repozytorium.git
```

### 1.3 Konfiguracja użytkownika

1. Wejdź w terminalu do folderu ze sklonowanym projektem.
2. Skonfiguruj swoje dane:

```sh
git config user.name "Twoje Imię i Nazwisko"
git config user.email "twoj@email.com"
```

## 🛠 2. Instalacja Dockera

???- info "Jak działa Docker?"

    Docker to platforma do uruchamiania aplikacji w kontenerach, czyli lekkich, odizolowanych środowiskach, które zawierają wszystko, co jest potrzebne do działania aplikacji (kod, zależności, konfigurację). Dzięki temu aplikacje działają identycznie niezależnie od systemu operacyjnego.

    - Każdy kontener działa jak mini-wirtualna maszyna, ale jest lżejszy i bardziej wydajny.

    - Docker używa obrazów – predefiniowanych pakietów, które można wielokrotnie uruchamiać jako kontenery.

    - Izolacja pozwala uniknąć konfliktów zależności i różnic między środowiskami.

???- info "Po co nam Docker?"

    Docker jest przydatny, ponieważ:

    - Eliminuje problem „u mnie działa” – środowisko w kontenerze jest identyczne na każdym komputerze.

    - Łatwo można współdzielić środowiska – wystarczy przekazać pliki Dockerfile i devcontainer.json.

    - Szybkie wdrażanie – nowe środowisko można uruchomić w kilka sekund.

    - Ułatwia pracę zespołową – każdy programista ma tę samą konfigurację.

    - Można uruchamiać aplikacje w chmurze i na serwerach bez zmian w kodzie.

    Docker to idealne narzędzie dla programistów, którzy chcą pracować w powtarzalnym i stabilnym środowisku! 🚀

### 📥 2.1. Instalacja na Windows/macOS/Linux
1. Pobierz i zainstaluj **Docker Desktop**: [Pobierz Docker](https://www.docker.com/get-started)
2. Sprawdź instalację:
    ```sh
    docker --version
    ```
3. Upewnij się, że Docker jest **uruchomiony**.

---

## 🔧 3. Konfiguracja IDE (VS Code) z Dockerem i Miniforge

- **Otwórz katalog projektu** w VS Code.
- Zapoznaj się z widocznymi tam plikami:

???- info "Plik `Dockerfile`"

    Ten plik definiuje obraz Dockera z preinstalowanym Pythonem i condą.

    ```dockerfile
    FROM condaforge/miniforge3:latest

    WORKDIR /app

    CMD ["/bin/bash", "-l"]
    ```

???- info "Plik `./.devcontainer/devcontainer.json`"

    Ten plik konfiguruje VS Code do pracy w kontenerze Dockera z odpowiednimi rozszerzeniami.

    ```json
    {
        "name": "Python Dev Container",
        "build": {
            "dockerfile": "../Dockerfile"
        },
        "workspaceFolder": "/app",
        "mounts": [
            "source=${localWorkspaceFolder},target=/app,type=bind,consistency=cached"
        ],
        "customizations": {
            "vscode": {
                "settings": {
                    "terminal.integrated.defaultProfile.linux": "bash"
                },
                "extensions": [
                    "ms-python.python",
                    "ms-vscode-remote.remote-containers",
                    "ms-vscode-remote.dev-containers"
                ]
            }
        }
    }
    ```

- Ręcznie zbuduj obraz w terminalu:
```sh
docker build -t python-env .
```

- Uruchom kontener w trybie interaktywnym i podłącz lokalny katalog:
```sh
docker run -it -v .:/app python-env /bin/bash -l
```
Dzięki temu wszystkie zmiany w projekcie będą widoczne w kontenerze.

---

Teraz chcemy sobie ułatwić życie, połączyć się do kontenera bezpośrednio z VS Code: 

- **Zainstaluj rozszerzenie** ➝ `Remote - Containers` lub `Dev Container`.
- Kliknij `Ctrl+Shift+P` i wybierz **`Remote-Containers: Reopen in Container`**.
- VS Code uruchomi kontener i otworzy projekt w odizolowanym środowisku.
- Otwórz terminal w VS Code (`Ctrl+~`) i sprawdź aktywne środowisko Conda:
   ```sh
   conda info --envs
   ```
   Powinieneś zobaczyć, że aktywowane jest środowisko `base`.

---

## 📦 5. Instalacja pakietów wewnątrz kontenera

!!! danger "Tego kroku nie robimy, jest czysto informacyjny."

Gdy kontener jest uruchomiony, można instalować pakiety za pomocą **mamby**:

```sh
micromamba install -y -n base -c conda-forge matplotlib geopandas
```
Pakiety można też instalować przez **pip**:
```sh
pip install requests
```

---

## 📝 Zadania

Utwórz plik `main.py`:

```python
import this
```

Uruchom go w kontenerze:

```sh
python main.py
```

Zrób zrzut ekranu (screenshot) pokazujący wynik działania tego skryptu w terminalu kontenera i wyślij go na czat 📸

---
