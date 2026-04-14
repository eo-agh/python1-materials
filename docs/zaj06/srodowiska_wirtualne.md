# Środowiska wirtualne w Pythonie

## Wprowadzenie

Środowiska wirtualne to fundamentalne narzędzie w ekosystemie Pythona, które rozwiązuje kluczowe problemy związane z zarządzaniem zależnościami w projektach. Umożliwiają tworzenie odizolowanych przestrzeni, w których można zarządzać zależnościami specyficznymi dla danego projektu. Ich zastosowanie znacząco upraszcza rozwój, testowanie i wdrażanie oprogramowania.

### Izolacja zależności

Każdy projekt może wymagać innych wersji bibliotek. Instalując wszystko globalnie, narażamy się na konflikty między wersjami, co prowadzi do trudnych do zdiagnozowania błędów.

Środowiska wirtualne pozwalają:

- Oddzielić zależności poszczególnych projektów,
- Zainstalować tylko te biblioteki, które są faktycznie potrzebne,
- Uniknąć konfliktów między wersjami.

### Zarządzanie zależnościami

Dzięki plikom takim jak `requirements.txt`, `env.yml`, czy `pyproject.toml`, możemy:

- Zapisać wszystkie aktualne zależności,
- Łatwo je zaktualizować lub odtworzyć,
- Mieć pełną kontrolę nad wersjami bibliotek.

### Spójność środowiska w zespole

Wielu programistów pracujących na różnych maszynach i systemach może bez problemu korzystać z tych samych zależności, jeśli środowisko jest zdefiniowane w plikach konfiguracyjnych.

To eliminuje problemy wynikające z niezgodnych konfiguracji lokalnych i ułatwia wdrażanie nowych członków zespołu.

### Reprodukowalność i deployment

Wdrożenie projektu na nową maszynę, środowisko lub serwer produkcyjny może być trudne, jeśli środowisko Pythona nie jest jednoznacznie zdefiniowane. Środowiska wirtualne pozwalają na:

- Szybkie odtworzenie identycznej konfiguracji,
- Zmniejszenie ryzyka błędów wdrożeniowych,
- Łatwiejsze testowanie i debugowanie.

### Pozostałe

??? - tip "Pozostałe korzyści"

    **Testowanie nowych wersji**
    
    Chcesz przetestować nową wersję biblioteki, ale boisz się, że coś zepsujesz? W środowisku wirtualnym możesz:

    - Utworzyć oddzielne środowisko testowe,
    - Sprawdzić kompatybilność nowej wersji,
    - Bezpiecznie wprowadzać zmiany bez wpływu na inne projekty.


    **Bezpieczeństwo**

    Instalując pakiety tylko w obrębie wirtualnego środowiska, ograniczamy ich wpływ na resztę systemu. W razie wykrycia problematycznego pakietu wystarczy usunąć środowisko - bez ryzyka uszkodzenia systemowego Pythona czy innych projektów.

    **Przejrzystość i łatwe czyszczenie**

    Środowiska wirtualne dają jasny obraz tego, co dokładnie jest zainstalowane. A jeśli coś pójdzie nie tak - możesz po prostu usunąć środowisko i utworzyć je od nowa. Bez bałaganu i bez utraty globalnych ustawień.

    **Ochrona głównej instalacji Pythona**

    Instalując biblioteki globalnie, łatwo uszkodzić środowisko systemowe. Środowiska wirtualne pozwalają uniknąć tej sytuacji - główny Python pozostaje „czysty”, a wszystkie zmiany ograniczają się do wirtualnego środowiska.

### Podsumowanie

Środowiska wirtualne to proste, ale niezwykle skuteczne narzędzie:

- Chronią główną instalację Pythona,

- Pozwalają utrzymać porządek w zależnościach,

- Ułatwiają współpracę i wdrażanie aplikacji,

- Zapewniają stabilność i powtarzalność środowiska.

Bez względu na to, czy pracujesz samodzielnie, czy w dużym zespole - korzystanie ze środowisk wirtualnych powinno być standardem w każdym projekcie Pythona.

!!! success "Dlaczego mamba + conda-lock na tych zajęciach?"

    Na zajęciach praktycznych będziemy używać kombinacji **mamba + conda-lock**, ponieważ:
    
    - **Mamba** jest znacznie szybsza niż klasyczna conda (nawet 10x),
    - **conda-lock** zapewnia pełną reprodukowalność środowiska na różnych systemach operacyjnych,
    - Ekosystem **conda-forge** oferuje gotowe pakiety naukowe i inżynierskie bez konieczności kompilacji,
    - To sprawdzony zestaw w projektach data science i inżynierskich.

## Narzędzia

W Pythonie dostępnych jest wiele narzędzi do **zarządzania środowiskami wirtualnymi i zależnościami**. Wybór odpowiedniego narzędzia zależy od specyficznych potrzeb projektu oraz od tego, jak precyzyjnie chcemy kontrolować zależności.

### [venv](https://docs.python.org/3/library/venv.html)

Wbudowany pakiet Pythona, który pozwala tworzyć proste środowiska wirtualne bez dodatkowych zależności. Nie oferuje wbudowanego mechanizmu zarządzania zależnościami - należy używać `pip` i tworzyć ręcznie pliki `requirements.txt`.

Najlepszy wybór, gdy potrzebujesz **szybkiego i lekkiego rozwiązania bez dodatkowego narzutu**, np. do małych, jednorazowych projektów lub nauki.

### [pipenv](https://pipenv.pypa.io/en/latest/)

Łączy funkcjonalności `pip` (zarządzanie zależnościami) i `venv` (tworzenie środowisk). Automatycznie tworzy środowisko wirtualne oraz zarządza zależnościami przy użyciu plików `Pipfile` i `Pipfile.lock`.

Najlepszy wybór dla średnich projektów, gdy zależy Ci na **prostocie, automatyzacji i spójności środowiska** bez potrzeby sięgania po narzędzia o większej złożoności.

### [poetry](https://python-poetry.org/docs/)

Nowoczesne i zaawansowane narzędzie, które ułatwia zarządzanie zależnościami, wersjonowanie i publikację paczek. Bazuje na pliku `pyproject.toml`, automatycznie tworzy środowisko i generuje `poetry.lock`.

Najlepszy wybór dla **profesjonalnych projektów**, w których zależy Ci na **precyzyjnej kontroli wersji, łatwym publikowaniu bibliotek oraz integracji z nowoczesnym ekosystemem Pythona**.

### conda / [miniconda](https://docs.anaconda.com/miniconda/)

Lekka wersja Anacondy, zawiera `conda` – menedżer pakietów i środowisk, który pozwala na instalowanie również bibliotek niskopoziomowych (np. C/C++). Działa niezależnie od `pip`, ale można je łączyć.

Najlepszy wybór w projektach **data science, obliczeniach naukowych i big data**, gdzie potrzebne są zależności spoza ekosystemu Pythona oraz większa elastyczność w zarządzaniu środowiskiem.

[conda-lock](https://conda.github.io/conda-lock/) to rozszerzenie do `conda`, które dodatkowo generuje pliki blokujące (`conda-lock.yml`), co zapewnia spójność wersji (identyczne środowiska w różnych systemach). Warto dodać do środowiska z `conda`, gdy potrzebujesz **ścisłej kontroli wersji i pełnej reprodukowalności środowiska**, np. przy wdrożeniach na różnych platformach lub w zespołach o zróżnicowanych systemach operacyjnych.

[mamba](https://mamba.readthedocs.io/) to wydajniejsza alternatywa dla `conda`, w pełni z nią kompatybilna. Używa C++ pod spodem, dzięki czemu znacznie przyspiesza rozwiązywanie zależności. Najlepszy wybór, gdy chcesz **korzystać z ekosystemu `conda`, ale potrzebujesz szybszego działania** - szczególnie przy dużych projektach lub częstej rekonfiguracji środowisk.

### [pixi](https://pixi.sh/)

Nowoczesne narzędzie od twórców `mamba` (prefix.dev), napisane w Rust. Łączy funkcjonalność `conda`, `mamba` i `conda-lock` w jednym spójnym narzędziu. Używa pliku `pixi.toml` do definiowania środowiska i automatycznie generuje pliki blokady (`pixi.lock`).

Kluczowe cechy:

- **Hybryda conda + PyPI** – używa `rattler` (Rust) dla pakietów conda-forge oraz **`uv`** dla pakietów PyPI,
- **Wbudowane lock files** – bez potrzeby instalowania dodatkowych narzędzi,
- **Kompatybilność z conda-forge** – pełen dostęp do pakietów z ekosystemu `conda`,
- **Obsługa zadań** – można definiować skrypty/zadania podobnie jak w `npm`.

Najlepszy wybór dla nowych projektów, gdy chcesz **prostoty połączonej z mocą ekosystemu conda-forge** - szczególnie w projektach **geoinformatycznych i data science**.

### [uv](https://docs.astral.sh/uv/)

Nowoczesne, lekkie narzędzie skoncentrowane na pracy z `pyproject.toml`, projektowane z myślą o wydajności i minimalnym narzucie. Łączy instalację pakietów, rozwiązywanie zależności i cache’owanie w jednym szybkim narzędziu.

Najlepszy wybór dla **zaawansowanych użytkowników**, którzy chcą **maksymalnej kontroli i szybkości** przy pracy z projektami opartymi na `pyproject.toml`.

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
