# Tworzenie środowiska z użyciem mamba i conda-lock

## Wstęp

W tym przewodniku przejdziemy krok po kroku po tym jak stworzyć i zarządzać środowiskiem wirtualnym przy użyciu `mamba` i `conda-lock`. Będziemy bazować na plikach `env.yml` oraz `env-dev.yml`, które razem zawierają wszystkie niezbędne zależności dla środowiska deweloperskiego.


## Przygotowanie środowiska wirtualnego

!!! danger "Praca w Dockerze"
    
    Ponieważ pracujemy w oparciu o moje repozytorium, proszę nie wykonywać kroków oznaczonych ❌. Te kroki zostały już niejako "dostarczone" w ramach moich zmian.

### ❌ Krok 1: Przygotowanie plików definicji

!!! info "Co to jest `env-dev.yml`?"

    Pliki `env.yml` oraz `env-dev.yml` to konfiguracja środowiska, która określa:

    - Jakie pakiety Python są potrzebne,
    - Z jakich źródeł (channels) pobierać pakiety,
    - Jakie wersje pakietów są wymagane.

    W przypadku pliku z `dev`, mamy tam dodatkowe narzędzia deweloperskie, potrzebne tylko przy rozwoju naszego projektu.

Plik `env.yml` powinien zawierać następującą strukturę (w głównym katalogu projektu):

```yaml
name: python1course-env
platforms:
  - linux-64
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.12
  - pip
  - numpy
```

Plik `env-dev.yml` powinien zawierać następującą strukturę (w głównym katalogu projektu):

```yaml
name: python1course-env
category: dev
platforms:
  - linux-64
channels:
  - conda-forge
  - defaults
dependencies:
  - jupyter
  - ipykernel
  - pytest
```

!!! tip "Instalowanie poprzez `pip`"
    
    Istnieje także możliwość dodania sekcji instalowanej przez `pip` w ramach `dependencies`:

    ```yaml
    dependencies:
        - python=3.11
        - pip
        - numpy
        - pip:
          - coverage
          - pre-commit < 4.0
    ```

### ✅ Krok 2: Generowanie plików blokady

Najpierw należy się upewnić, że `conda-lock` jest zainstalowane w środowisku bazowym:

```bash
mamba install -c conda-forge conda-lock
```

!!! info "Co to jest `conda-lock`?"

    `conda-lock` to narzędzie, które:
    
    - Zapewnia reprodukowalność środowiska,
    - Generuje dokładne wersje wszystkich zależności,
    - Gwarantuje, że środowisko będzie identyczne na różnych maszynach.

Używając `conda-lock`, wygeneruj plik blokady dla środowiska deweloperskiego:

```bash
conda-lock --mamba -f env.yml -f env-dev.yml --lockfile conda-lock-dev.yml
```

A także dla środowiska produkcyjnego:

```bash
conda-lock --mamba -f env.yml --lockfile conda-lock.yml
```

### ✅ Krok 3: Stworzenie środowiska wirtualnego

Stwórz środowisko wirtualne oparte o plik blokady:

```bash
conda-lock install --mamba -n python1course-env conda-lock-dev.yml
```

Aktywuj stworzone środowisko wirtualne:

```bash
# Inicjalizacja mamba w bieżącej sesji (wymagane przy pierwszym użyciu)
eval "$(mamba shell hook --shell bash)"

# Aktywacja środowiska
mamba activate python1course-env
```

!!! tip "Trwała inicjalizacja"
    
    Żeby nie musieć za każdym razem uruchamiać `eval "$(mamba shell hook --shell bash)"`, możesz dodać inicjalizację do swojego profilu:
    
    ```bash
    mamba shell init --shell bash --root-prefix=/opt/conda
    ```
    
    Po tym wystarczy zrestartować terminal i `mamba activate` będzie działać od razu.

### ✅ Krok 4: Weryfikacja środowiska

Zweryfikuj czy zainstalowane zostały wymagane biblioteki:

```bash
# Lista zainstalowanych pakietów
mamba list

# Sprawdzenie wersji Pythona
python --version
```

### ✅ Krok 5: Integracja z Docker i Dev Container (opcjonalny)

!!! question "Po co ten krok?"

    Do tej pory tworzyliśmy środowisko **ręcznie** wewnątrz już uruchomionego kontenera. To działa, ale ma wady:
    
    - Po usunięciu kontenera trzeba tworzyć środowisko od nowa,
    - Każdy członek zespołu musi wykonać te same kroki,
    - Nie ma gwarancji, że wszyscy mają identyczne środowisko.
    
    Integrując tworzenie środowiska z `Dockerfile`, środowisko będzie **automatycznie gotowe** przy każdym uruchomieniu kontenera - bez dodatkowych kroków.

#### Dockerfile

Rozszerz istniejący `Dockerfile` o instalację środowiska z pliku blokady:

```dockerfile
FROM condaforge/miniforge3:latest

WORKDIR /app

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

#### Dev Container (VS Code)

Zmodyfikuj istniejący plik `.devcontainer/devcontainer.json`, dodając ścieżkę do interpretera Pythona w sekcji `settings`:

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
                "terminal.integrated.defaultProfile.linux": "bash",
                "python.defaultInterpreterPath": "/opt/conda/envs/python1course-env/bin/python"
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

!!! tip "Rebuild kontenera"
    
    Po zmianie `Dockerfile` lub plików blokady, należy przebudować kontener:
    
    - VS Code: `Ctrl+Shift+P` → "Dev Containers: Rebuild Container"

## Aktualizacja środowiska wirtualnego

!!! info "Kiedy aktualizować środowisko?"
    
    Aktualizacja jest potrzebna, gdy:

    - Potrzebne są nowe biblioteki,
    - Występują problemy z bezpieczeństwem,
    - Pojawiają się nowe funkcje w bibliotekach,
    - Konieczne są poprawki błędów.

Kolejne kroki, które należy wykonać, żeby zaktualizować środowisko:

1. Zmodyfikuj ręcznie odpowiednio plik `env.yml` lub `env-dev.yml`,
2. Ponownie wygeneruj odpowiedni plik blokady,
3. Aktywuj środowisko bazowe, usuń środowisko wirtualne i stwórz je ponownie na podstawie nowych plików blokady.

!!! warning "Nawet w przypadku pracy w kontenerze, musimy samodzielnie modyfikować pliki z definicją oraz regenerować pliki blokady!"

## Dobre praktyki

1. **Zawsze używaj plików blokady** - gwarantują one reprodukowalność środowiska,
2. **Regularnie aktualizuj zależności** - ale rób to świadomie i testuj zmiany,
3. **Używaj mamba zamiast conda** - szybsze rozwiązywanie zależności,
4. **Dokumentuj zmiany** - szczególnie przy aktualizacji wersji pakietów,
5. **Testuj środowisko** - po każdej większej zmianie w zależnościach.

## Przydatne linki

- [Dokumentacja mamba](https://mamba.readthedocs.io/)
- [Dokumentacja conda-lock](https://conda.github.io/conda-lock/)
- [Miniforge](https://github.com/conda-forge/miniforge)

## 📝 Zadania

W zadaniach symulujemy sytuację, w której w naszym projekcie powstało nowe wymaganie: potrzebujemy biblioteki pandas.

1. Dodaj nową zależność `pandas` do pliku `env.yml`.
2. Wygeneruj ponownie pliki blokady dla środowiska deweloperskiego i produkcyjnego.
3. Usuń poprzednio stworzone środowisko wirtualne i stwórz je ponownie wykorzystując nowe pliki blokady.