# 📌 **Środowisko lokalne do pracy z Pythonem w Dockerze**

## 🏁 1. Wprowadzenie
Na tych zajęciach skonfigurujemy środowisko do pracy z Pythonem w kontenerze **Docker**. Dzięki temu każdy będzie miał **spójne, odizolowane środowisko**, w którym można instalować pakiety, uruchamiać skrypty i pracować z Pythonem **interaktywnie z poziomu IDE**.

---

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

## 🔧 3. Tworzenie środowiska w Dockerze

### 📂 3.1. Tworzenie folderu projektu
Wybierz katalog, w którym chcesz pracować:
```sh
mkdir python1 && cd python1
```

### 📜 3.2. Tworzenie pliku `Dockerfile`
Stwórz plik `Dockerfile` i dodaj następującą zawartość:
```dockerfile
FROM condaforge/miniforge3:latest

WORKDIR /app

CMD ["/bin/bash", "-l"]
```

### ⚙️ 3.3. Tworzenie pliku konfiguracyjnego VS Code
Aby ułatwić integrację Dockera z VS Code, utwórz folder `.devcontainer` w katalogu projektu i w nim plik `devcontainer.json`:
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

### 🚀 3.4. Budowanie obrazu Dockera
```sh
docker build -t python-env .
```

### 🔄 3.5. Uruchamianie kontenera
Uruchom kontener w trybie interaktywnym i podłącz lokalny katalog:
```sh
docker run -it -v .:/app python-env /bin/bash -l
```
Dzięki temu wszystkie zmiany w katalogu `python1` będą widoczne w kontenerze.

---

## 🖥️ 4. Konfiguracja IDE (VS Code) z Dockerem i Miniforge
Aby połączyć się z kontenerem w **VS Code**:

1. **Zainstaluj rozszerzenie** ➝ `Remote - Containers` lub `Dev Container`.
2. **Otwórz katalog projektu** w VS Code.
3. Kliknij `Ctrl+Shift+P` i wybierz **`Remote-Containers: Reopen in Container`**.
4. VS Code uruchomi kontener i otworzy projekt w odizolowanym środowisku.
5. Otwórz terminal w VS Code (`Ctrl+~`) i sprawdź aktywne środowisko Conda:
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

## 🏃‍♂️ 6. Uruchamianie skryptów

Utwórz plik `main.py`:

```python
import this
```

Uruchom go w kontenerze:

```sh
python main.py
```

**Zadanie:** Zrób zrzut ekranu (screenshot) pokazujący wynik działania tego skryptu w terminalu kontenera i wyślij go na czat. 📸

---
