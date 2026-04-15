# Automatyzacja zadań z Make

`GNU Make` to narzędzie automatyzujące procesy budowania oprogramowania dla developerów. Zostało pierwotnie stworzone dla systemów Unix w latach 70, aby ułatwić kompilację kodu źródłowego w językach takich jak C. `Makefile` to plik konfiguracyjny, w którym definiuje się zasady, jak narzędzie `make` ma wykonywać zautomatyzowane zadania.

## Jak działa Make?

Działa na podstawie zależności między plikami. Dla każdego zadania (`target`) definiujemy:

1. **Plik docelowy (target)** - co chcemy osiągnąć (np. skompilowany program, przetestowana aplikacja).
2. **Zależności (dependencies)** - co jest wymagane, aby zrealizować zadanie.
3. **Komendy (commands)** - jak wykonać zadanie.

Generalnie korzysta się z niego prosto, a bardzo ułatwia życie.

### Zalety Make

1. **Automatyzowanie procesów** - pozwala zautomatyzować powtarzalne zadania, takie jak uruchamianie testów, kompilacja, instalacja zależności czy budowa projektu.
2. **Przenośność** - działa na różnych systemach operacyjnych (Unix, Linux, macOS, Windows z odpowiednimi narzędziami).
3. **Czytelność** - zamiast opisywać procesy w dokumentacji, wszystkie kroki są zdefiniowane w jednym pliku `Makefile`, co ułatwia nowym programistom zrozumienie projektu.
4. **Uniwersalność** - pomimo historycznego związku z `C` i `C++`, `Makefile` jest obecnie używany w wielu językach programowania, takich jak `Python`, `Go` czy `Rust`.

## Instalacja Make

Żeby sprawdzić czy narzędzie jest dostępne, wystarczy uruchomić w konsoli `make --version`.

??? - "Instalacja na **Linux**"
    Na większości dystrybucji GNU Make jest dostępny w standardowych repozytoriach.

    ```bash
    sudo apt update
    sudo apt install make
    ```

??? - "Instalacja na **Windows**"
    Na Windows GNU Make nie jest instalowane domyślnie, ale można je zainstalować za pomocą różnych narzędzi.

    Tutaj przykład dla Chocolatey (które trzeba też najpierw [zainstalować](https://chocolatey.org/install)):

    ```cmd
    choco install make
    ```

    Alternatywnie można użyć [Git Bash](https://git-scm.com/downloads), który zawiera make.

??? - "Instalacja na **macOS**"
    W przypadku Homebrew GNU Make może być dostępny jako `gmake`, aby odróżnić go od wersji dostarczanej z systemem.

    ```bash
    brew install make
    ```

    Lub użyj systemowego make (zwykle już zainstalowany).

## Podstawowa składnia Makefile

### Struktura targetu

```makefile
target: dependencies
	command1
	command2
	command3
```

**Ważne:** Komendy muszą być poprzedzone **tabem** (nie spacjami)!

## Czym jest .PHONY?

`.PHONY` to specjalna dyrektywa w Makefile, która informuje Make, że target **nie tworzy pliku o tej nazwie**. 

**Dlaczego to ważne?**

Jeśli masz target `test` i przypadkowo utworzysz plik o nazwie `test`, Make pomyśli że target jest już "zrobiony" (bo plik istnieje) i nie uruchomi komend. `.PHONY` zapobiega temu problemowi.

```makefile
# Bez .PHONY - może być problem
test:
	pytest

# Z .PHONY - zawsze uruchomi się, nawet jeśli istnieje plik 'test'
.PHONY: test
test:
	pytest
```

**Zalecenie:** Zawsze używaj `.PHONY` dla targetów, które nie tworzą plików!

## Używanie zmiennych

### Podstawowe zmienne

```makefile
# Definicja
PYTHON = python3
VERSION = 1.0.0

# Użycie
.PHONY: version
version:
	@echo "Wersja: $(VERSION)"
	$(PYTHON) --version
```

### Zmienne środowiskowe

```makefile
# Użycie zmiennej środowiskowej (jeśli nie zdefiniowana, użyj domyślnej)
PYTHON ?= python3

# Automatyczne zmienne
.PHONY: example
example:
	@echo "Target: $@"        # Nazwa targetu
	@echo "Zależności: $^"    # Wszystkie zależności
	@echo "Pierwsza: $<"      # Pierwsza zależność
```

## Przykładowy Makefile dla projektu Python

Oto kompletny przykład Makefile dla projektu Python z conda/mamba:

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

## Uruchamianie komend Make

### Podstawowe użycie

```bash
# Uruchom domyślny target (zwykle 'help' lub pierwszy)
make

# Uruchom konkretny target
make test

# Uruchom target z parametrami (jeśli zdefiniowane)
make install PACKAGE=numpy
```

### Przydatne flagi

```bash
# Pokaż komendy które będą wykonane (dry-run)
make -n test

# Ignoruj błędy i kontynuuj
make -k all

# Uruchom równolegle (jeśli targety są niezależne)
make -j4 all
```

## Najlepsze praktyki

### 1. Zawsze używaj .PHONY

```makefile
.PHONY: test clean install
```

### 2. Używaj zmiennych dla powtarzających się wartości

```makefile
PYTHON = python3
SRC = src/
TEST = tests/
```

### 3. Dodaj target 'help'

```makefile
.PHONY: help
help:
	@echo "Dostępne komendy:"
	@echo "  make test  - Uruchom testy"
```

### 4. Grupuj powiązane targety

```makefile
# Development
.PHONY: dev-install dev-test dev-run

# Production
.PHONY: build deploy
```

### 5. Używaj zależności zamiast duplikować komendy

```makefile
# Źle - duplikacja
test:
	pip install -r requirements.txt
	pytest

lint:
	pip install -r requirements.txt
	ruff check .

# Dobrze - użycie zależności
install:
	pip install -r requirements.txt

test: install
	pytest

lint: install
	ruff check .
```

## Częste problemy

### Problem: "missing separator"

**Przyczyna:** Używasz spacji zamiast tabu przed komendami.

**Rozwiązanie:** Upewnij się, że używasz **tabu** (nie spacji) przed każdą komendą.

### Problem: Target nie uruchamia się

**Przyczyna:** Istnieje plik o nazwie targetu.

**Rozwiązanie:** Dodaj `.PHONY: target-name` przed definicją targetu.

### Problem: Zmienne nie działają

**Przyczyna:** Błędna składnia.

**Rozwiązanie:** Użyj `$(VAR)` lub `${VAR}` do odwołania do zmiennej.

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

2. Stwórz plik `Makefile` w głównym katalogu Twojego projektu zgodnie z przykładem u góry.

3. Usuń poprzednio stworzone środowisko wirtualne i stwórz je ponownie wykorzystując nowe pliki blokady. Komenda `make recreate-env` automatycznie:
    
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
   # Sprawdź czy make działa
   make --version
   ```
