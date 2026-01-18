# Budowanie pakietu

## Plik konfiguracyjny `pyproject.toml`

Plik `pyproject.toml` to centralne miejsce konfiguracji pakietu. Od 2020 roku jest to zalecany (i standardowy) format dla wszystkich narzędzi do budowania pakietów (zgodnie z PEP 517/518/621). Pozwala narzędziom jak `pip`, `build`, `twine` itp. rozpoznać, jak zbudować i zainstalować pakiet.

???- note "Czy można używać czegoś innego niż `pyproject.toml`?"

    Tradycyjnie używano pliku `setup.py`, a następnie dodano `setup.cfg` jako opcję konfiguracji bezpośredniej w formacie `.ini`. Obecnie, możliwa jest konfiguracja pakietów wyłącznie za pomocą `pyproject.toml`, który upraszcza zarządzanie projektami w całym ekosystemie.

    W najnowszych wersjach `setuptools` wystarczy plik `pyproject.toml`, w poprzednich mogą być wymagane inne / pozostałe.

    Więcej szczegółów w dokumentacji [setuptools](https://setuptools.pypa.io/en/latest/index.html).

Plik `pyproject.toml` powinien się znaleźć w głównym katalogu projektu i przechowuje szczegółowe informacje o projekcie, takie jak nazwa, wersja, autor oraz wymagania dla systemu budowania pakietu.

### Podstawowa struktura

```toml
[build-system]
requires = ["setuptools", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "python1course"
version = "0.1.0"
requires-python = ">=3.12"
description = "Pakiet z kodem z zajęć"
authors = [
    { name = "Jakub Staszel", email = "jstaszel@agh.edu.pl" }
]

[tool.setuptools.packages.find]
where = ["."]
include = ["python1course*"]
```

!!! tip "include vs exclude"
    Lepiej używać `include` (whitelist) niż `exclude` (blacklist). Dzięki temu mamy pełną kontrolę nad tym, co trafia do pakietu - nowe foldery nie zostaną przypadkowo dołączone.

### Zależności pakietu

Kiedy budujemy nasz pakiet lokalnie, proces budowy przebiega bez problemów, ponieważ wszystkie zależności są już zainstalowane i dostępne w środowisku. Po zbudowaniu pakietu sytuacja się zmienia - użytkownicy, którzy chcą zainstalować nasz pakiet, nie będą mieli automatycznie dostępu do tych zależności.

Aby upewnić się, że użytkownicy będą mogli poprawnie zainstalować pakiet, konieczne jest określenie zależności runtime w sekcji `dependencies`:

```toml
[project]
name = "moj_pakiet"
version = "1.0.0"
description = "Przykład pakietu"
dependencies = [
    "numpy>=1.21.0,<2.0.0",
    "pandas>=1.3.0",
]
```

### Zależności opcjonalne (deweloperskie)

Można również zdefiniować zależności opcjonalne, np. narzędzia potrzebne tylko podczas developmentu:

```toml
[project.optional-dependencies]
dev = [
    "pytest",
    "ruff",
    "pre-commit",
    "build",
]
```

Instalacja z zależnościami deweloperskimi: `pip install -e ".[dev]"`

## Instalacja pakietu w trybie edytowalnym

Żeby mieć możliwość pracy na lokalnym pakiecie (ale już wersji zdefiniowanej w `pyproject.toml` oraz bez uzależnienia od `sys.path`), wystarczy zainstalować pakiet w trybie edytowalnym za pomocą `pip install -e .` w terminalu. Tworzone zmiany będą widoczne od razu w Pythonie bez konieczności reinstalacji pakietu.

```bash
pip install -e .
```

???+ danger "To nie jest jedyna dopuszczalna struktura projektu!"

    Aktualnie `setuptools` wspiera automatyczne przeszukiwanie 2 typów struktur projektów, na tych zajęciach stworzyliśmy `flat-layout`, więcej szczegółów [tutaj](https://setuptools.pypa.io/en/latest/userguide/package_discovery.html#automatic-discovery).

## Wersjonowanie pakietów

Wersjonowanie to proces przypisywania numeru wersji do konkretnego stanu projektu. Dzięki temu możemy jasno komunikować co się zmieniło, czy wersja jest stabilna oraz czy aktualizacja wpływa na kompatybilność z innymi projektami.

Dobrze prowadzone wersjonowanie pozwala zachować porządek w historii zmian, umożliwić instalację konkretnej wersji (np. do testów) i uniknąć błędów wynikających z nieoczekiwanych zmian w kodzie.

### Konwencja SemVer (Semantic Versioning)

Najczęściej stosowaną konwencją jest [Semantic Versioning](https://semver.org/), czyli:

```
MAJOR.MINOR.PATCH
```

Szczegóły:

- **MAJOR** (np. 2.0.0) - zmiany niekompatybilne z poprzednimi wersjami (np. usunięcie lub zmiana działania funkcji),
- **MINOR** (np. 1.3.0) - nowe funkcje, ale kompatybilne z poprzednimi wersjami.
- **PATCH** (np. 1.3.2) - poprawki błędów, bez dodawania nowych funkcji.

Przykład rozwoju wersji:

- 0.1.0 - wstępna wersja rozwojowa,
- 0.2.0 - dodano nowe funkcje,
- 0.2.1 - poprawki błędów,
- 1.0.0 - pierwsza stabilna wersja,
- 2.0.0 - duża zmiana, niekompatybilna z 1.x.

## Proces budowania pakietu

Budowanie pakietu to proces tworzenia dystrybucji kodu, która może być zainstalowana przez innych użytkowników lub na różnych maszynach. W Pythonie używa się narzędzi takich jak `setuptools` i `wheel`, aby stworzyć gotową paczkę w formatach `.whl` (wheel) i `.tar.gz` (source distribution).

Do budowania pakietu służy narzędzie `build` ([dokumentacja](https://build.pypa.io/en/stable/)):

```bash
python -m build
```

!!! warning "Paczka zbudowana zostanie w wersji zgodnej z `pyproject.toml`, należy najpierw zaktualizować ten plik!"

Pliki powinny zostać stworzone w folderze `dist`:

- `.tar.gz` - klasyczna paczka źródłowa,
- `.whl` (wheel) - zoptymalizowana binarna paczka do szybkiej instalacji.

???- question "Co to jest `wheel`?"

    Wheel to nowoczesny format paczek dla Pythona (rozszerzenie .whl).

    Zalety:

    - szybka instalacja bez potrzeby kompilacji,
    - lepsze wsparcie dla CI/CD i instalacji zależności systemowych,
    - obsługa zależności i metadanych.

    Instalacja pliku `.whl`:

    ```bash
    pip install dist/python1course-0.1.0-py3-none-any.whl
    ```

## Integracja z pyproject.toml

Plik `pyproject.toml` może służyć jako **jedno źródło prawdy** dla całego projektu. Oprócz konfiguracji pakietu, można w nim umieścić konfigurację innych narzędzi, takich jak `ruff`, `pytest`, czy `mypy`.

### Konfiguracja ruff w pyproject.toml

Zamiast trzymać konfigurację w osobnym pliku `ruff.toml`, można przenieść ją do `pyproject.toml` używając prefiksu `[tool.ruff]`:

```toml
# Konfiguracja ruff (zamiast osobnego ruff.toml)
[tool.ruff]
line-length = 88

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "C4", "UP", "ARG", "SIM", "TCH", "PTH", "RUF"]
ignore = ["F403", "F405"]

[tool.ruff.lint.isort]
known-first-party = ["python1course"]
```

### Kompletny przykład pyproject.toml

Poniżej znajduje się przykład kompletnego pliku `pyproject.toml` łączącego konfigurację pakietu z konfiguracją narzędzi:

```toml
[build-system]
requires = ["setuptools", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "python1course"
version = "0.1.0"
requires-python = ">=3.12"
description = "Pakiet z kodem z zajęć Python 1"
authors = [
    { name = "Twoje Imię", email = "twoj@email.pl" }
]
dependencies = [
    "numpy",
]

[project.optional-dependencies]
dev = [
    "pytest",
    "ruff",
    "pre-commit",
    "build",
]

[tool.setuptools.packages.find]
where = ["."]
include = ["python1course*"]

# Konfiguracja ruff
[tool.ruff]
line-length = 88

[tool.ruff.lint]
select = ["E", "W", "F", "I", "B", "C4", "UP", "ARG", "SIM", "TCH", "PTH", "RUF"]
ignore = ["F403", "F405"]

[tool.ruff.lint.isort]
known-first-party = ["python1course"]
```

## Integracja z Makefile

Do automatyzacji procesu budowania można rozszerzyć istniejący `Makefile` o targety związane z pakietami. Poniżej znajdują się nowe targety do dodania:

```makefile
# Budowanie pakietu
.PHONY: build
build:
	$(PYTHON) -m build

# Instalacja pakietu w trybie edytowalnym
.PHONY: install-editable
install-editable:
	pip install -e .

# Pełny proces przed publikacją
.PHONY: pre-release
pre-release: lint format test build
	@echo "Gotowe do publikacji!"
```

Należy również zaktualizować sekcję `help` oraz target `clean`:

```makefile
# W sekcji help dodaj:
@echo "  make build            - Zbuduj pakiet (dist/)"
@echo "  make install-editable - Zainstaluj pakiet w trybie edytowalnym"
@echo "  make pre-release      - Pełny proces przed publikacją"
```

Target `pre-release` łączy wszystkie kroki weryfikacji (linting, formatowanie, testy) z budowaniem pakietu w jedną komendę - idealny do uruchomienia przed publikacją nowej wersji.

## 📝 Zadania

1. Stwórz w głównym katalogu projektu plik `pyproject.toml`:
    - Wypełnij podstawowe informacje (nazwa, wersja, autor, opis)
    - Dodaj zależności runtime (np. `numpy`)
    - Przenieś konfigurację z `ruff.toml` do sekcji `[tool.ruff]`
    - Po przeniesieniu usuń plik `ruff.toml`

2. Zainstaluj pakiet w trybie edytowalnym i zweryfikuj, że działa:

    ```bash
    pip install -e .
    python -c "from python1course import *"
    ```

3. Zaktualizuj `env-dev.yml` - dodaj `build` jako zależność deweloperską (poprzez pip):

    ```yaml
    dependencies:
      # ... istniejące zależności ...
      - pip
      - pip:
        - build
    ```

4. Przebuduj środowisko i zweryfikuj, że `build` jest dostępne:

    ```bash
    make recreate-env
    python -m build --help
    ```

5. Rozszerz `Makefile` o nowe targety: `build`, `install-editable`, `pre-release`. Zaktualizuj też sekcję `help`.

6. Zbuduj pakiet i sprawdź zawartość folderu `dist/`:

    ```bash
    make build
    ls dist/
    ```
