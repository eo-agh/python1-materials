# Pre-commit hooks

Pre-commit to narzędzie, które ułatwia pracę poprzez agregację różnych funkcjonalności, a przy okazji **blokuje możliwość przesyłania zmian do repozytorium**, jeśli nie spełniają one narzuconych wymagań.

## Czym są git hooks?

Git hooks to skrypty, które są automatycznie uruchamiane w określonych momentach cyklu życia repozytorium git. Pre-commit hooks uruchamiają się **przed** zatwierdzeniem zmian (commit), co pozwala na automatyczne sprawdzenie kodu przed jego zapisaniem w historii.

### Zalety pre-commit hooks

W zależności od tego jak zostanie skonfigurowany, może nam pomagać z:

1. **Wyszukiwaniem błędów przed zatwierdzeniem kodu** (`commit`) - np. zatwierdzanie kodu, który nie spełnia standardów stylu, importowanie nieużywanych bibliotek, niedokończone fragmenty kodu czy konflikty w merge'ach.
2. **Powtarzalnymi procesami** - np. manualnym sprawdzaniem stylu, uruchamianiem testów czy usuwaniem plików tymczasowych.
3. **Zapewnieniem spójności w kodzie od różnych deweloperów** - np. różni członkowie zespołu mogą korzystać z różnych standardów.
4. **Wymaganiami projektowymi** - wymusza stosowanie linterów, testów czy innych narzędzi.

## Konfiguracja pre-commit

Konfiguracja `pre-commit` odbywa się poprzez plik `.pre-commit-config.yaml` (zwykle w głównym katalogu repozytorium). Ten plik definiuje, jakie zadania (`hooks`) mają być uruchamiane przed commitowaniem.

### Przykładowa konfiguracja

Oto kompletny przykład `.pre-commit-config.yaml` z popularnymi hookami:

```yaml
# Wersja konfiguracji pre-commit
repos:
  # Hooki ogólne (usuwanie białych znaków, sprawdzanie YAML, etc.)
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v6.0.0
    hooks:
      - id: trailing-whitespace      # Usuwa białe znaki na końcu linii
      - id: end-of-file-fixer        # Dodaje pustą linię na końcu pliku
      - id: check-yaml               # Sprawdza składnię YAML
      - id: check-json               # Sprawdza składnię JSON
      - id: check-toml               # Sprawdza składnię TOML
      - id: check-added-large-files  # Ostrzega o dużych plikach
      - id: check-merge-conflict     # Sprawdza konflikty merge
      - id: check-case-conflict      # Sprawdza konflikty nazw plików
      - id: debug-statements         # Znajduje debugger, pdb, etc.
      - id: mixed-line-ending        # Sprawdza mieszane końce linii

  # Ruff - linting i formatowanie
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.8
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]
      - id: ruff-format

  # (Opcjonalnie) Black - jeśli nie używasz ruff format
  # - repo: https://github.com/psf/black
  #   rev: 24.1.0
  #   hooks:
  #     - id: black
  #       language_version: python3.11

  # (Opcjonalnie) isort - jeśli nie używasz ruff do sortowania importów
  # - repo: https://github.com/pycqa/isort
  #   rev: 5.13.2
  #   hooks:
  #     - id: isort
  #       args: [--profile=black]

# mypy - analiza typów statycznych
  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        args: [--explicit-package-bases]

# pytest - uruchamianie testów
  - repo: local
    hooks:
      - id: pytest
        name: pytest
        entry: pytest
        language: system
        pass_filenames: false
        always_run: true
        args: [--tb=short]

```

## Instalacja hooków

Po utworzeniu pliku `.pre-commit-config.yaml`, należy zainstalować hooki w repozytorium Git:

```bash
# Zainstaluj hooki w repozytorium
pre-commit install
```

!!! info "Co robi `pre-commit install`?"

    Komenda ta kopiuje hooki z `.pre-commit-config.yaml` do katalogu `.git/hooks/` w Twoim repozytorium. Dzięki temu git automatycznie uruchomi te hooki przed każdym commitem.

## Używanie pre-commit

### Automatyczne uruchamianie

Po instalacji, hooki uruchamiają się **automatycznie** przy każdym `git commit`:

```bash
git add .
git commit -m "Moja zmiana"
# Pre-commit automatycznie uruchomi wszystkie hooki
```

!!! warning "Także w GUI naszego IDE!"

Jeśli któryś z hooków znajdzie problemy, commit **nie zostanie wykonany** - musisz poprawić błędy i spróbować ponownie.

### Ręczne uruchamianie

Możesz również uruchomić hooki ręcznie:

```bash
# Uruchom wszystkie hooki na wszystkich plikach
pre-commit run --all-files

# Uruchom wszystkie hooki tylko na staged files (domyślnie)
pre-commit run

# Uruchom konkretny hook
pre-commit run ruff --all-files
```

### Pomijanie hooków (w razie potrzeby)

Jeśli musisz pominąć hooki (np. w sytuacji awaryjnej):

```bash
# Pomiń wszystkie hooki
git commit --no-verify -m "Pilna zmiana"

# LUB użyj skrótu
git commit -n -m "Pilna zmiana"
```

!!! warning "Ostrzeżenie"

    Pomijanie hooków powinno być **rzadkością**! Hooki są po to, żeby zapewnić jakość kodu. Jeśli często musisz je pomijać, może warto poprawić konfigurację.

## Aktualizacja hooków

Hooki są aktualizowane automatycznie, ale możesz je zaktualizować ręcznie:

```bash
# Zaktualizuj wszystkie hooki do najnowszych wersji
pre-commit autoupdate

# Zaktualizuj tylko konkretny hook
pre-commit autoupdate --repo https://github.com/astral-sh/ruff-pre-commit
```

## Wyłączanie pre-commit

Jeśli chcesz tymczasowo wyłączyć pre-commit:

```bash
# Odinstaluj hooki
pre-commit uninstall

# Później możesz je ponownie zainstalować
pre-commit install
```

## Popularne hooki

### Ruff (linting i formatowanie)

```yaml
- repo: https://github.com/astral-sh/ruff-pre-commit
  rev: v0.1.8
  hooks:
    - id: ruff
      args: [--fix, --exit-non-zero-on-fix]
    - id: ruff-format
```

### Black (formatowanie)

```yaml
- repo: https://github.com/psf/black
  rev: 24.1.0
  hooks:
    - id: black
      language_version: python3.11
```

### isort (sortowanie importów)

```yaml
- repo: https://github.com/pycqa/isort
  rev: 5.13.2
  hooks:
    - id: isort
      args: [--profile=black]
```

### mypy (analiza typów)

```yaml
- repo: https://github.com/pre-commit/mirrors-mypy
  rev: v1.8.0
  hooks:
    - id: mypy
      additional_dependencies: [types-all]
      args: [--ignore-missing-imports]
```

### pytest (testy)

```yaml
- repo: local
  hooks:
    - id: pytest
      name: pytest
      entry: pytest
      language: system
      pass_filenames: false
      always_run: true
```

## Przykład workflow

1. **Tworzenie pliku konfiguracyjnego:**
   ```bash
   # Utwórz .pre-commit-config.yaml w głównym katalogu projektu
   ```

2. **Instalacja hooków:**
   ```bash
   pre-commit install
   ```

3. **Testowanie:**
   ```bash
   # Uruchom na wszystkich plikach (pierwszy raz)
   pre-commit run --all-files
   ```

4. **Normalna praca:**
   ```bash
   git add .
   git commit -m "Moja zmiana"
   # Hooki uruchomią się automatycznie
   ```

## Rozwiązywanie problemów

### Problem: Zbyt wiele błędów przy pierwszym uruchomieniu

**Rozwiązanie:** 
1. Uruchom `pre-commit run --all-files` ręcznie
2. Popraw wszystkie błędy
3. Dopiero potem zacznij normalnie commitować

### Problem: Hook blokuje commit, ale chcę go pominąć

**Rozwiązanie:** Użyj `git commit --no-verify` (ale tylko w wyjątkowych sytuacjach!).

### Problem: Hook nie działa w Dockerze

**Rozwiązanie:** Upewnij się, że `.git` jest zamontowany w kontenerze, lub użyj `pre-commit run` zamiast automatycznych hooków.

## Przykładowa integracja z CI/CD

Możesz również uruchamiać pre-commit w CI/CD (np. GitHub Actions):

```yaml
name: Pre-commit

on: [push, pull_request]

jobs:
  pre-commit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - name: Install pre-commit
        run: pip install pre-commit
      - name: Run pre-commit
        run: pre-commit run --all-files
```

## 📝 Zadania

1. Zainstaluj `pre-commit` w swoim środowisku wirtualnym:
   ```bash
   mamba install -c conda-forge pre-commit
   ```

2. Utwórz plik `.pre-commit-config.yaml` w głównym katalogu projektu zgodnie z przykładem u góry.

3. Zainstaluj hooki w repozytorium:
   ```bash
   pre-commit install
   ```

4. Uruchom hooki na wszystkich plikach:
   ```bash
   pre-commit run --all-files
   ```

5. Popraw wszystkie znalezione błędy.

6. Spróbuj zrobić commit - hooki powinny uruchomić się automatycznie:
   ```bash
   git add .
   git commit -m "Test pre-commit"
   ```

7. Sprawdź czy udało się zrobić commit.
