# Formatowanie i analiza kodu

## Python Enhancement Proposals

PEP (Python Enhancement Proposals) to oficjalne dokumenty opisujące **nowe** funkcje, standardy, ulepszenia i procesy w języku Python. PEP-y stanowią podstawowy sposób proponowania zmian w języku i ekosystemie, a także są kluczowym źródłem informacji o standardach kodowania i najlepszych praktykach.

Tworzone są przez członków społeczności, a każdy PEP przechodzi przez **proces** akceptacji, który obejmuje recenzję techniczną, dyskusję w społeczności i finalne zatwierdzenie przez komitet kierujący językiem Python.

**Rodzaje PEPów**:

- **Standardowe** - Proponują zmiany w implementacji Pythona, np. wprowadzenie nowych funkcji, konstrukcji języka czy bibliotek.
- **Informacyjne** - Opisują zasady, dobre praktyki lub procesy bez wymuszania ich implementacji.
- **Procesowe** - Określają procesy zarządzania projektem Python, takie jak rozwój języka czy standardy pracy.

**Wybrane przykłady PEPów**:

- **PEP 8** - styl kodowania

    Definiuje podstawy formatowania kodu w Pythonie. Kluczowe zasady to wcięcia - używanie 4 spacji, nazewnictwo - klasy w stylu `CamelCase`, funkcje i zmienne w stylu `snake_case` czy białe znaki - brak spacji wokół nawiasów, np. `foo(a, b)` zamiast `foo( a, b )`.

- **PEP 257** - docstringi

    Definiuje zasady dokumentowania modułów, klas i funkcji. Każdy moduł, klasa i funkcja powinny mieć docstring. Docstringi powinny być zwięzłe, ale informatywne. Multiliniowe docstringi powinny zaczynać się od podsumowania na pierwszej linii.

- **PEP 484** - typowanie

    Wprowadza adnotacje typów, które pozwalają na precyzyjne określenie typów danych.

**Po co nam PEPy?**

1. Zapewniają jednolite standardy, dzięki czemu kod w Pythonie jest łatwiejszy do czytania i utrzymania.
2. Służą jako przewodnik dla początkujących programistów, wprowadzając ich w dobre praktyki.
3. Dzięki nim Python może rozwijać się w sposób kontrolowany i przemyślany, odpowiadając na potrzeby społeczności.

## Narzędzia do analizy i formatowania kodu

**Linting** to kluczowy element nowoczesnego programowania, który pomaga utrzymać wysoki standard jakości kodu. **Proces** ten automatycznie analizuje kod źródłowy i identyfikuje błędy, problemy stylistyczne oraz potencjalne problemy logiczne. Jego główne cele to zwiększenie czytelności, spójności i niezawodności kodu.

### Zalety lintingu

1. Otrzymujemy lepszej jakości kod.
2. Mamy możliwość wczesnego wykrywania błędów.
3. Zwiększamy produktywność naszego zespołu.
4. W dużych projektach ujednolica styl, co zwiększa łatwość utrzymywania kodu.
5. Eliminujemy problemy stylistyczne.

Na szczęście mamy pod ręką bogaty ekosystem narzędzi do analizy i formatowania, możliwe jest:

1. Automatyczne wykrywanie błędów i niezgodności ze standardami - narzędzia takie jak `flake8`, `pylint`, czy `mypy` pozwalają na szybkie wychwycenie problemów w kodzie.
2. Utrzymywanie spójnego stylu kodowania - narzędzia takie jak `black` i `isort` zapewniają jednolity styl kodu w całym projekcie.
3. Optymalizacja i czystość kodu - analizatory kodu pomagają usuwać redundancje, identyfikować problemy z wydajnością oraz utrzymywać dobre praktyki.

## ruff - nowoczesne narzędzie do analizy i formatowania

Na zajęciach skupimy się na narzędziu **`ruff`**, które integruje funkcjonalności różnych narzędzi w jednym, ekstremalnie szybkim narzędziu napisanym w Rust.

### Dlaczego ruff?

| **Funkcja**                       | **`ruff`**              | **Odpowiadające narzędzie** | **Opis**                                                                        |
| --------------------------------- | ----------------------- | --------------------------- | ------------------------------------------------------------------------------- |
| **Sprawdzanie stylu kodu**        | ✅ Tak                  | `flake8`                    | Sprawdza zgodność ze standardami stylu, takimi jak PEP 8.                       |
| **Sortowanie importów**           | ✅ Tak                  | `isort`                     | Sortuje i grupuje importy według ustalonych zasad.                              |
| **Analiza typów statycznych**     | ✅ Częściowo            | `mypy`                      | Ostrzega o niezgodnych typach w adnotacjach, ale nie zastępuje pełnej analizy.  |
| **Sprawdzanie błędów logicznych** | ✅ Tak                  | `flake8-bugbear`, `pylint`  | Identyfikuje potencjalne błędy w logice programu, np. użycie `==` zamiast `is`. |
| **Formatowanie kodu**             | ✅ Tak (ruff format)    | `black`                     | Formatuje kod zgodnie z PEP 8 i stylem black.                                   |
| **Analiza jakości kodu**          | ✅ Tak                  | `pylint`, `radon`           | Identyfikuje redundancje, długi kod, złożoność funkcji.                         |
| **Wsparcie dla docstringów**      | ✅ Tak                  | `pydocstyle`                | Sprawdza poprawność dokumentacji zgodnie z PEP 257.                             |
| **Wydajność**                     | ✅ Ekstremalnie szybkie |                             | Napisane w Rust, jest wielokrotnie szybsze niż `flake8` czy `pylint`.           |
| **Integracja w CI/CD**            | ✅ Tak                  | Wszystkie                   | Łatwo integruje się z procesami `CI/CD`.                                        |
| **Obsługa konfiguracji**          | ✅ Tak                  | Wszystkie                   | Obsługuje konfigurację w plikach `pyproject.toml`, `ruff.toml`, lub CLI.        |

**Kluczowe zalety ruff:**

1. **Wydajność** - napisane w Rust, jest wielokrotnie szybsze niż `flake8` czy `pylint` (nawet 10-100x).
2. **Wszystko w jednym** - zastępuje `flake8`, `isort`, `black`, `pydocstyle` i wiele innych.
3. **Automatyczne naprawy** - większość problemów można naprawić automatycznie.
4. **Łatwa integracja** - działa z `pre-commit`, CI/CD, IDE.

## Podstawowe użycie ruff

### Sprawdzanie kodu (linting)

```bash
# Sprawdź cały projekt
ruff check .

# Sprawdź konkretny plik
ruff check main.py

# Sprawdź i automatycznie napraw wszystkie możliwe problemy
ruff check --fix .

# Sprawdź i pokaż tylko sugestie (bez automatycznych napraw)
ruff check --output-format=concise .
```

### Formatowanie kodu

```bash
# Sformatuj cały projekt
ruff format .

# Sformatuj konkretny plik
ruff format main.py

# Sprawdź formatowanie bez wprowadzania zmian (dry-run)
ruff format --check .
```

## Konfiguracja ruff

Ruff można skonfigurować w pliku `ruff.toml` (zalecane dla projektów bez pakietów) lub `pyproject.toml`. Oto przykładowa konfiguracja z tylko niezbędnymi ustawieniami:

```toml
# ruff.toml - przykładowa konfiguracja

# Długość linii (domyślnie 88, zgodnie z black)
line-length = 88

# Konfiguracja lintera
[lint]
# Wyłączone reguły (jeśli potrzebne)
ignore = [
    "F403",  # star imports - używane celowo w tym projekcie
    "F405",  # może być niezdefiniowane przez star imports - akceptowalne w tym kontekście
]

# Wybrane reguły do sprawdzania
# Uwaga: E, F, W są domyślnie włączone, ale można je jawnie wymienić dla przejrzystości
select = [
    "E",     # pycodestyle errors - podstawowe błędy stylu
    "W",     # pycodestyle warnings - ostrzeżenia stylistyczne
    "F",     # pyflakes - nieużywane importy, zmienne, etc.
    "I",     # isort - sortowanie i grupowanie importów
    "B",     # flake8-bugbear - wykrywanie typowych błędów
    "C4",    # flake8-comprehensions - optymalizacja list/dict comprehensions
    "UP",    # pyupgrade - modernizacja kodu do nowszych wersji Pythona
    "ARG",   # flake8-unused-arguments - nieużywane argumenty funkcji
    "SIM",   # flake8-simplify - upraszczanie kodu
    "TCH",   # flake8-type-checking - optymalizacja importów typów
    "PTH",   # flake8-use-pathlib - zachęca do używania pathlib zamiast os.path
    "RUF",   # ruff-specific rules - dodatkowe reguły specyficzne dla ruff
]

# Konfiguracja sortowania importów (isort)
[lint.isort]
# Znane biblioteki pierwszej strony (dla grupowania importów)
known-first-party = ["python1course"]

# Konfiguracja type-checking (TCH)
# Reguła TCH sugeruje przeniesienie importów typów do bloku TYPE_CHECKING
# jeśli są używane tylko w adnotacjach typów
[lint.flake8-type-checking]
# Klasy bazowe które są oceniane w runtime (ich importy nie powinny być w TYPE_CHECKING)
# Przykład: jeśli używasz abc.ABC jako klasy bazowej, dodaj tutaj
runtime-evaluated-base-classes = ["abc.ABC"]

# Dekoratory które są oceniane w runtime
# Przykład: jeśli używasz własnych dekoratorów które muszą być dostępne w runtime
runtime-evaluated-decorators = []

```

## Integracja

### Przykładowa integracja z IDE - VS Code

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

### Przykładowa integracja z CI/CD - GitHub Actions

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
        Oczywiście docelowo chcielibyśmy go instalować zawsze w środowisku deweloperskim używając plików definiujących środowisko (`env-dev.yml`).

2. Utwórz plik `ruff.toml` w głównym katalogu projektu i dodaj konfigurację ruff zgodnie z przykładami powyżej.

3. Uruchom `ruff check .` i zobacz jakie problemy znajduje w Twoim kodzie.

4. Uruchom `ruff check --fix .`, aby automatycznie naprawić wszystkie możliwe problemy.

5. Uruchom `ruff format .`, aby sformatować cały kod.

6. (Opcjonalnie) Skonfiguruj ruff w swoim IDE (VS Code lub PyCharm).
