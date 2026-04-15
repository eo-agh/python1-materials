# Automatyzacja zadań z Make

`GNU Make` to narzędzie automatyzujące powtarzalne zadania — uruchamianie testów, instalację zależności, formatowanie kodu. Zamiast opisywać procesy w dokumentacji, definiuje się je raz w pliku `Makefile`. Pierwotnie stworzony dla kompilacji C/C++, dziś używany w projektach Pythona, Go, Rust i wielu innych.

## Jak działa Make?

Dla każdego zadania (`target`) definiujemy zależności i komendy do wykonania:

```makefile
target: dependencies
	command1
	command2
```

**Ważne:** Komendy muszą być poprzedzone **tabulatorem** (nie spacjami)!

## Instalacja

Sprawdź czy narzędzie jest dostępne: `make --version`.

??? - "Instalacja na **Linux**"
    ```bash
    sudo apt update
    sudo apt install make
    ```

??? - "Instalacja na **Windows**"
    Chocolatey (wymaga [wcześniejszej instalacji](https://chocolatey.org/install)):

    ```cmd
    choco install make
    ```

    Alternatywnie: [Git Bash](https://git-scm.com/downloads), który zawiera make.

??? - "Instalacja na **macOS**"
    ```bash
    brew install make
    ```

    Lub użyj systemowego make (zwykle już zainstalowany).

## Czym jest `.PHONY`?

`.PHONY` informuje Make, że target **nie tworzy pliku o tej nazwie**. Bez tego, jeśli w projekcie istnieje plik `test`, Make uzna że target `test` jest już "zrobiony" i nie uruchomi komend.

```makefile
# Zawsze uruchomi się, nawet jeśli istnieje plik 'test'
.PHONY: test
test:
	pytest
```

**Zalecenie:** Zawsze używaj `.PHONY` dla targetów, które nie tworzą plików.

## Zmienne

```makefile
PYTHON = python3
ENV_NAME = python1course-env

# Użycie zmiennej środowiskowej (jeśli nie zdefiniowana, użyj domyślnej)
PYTHON ?= python3

.PHONY: version
version:
	@echo "Python: $(PYTHON)"
	$(PYTHON) --version
```

## Przykładowy Makefile dla projektu Python

```makefile
# Zmienne
PYTHON = python
MAMBA = mamba
RUFF = ruff
PYTEST = pytest
ENV_NAME = python1course-env

# Domyślny target
.PHONY: help
help:
	@echo "Dostępne komendy:"
	@echo "  make env              - Utwórz środowisko i zainstaluj pre-commit"
	@echo "  make lock-file        - Wygeneruj pliki conda-lock"
	@echo "  make conda-lock-install - Zainstaluj środowisko z lock file"
	@echo "  make setup-pre-commit - Zainstaluj pre-commit hooks"
	@echo "  make lint             - Sprawdź kod używając ruff"
	@echo "  make format           - Sformatuj kod"
	@echo "  make test             - Uruchom testy"
	@echo "  make clean            - Usuń pliki tymczasowe"
	@echo "  make recreate-env     - Usuń i odtwórz środowisko"

# Generowanie lock files
.PHONY: lock-file
lock-file:
	conda-lock --mamba -f env.yml -f env-dev.yml --lockfile conda-lock-dev.yml
	conda-lock --mamba -f env.yml --lockfile conda-lock.yml

# Instalacja środowiska z lock file
.PHONY: conda-lock-install
conda-lock-install:
	conda-lock install --mamba -n $(ENV_NAME) conda-lock-dev.yml

# Instalacja pre-commit
.PHONY: setup-pre-commit
setup-pre-commit:
	pre-commit install

# Stwórz środowisko
.PHONY: env
env: conda-lock-install

# Linting
.PHONY: lint
lint:
	$(RUFF) check .

# Formatowanie
.PHONY: format
format:
	$(RUFF) format .
	$(RUFF) check --fix .

# Testy
.PHONY: test
test:
	$(PYTEST) tests/

# Czyszczenie
.PHONY: clean
clean:
	rm -rf __pycache__/
	rm -rf .pytest_cache/
	rm -rf dist/
	rm -rf build/
	rm -rf *.egg-info
	find . -type d -name "*.egg-info" -exec rm -rf {} +
	find . -type f -name "*.pyc" -delete

# Usunięcie środowiska
.PHONY: remove-env
remove-env:
	mamba env remove -n $(ENV_NAME)

# Odtworzenie środowiska od zera
.PHONY: recreate-env
recreate-env: remove-env lock-file env
	@echo "Środowisko zostało odtworzone!"
```

## Uruchamianie

```bash
# Uruchom domyślny target (help)
make

# Uruchom konkretny target
make test

# Dry-run — pokaż komendy bez wykonywania
make -n test
```

??? - tip "Najlepsze praktyki"

    1. **Zawsze używaj `.PHONY`** dla targetów, które nie tworzą plików.
    2. **Używaj zmiennych** dla powtarzających się wartości (`PYTHON`, `ENV_NAME`).
    3. **Dodaj target `help`** jako domyślny — ułatwia nowym osobom odnalezienie się w projekcie.
    4. **Używaj zależności** zamiast duplikować komendy — jeśli `test` i `lint` potrzebują instalacji, zrób `install` jako wspólną zależność.

??? - warning "Częste problemy"

    **`missing separator`** — używasz spacji zamiast tabulatora przed komendami. Każda komenda w targecie musi zaczynać się od **taba**.

    **Target nie uruchamia się** — istnieje plik o nazwie targetu. Dodaj `.PHONY: nazwa-targetu`.

    **Zmienne nie działają** — błędna składnia. Używaj `$(VAR)` lub `${VAR}`.

## 📝 Zadania

### Część 1: Dodanie narzędzi do środowiska developerskiego

!!! warning "Wymagane: instalacja make"

    Przed rozpoczęciem zadań musisz zainstalować `make` w kontenerze Docker. Wykonaj w terminalu kontenera:

    ```bash
    apt-get update && apt-get install -y make
    ```

    Sprawdź czy instalacja się powiodła:
    ```bash
    make --version
    ```

1. Dodaj `ruff` i `pre-commit` do pliku `env-dev.yml` w sekcji `dependencies`:
   ```yaml
   dependencies:
     - jupyter
     - ipykernel
     - pytest
     - ruff
     - pre-commit
   ```

2. Stwórz plik `Makefile` w głównym katalogu Twojego projektu zgodnie z przykładem powyżej.

3. Usuń poprzednio stworzone środowisko wirtualne i stwórz je ponownie. Komenda `make recreate-env` automatycznie:

    - Usunie istniejące środowisko (`remove-env`)
    - Wygeneruje nowe pliki blokady (`lock-file`)
    - Utworzy środowisko i zainstaluje pre-commit (`env`)

    ```bash
    make recreate-env
    ```

4. Zweryfikuj czy `ruff` i `pre-commit` są zainstalowane:
   ```bash
   ruff --version
   pre-commit --version
   ```

### Część 2: Instalacja Make w Dockerfile

5. Zmodyfikuj `Dockerfile` dodając instalację `make` przed instalacją środowiska:
   ```dockerfile
   FROM condaforge/miniforge3:latest

   WORKDIR /app

   # Zainstaluj make (nie ma go w miniforge3)
   RUN apt-get update && apt-get install -y make && rm -rf /var/lib/apt/lists/*

   # Kopiuj pliki konfiguracyjne środowiska
   COPY conda-lock-dev.yml .

   # Zainstaluj conda-lock (nie ma go w miniforge3) i środowisko
   RUN mamba install -y -n base conda-lock && \
       conda-lock install --mamba -n python1course-env conda-lock-dev.yml && \
       mamba clean --all -f -y

   # Inicjalizuj mamba i aktywuj środowisko domyślnie
   RUN mamba shell init --shell bash --root-prefix=/opt/conda && \
       echo "mamba activate python1course-env" >> ~/.bashrc
   ENV PATH="/opt/conda/envs/python1course-env/bin:$PATH"

   CMD ["/bin/bash", "-l"]
   ```

6. Przebuduj kontener Docker i sprawdź czy `make` działa:
   ```bash
   make --version
   ```
