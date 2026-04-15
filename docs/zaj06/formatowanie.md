# Formatowanie i analiza kodu

## Python Enhancement Proposals

PEP-y to oficjalne dokumenty opisujące standardy i ulepszenia Pythona. Trzy najważniejsze dla codziennego pisania kodu:

- **PEP 8** — styl kodowania: 4 spacje wcięcia, `snake_case` dla funkcji i zmiennych, `CamelCase` dla klas.
- **PEP 257** — docstringi: każdy moduł, klasa i funkcja powinny mieć krótki docstring.
- **PEP 484** — adnotacje typów.

## ruff

Na zajęciach skupimy się na narzędziu **`ruff`**, które integruje funkcjonalności wielu narzędzi w jednym, ekstremalnie szybkim narzędziu napisanym w Rust.

| **Funkcja** | **`ruff`** | **Odpowiadające narzędzie** |
| --- | --- | --- |
| Sprawdzanie stylu kodu | ✅ | `flake8` |
| Sortowanie importów | ✅ | `isort` |
| Analiza typów statycznych | ✅ Częściowo | `mypy` |
| Sprawdzanie błędów logicznych | ✅ | `flake8-bugbear`, `pylint` |
| Formatowanie kodu | ✅ (`ruff format`) | `black` |
| Wsparcie dla docstringów | ✅ | `pydocstyle` |
| Wydajność | ✅ Ekstremalnie szybkie | — napisane w Rust |

## Podstawowe użycie

### Linting

```bash
# Sprawdź cały projekt
ruff check .

# Sprawdź i automatycznie napraw problemy
ruff check --fix .
```

### Formatowanie

```bash
# Sformatuj cały projekt
ruff format .

# Sprawdź formatowanie bez wprowadzania zmian (dry-run)
ruff format --check .
```

## Konfiguracja

Ruff można skonfigurować w pliku `ruff.toml` lub `pyproject.toml`:

```toml
# ruff.toml
line-length = 88

[lint]
ignore = [
    "F403",  # star imports
    "F405",
]

select = [
    "E",     # pycodestyle errors
    "W",     # pycodestyle warnings
    "F",     # pyflakes - nieużywane importy, zmienne
    "I",     # isort - sortowanie importów
    "B",     # flake8-bugbear - typowe błędy
    "C4",    # flake8-comprehensions
    "UP",    # pyupgrade - modernizacja kodu
    "ARG",   # nieużywane argumenty funkcji
    "SIM",   # flake8-simplify
    "PTH",   # pathlib zamiast os.path
    "RUF",   # ruff-specific rules
]

[lint.isort]
known-first-party = ["python1course"]

[lint.flake8-type-checking]
runtime-evaluated-base-classes = ["abc.ABC"]
```

## Integracja z IDE

Dodaj do `.vscode/settings.json`:

```json
{
    "[python]": {
        "editor.defaultFormatter": "charliermarsh.ruff",
        "editor.formatOnSave": true,
        "editor.codeActionsOnSave": {
            "source.fixAll": "explicit",
            "source.organizeImports": "explicit"
        }
    }
}
```

??? - tip "Integracja z CI/CD — GitHub Actions"

    ```yaml
    name: Lint and Format

    on: [push, pull_request]

    jobs:
      ruff:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v3
          - uses: actions/setup-python@v4
            with:
              python-version: '3.11'
          - name: Install ruff
            run: pip install ruff
          - name: Run ruff check
            run: ruff check .
          - name: Run ruff format check
            run: ruff format --check .
    ```

## 📝 Zadania

1. Zainstaluj `ruff` w swoim środowisku wirtualnym:

    ```bash
    mamba install -c conda-forge ruff
    ```

    !!! tip
        Na razie instalujesz `ruff` ręcznie. W zadaniach z `make.md` dodasz go do `env-dev.yml`, żeby był instalowany automatycznie razem ze środowiskiem.

2. Utwórz plik `ruff.toml` w głównym katalogu projektu i dodaj konfigurację zgodnie z przykładem powyżej.

3. Uruchom `ruff check .` i zobacz jakie problemy znajduje w Twoim kodzie.

4. Uruchom `ruff check --fix .`, aby automatycznie naprawić wszystkie możliwe problemy.

5. Uruchom `ruff format .`, aby sformatować cały kod.

6. (Opcjonalnie) Skonfiguruj ruff w swoim IDE (VS Code lub PyCharm).
