# Publikowanie pakietu

Publikacja pakietu to kluczowy krok w procesie udostępnienia oprogramowania innym użytkownikom, zespołom lub całej społeczności. Udostępniając pakiet, umożliwiamy jego łatwą **instalację, aktualizację i wykorzystanie w innych projektach**. Proces publikacji zależy od repozytorium pakietów, na którym chcemy opublikować pakiet, a także od tego, czy nasz pakiet jest przeznaczony do użytku prywatnego, publicznego, czy specjalistycznego.

## Platformy do dystrybucji pakietów

### [PyPI](https://pypi.org/)

Jest domyślną i najczęściej używaną platformą. Pozwala na łatwą instalację pakietów za pomocą `pip` oraz ich aktualizację.

Instalacja dostępnego tam pakietu: `pip install <nazwa_pakietu>`.

### [conda-forge](https://conda-forge.org/)

Platforma do dystrybucji pakietów oparta na systemie Conda, który wspiera pakiety Python i nie tylko. Conda-Forge to społecznościowe repozytorium, które umożliwia tworzenie i publikowanie pakietów w szerokiej gamie języków programowania.

Instalacja dostępnego tam pakietu: `conda install -c conda-forge <nazwa_pakietu>`.

Wymaga Conda do zarządzania.

### [GitHub Packages](https://docs.github.com/en/packages/learn-github-packages/introduction-to-github-packages)

Platforma dystrybucji zintegrowana bezpośrednio z GitHub, co umożliwia publikację pakietów obok kodu źródłowego i integrację z GitHub Actions.

Instalacja dostępnego tam pakietu: w zależności od konfiguracji `pip install` lub `conda install`.

### Inne

- Anaconda Cloud
- Artifactory
- PyPI Pro
- Docker Hub

## Weryfikacja przed publikacją

Przed opublikowaniem pakietu ważne jest upewnienie się, że kod jest wysokiej jakości. Kluczowe elementy do sprawdzenia:

- **Linting i formatowanie** - kod powinien być zgodny z ustalonymi standardami (`ruff check`, `ruff format --check`)
- **Testy** - wszystkie testy powinny przechodzić (`pytest`)
- **Konfiguracja pakietu** - plik `pyproject.toml` powinien być poprawny i zawierać aktualne informacje
- **Wersja** - numer wersji w `pyproject.toml` powinien być zaktualizowany i odpowiadać tagowi git

Target `make pre-release` (dodany w poprzedniej części) automatyzuje te kroki, wykonując linting, formatowanie, testy i budowanie pakietu w jednej komendzie.

## Git tags

Git tagi to specjalne "etykiety" przypinane do konkretnych commitów, najczęściej służące do oznaczania wersji projektu (np. v1.0.0, v2.1.3). W odróżnieniu od branchy, tagi są niezmienne - raz przypisane do commita, pozostają z nim związane na stałe.

Tagi są kluczowe w cyklu wydawniczym:

- pozwalają budować i publikować wersje paczek,
- umożliwiają tworzenie release'ów na GitHubie,
- pozwalają odtwarzać stan repozytorium z momentu konkretnego wydania.

Przykład dodania taga:

```bash
git tag v0.1.0
git push origin v0.1.0
```

!!! warning "Wersja w tagu musi odpowiadać wersji w `pyproject.toml`"
    Tag `v0.1.0` powinien odpowiadać `version = "0.1.0"` w `pyproject.toml`.

## Publikowanie manualne vs automatyczne

### Manualne

Publikować pakiety można ręcznie za pomocą interfejsu GitHub (zakładka Releases). GitHub zadba o przechowanie plików, ale wymaga to ręcznego budowania i przesyłania paczek.

!!! danger "Na zajęciach używamy wersji automatycznej!"

### Automatyczne (GitHub Actions)

Lepszym rozwiązaniem jest automatyzacja za pomocą GitHub Actions. Workflow uruchamia się automatycznie po wypchnięciu taga i wykonuje wszystkie kroki: weryfikację jakości, budowanie pakietu i tworzenie release'u.

## Workflow do automatycznej publikacji

Poniżej znajduje się przykładowy workflow, który:

1. Uruchamia się automatycznie po wypchnięciu taga zaczynającego się od `v`
2. Weryfikuje zgodność wersji między tagiem a `pyproject.toml`
3. Sprawdza linting, formatowanie i uruchamia testy
4. Buduje pakiet
5. Tworzy release na GitHubie z plikami `.whl` i `.tar.gz`

```yaml
name: Build and Release

on:
  push:
    tags:
      - 'v*'

jobs:
  build-and-release:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Check version matches tag
        run: |
          PYPROJECT_VERSION=$(grep '^version' pyproject.toml | sed 's/version = //' | tr -d '" ')
          TAG_VERSION=${GITHUB_REF#refs/tags/v}

          echo "Version in pyproject.toml: $PYPROJECT_VERSION"
          echo "Version from tag: $TAG_VERSION"

          if [ "$PYPROJECT_VERSION" != "$TAG_VERSION" ]; then
            echo "❌ Version mismatch! Tag is v$TAG_VERSION, but pyproject.toml has $PYPROJECT_VERSION"
            exit 1
          else
            echo "✅ Version matches."
          fi

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install build ruff pytest

      - name: Run linting
        run: ruff check .

      - name: Run format check
        run: ruff format --check .

      - name: Run tests
        run: pytest tests/ -v

      - name: Build the package
        run: python -m build

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          name: Release ${{ github.ref_name }}
          tag_name: ${{ github.ref_name }}
          files: dist/*
          generate_release_notes: true
```

Po wykonaniu workflow na stronie repozytorium widoczny będzie status oraz gotowy release z plikami do pobrania - widoczny w zakładce **Actions** (status workflow) oraz **Releases** (gotowe pliki `.whl` i `.tar.gz`).

## Instalacja opublikowanego pakietu

Po opublikowaniu pakietu inni użytkownicy mogą go zainstalować bezpośrednio z GitHub:

```bash
pip install https://github.com/<organizacja>/<repo>/releases/download/v0.1.0/python1course-0.1.0-py3-none-any.whl
```

Dla pakietów opublikowanych na PyPI instalacja jest prostsza:

```bash
pip install <nazwa_pakietu>
```

## 📝 Zadania

1. Stwórz plik `.github/workflows/release.yml` z zawartością workflow przedstawionego powyżej.

2. Uruchom weryfikację przed publikacją:

    ```bash
    make pre-release
    ```

3. Zaktualizuj wersję w `pyproject.toml` jeśli to konieczne (powinna być `0.1.0`).

4. Zatwierdź zmiany i wypchnij na główną gałąź:

    ```bash
    git add .
    git commit -m "feat: przygotowanie pakietu do publikacji"
    git push origin main
    ```

5. Dodaj tag i wypchnij go, aby uruchomić workflow:

    ```bash
    git tag v0.1.0
    git push origin v0.1.0
    ```

6. Sprawdź na GitHubie:
    - Czy workflow wykonał się poprawnie (zakładka Actions)
    - Czy release pojawił się (zakładka Releases)

7. (Opcjonalnie) Zainstaluj opublikowany pakiet w nowym środowisku i zweryfikuj, że działa:

    ```bash
    pip install https://github.com/<twoja-organizacja>/<twoje-repo>/releases/download/v0.1.0/python1course-0.1.0-py3-none-any.whl
    python -c "from python1course import *"
    ```
