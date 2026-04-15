# Pre-commit hooks

Pre-commit to narzędzie, które **blokuje możliwość commitowania zmian**, jeśli nie spełniają one zdefiniowanych wymagań — uruchamia automatyczne sprawdzenia przed każdym `git commit`.

## Czym są git hooks?

Git hooks to skrypty uruchamiane automatycznie w określonych momentach cyklu życia repozytorium. Pre-commit hooks uruchamiają się **przed** zatwierdzeniem zmian, co pozwala wychwycić problemy zanim trafią do historii. W praktyce pomagają z: wyszukiwaniem błędów stylistycznych, egzekwowaniem linterów i formaterów, uruchamianiem testów czy sprawdzaniem składni plików konfiguracyjnych.

## Konfiguracja

Konfiguracja odbywa się przez plik `.pre-commit-config.yaml` w głównym katalogu repozytorium:

```yaml
repos:
  # Hooki ogólne
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
      - id: debug-statements         # Znajduje debugger, pdb, etc.

  # Ruff - linting i formatowanie
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.8
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix]
      - id: ruff-format

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

Po utworzeniu pliku `.pre-commit-config.yaml`, zainstaluj hooki w repozytorium Git:

```bash
pre-commit install
```

!!! info "Co robi `pre-commit install`?"

    Komenda kopiuje hooki do katalogu `.git/hooks/`. Dzięki temu git automatycznie uruchomi je przed każdym commitem.

## Używanie

Po instalacji hooki uruchamiają się **automatycznie** przy każdym `git commit` — także w GUI IDE.

Jeśli któryś hook znajdzie problemy, commit **nie zostanie wykonany** — musisz poprawić błędy i spróbować ponownie.

```bash
# Ręczne uruchomienie na wszystkich plikach (przydatne przy pierwszym użyciu)
pre-commit run --all-files

# Uruchomienie konkretnego hooka
pre-commit run ruff --all-files

# Pominięcie hooków (tylko w wyjątkowych sytuacjach!)
git commit --no-verify -m "Pilna zmiana"
```

## Aktualizacja hooków

```bash
# Zaktualizuj wszystkie hooki do najnowszych wersji
pre-commit autoupdate
```

??? - tip "Rozwiązywanie problemów"

    **Zbyt wiele błędów przy pierwszym uruchomieniu:**
    Uruchom `pre-commit run --all-files` ręcznie, popraw wszystkie błędy, dopiero potem commituj normalnie.

    **Hook nie działa w Dockerze:**
    Upewnij się że `.git` jest zamontowany w kontenerze, lub używaj `pre-commit run` zamiast automatycznych hooków.

??? - tip "Integracja z CI/CD — GitHub Actions"

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

   !!! tip
       Na razie instalujesz `pre-commit` ręcznie. W zadaniach z `make.md` dodasz go do `env-dev.yml`, żeby był instalowany automatycznie razem ze środowiskiem.

2. Utwórz plik `.pre-commit-config.yaml` w głównym katalogu projektu zgodnie z przykładem powyżej.

3. Zainstaluj hooki w repozytorium:
   ```bash
   pre-commit install
   ```

4. Uruchom hooki na wszystkich plikach:
   ```bash
   pre-commit run --all-files
   ```

5. Popraw wszystkie znalezione błędy.

6. Spróbuj zrobić commit — hooki powinny uruchomić się automatycznie:
   ```bash
   git add .
   git commit -m "Test pre-commit"
   ```

7. Sprawdź czy udało się zrobić commit.
