# Środowiska wirtualne w Pythonie

Środowiska wirtualne to odizolowane przestrzenie, w których każdy projekt ma własne zależności - bez konfliktów z innymi projektami ani z systemowym Pythonem. Dzięki plikom konfiguracyjnym (`env.yml`, `pyproject.toml`, `requirements.txt`) środowisko można odtworzyć identycznie na innej maszynie lub w całym zespole.

!!! success "Dlaczego mamba + conda-lock na tych zajęciach?"

    Na zajęciach praktycznych będziemy używać kombinacji **mamba + conda-lock**, ponieważ:

    - **Mamba** jest znacznie szybsza niż klasyczna conda (nawet 10x),
    - **conda-lock** zapewnia pełną reprodukowalność środowiska na różnych systemach operacyjnych,
    - Ekosystem **conda-forge** oferuje gotowe pakiety naukowe i inżynierskie bez konieczności kompilacji,
    - To sprawdzony zestaw w projektach data science i inżynierskich.

## Narzędzia

### [venv](https://docs.python.org/3/library/venv.html)

Wbudowany pakiet Pythona. Zależnościami zarządza się ręcznie przez `pip` i `requirements.txt`.

Najlepszy wybór dla **szybkiego i lekkiego rozwiązania** bez dodatkowych narzędzi, np. do małych projektów lub nauki.

### [pipenv](https://pipenv.pypa.io/en/latest/)

Łączy `pip` z `venv` - zarządza zależnościami przez `Pipfile` i `Pipfile.lock`.

Najlepszy wybór dla średnich projektów z **prostą automatyzacją środowiska**.

### [poetry](https://python-poetry.org/docs/)

Nowoczesne narzędzie do zarządzania zależnościami, wersjonowania i publikacji paczek. Bazuje na `pyproject.toml` i generuje `poetry.lock`.

Najlepszy wybór dla **profesjonalnych projektów** z precyzyjną kontrolą wersji i publikowaniem bibliotek.

### conda / [miniconda](https://docs.anaconda.com/miniconda/)

Menedżer pakietów i środowisk działający niezależnie od `pip` - instaluje też biblioteki niskopoziomowe (C/C++).

Najlepszy wybór w projektach **data science i obliczeniach naukowych** wymagających pakietów spoza PyPI.

[conda-lock](https://conda.github.io/conda-lock/) - rozszerzenie generujące pliki blokujące (`conda-lock.yml`) dla pełnej reprodukowalności na różnych systemach.

[mamba](https://mamba.readthedocs.io/) - wydajniejsza alternatywa dla `conda` (implementacja w C++), w pełni z nią kompatybilna.

### [pixi](https://pixi.sh/)

Narzędzie od twórców `mamba` napisane w Rust. Łączy `conda`, `mamba` i `conda-lock` w jednym miejscu - plik `pixi.toml` z automatycznymi lock files i obsługą zadań podobną do `npm`.

Najlepszy wybór dla nowych projektów łączących **conda-forge z PyPI** w jednym narzędziu.

### [uv](https://docs.astral.sh/uv/)

Nowoczesne, lekkie narzędzie skoncentrowane na `pyproject.toml` - łączy instalację pakietów, rozwiązywanie zależności i cache'owanie.

Najlepszy wybór dla **zaawansowanych użytkowników** chcących maksymalnej szybkości.

## Porównanie narzędzi

| Narzędzie | Plik konfiguracyjny | Lock file | Pakiety spoza PyPI | Szybkość |
|-----------|---------------------|-----------|-------------------|----------|
| venv + pip | `requirements.txt` | ❌ (ręcznie) | ❌ | ⭐⭐ |
| pipenv | `Pipfile` | ✅ `Pipfile.lock` | ❌ | ⭐⭐ |
| poetry | `pyproject.toml` | ✅ `poetry.lock` | ❌ | ⭐⭐⭐ |
| conda | `environment.yml` | ❌ (+ conda-lock) | ✅ | ⭐ |
| mamba | `environment.yml` | ❌ (+ conda-lock) | ✅ | ⭐⭐⭐ |
| pixi | `pixi.toml` | ✅ `pixi.lock` | ✅ | ⭐⭐⭐⭐ |
| uv | `pyproject.toml` | ✅ `uv.lock` | ❌ | ⭐⭐⭐⭐⭐ |
