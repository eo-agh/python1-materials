W tym module poznasz podstawowe informacje o systemie kontroli wersji git. Omówimy commity, synchronizację z repozytorium zdalnym, pracę z gałęziami oraz współpracę przez pull requesty.

## Czym jest git?

Git to rozproszony system kontroli wersji, który umożliwia śledzenie zmian w plikach, współpracę między programistami i zarządzanie kodem źródłowym w sposób efektywny i bezpieczny. Jest szeroko stosowany w projektach open-source oraz w firmach na całym świecie.

### Kluczowe cechy gita
- **Rozproszony system** – każdy użytkownik ma pełną kopię repozytorium,
- **Wersjonowanie** – śledzenie zmian i możliwość powrotu do wcześniejszych wersji kodu,
- **Gałęzie (branches)** – możliwość pracy nad różnymi wersjami kodu równocześnie,
- **Integracja z platformami zdalnymi** – np. GitHub, GitLab, Bitbucket.

---

## Połączenie z repozytorium zdalnym

Tą funkcjonalość mamy już opanowaną - zrobiliśmy to w poprzednich krokach.

## Co to są commity i jak działają?

???+ info "Commit - zapisywanie zmian"

    **Commit** to jak "zapisanie gry" w programowaniu. To moment, w którym zapisujesz aktualny stan swoich plików w repozytorium git. Każdy commit ma unikalny identyfikator (hash) i zawiera informacje o tym, co zostało zmienione, kiedy i przez kogo.

    **Dlaczego commity są ważne?**

    - Pozwalają śledzić historię zmian w kodzie.
    - Umożliwiają powrót do wcześniejszych wersji.
    - Ułatwiają współpracę w zespole.
    - Pomagają zrozumieć, co i dlaczego zostało zmienione.

    **Podstawowy workflow z commitami**:
    ```sh
    # 1. Sprawdź status plików
    git status
    
    # 2. Dodaj pliki do staging area (przygotowanie do commita)
    git add nazwa-pliku.py
    # lub dodaj wszystkie zmienione pliki:
    git add .
    
    # 3. Stwórz commit z opisem zmian
    git commit -m "Add function to calculate average"
    ```

    **Dobre praktyki przy commitach**:

    - Pisz jasne i zwięzłe opisy commitów.
    - Commituj często, ale tylko logicznie spójne zmiany.
    - Używaj czasu teraźniejszego w opisach (np. "Add function" zamiast "Added function").

    **Przykłady dobrych opisów commitów**:
    ```
    "Fix calculation bug in average function"
    "Add input validation for user data"
    "Remove unused imports"
    "Update function documentation"
    ```

## Synchronizacja z repozytorium zdalnym

???+ info "Push, Pull i Fetch"

    Gdy pracujesz (chociażby na tych zajęciach), musisz synchronizować zmiany z repozytorium zdalnym (np. na GitHubie). To dopiero tam inni zainteresowani będą mieć wgląd w kod i zmiany.

    **Push - wysyłanie zmian na serwer**:
    ```sh
    git push origin nazwa-galezi
    ```
    Wysyła twoje lokalne commity do repozytorium zdalnego. `origin` to domyślna nazwa repozytorium zdalnego, a `nazwa-galezi` to gałąź, którą chcesz wysłać.

    **Pull - pobieranie i scalanie zmian**:
    ```sh
    git pull origin nazwa-galezi
    ```
    Pobiera zmiany z repozytorium zdalnego i automatycznie scala je z twoją lokalną gałęzią. To skrót dla `git fetch` + `git merge`.

    **Fetch - tylko pobieranie informacji**:
    ```sh
    git fetch origin
    ```
    Pobiera informacje o zmianach z repozytorium zdalnego, ale nie scala ich automatycznie. Pozwala sprawdzić, co się zmieniło, zanim zdecydujesz się na scalenie.

    **Kiedy używać którego polecenia?**

    - `git push` - gdy skończyłeś pracę nad funkcją i chcesz udostępnić zmiany innym.
    - `git pull` - gdy chcesz pobrać najnowsze zmiany od innych programistów.
    - `git fetch` - gdy chcesz tylko sprawdzić, czy są nowe zmiany, bez ich scalania.

    **Typowy workflow**:
    ```sh
    # Przed rozpoczęciem pracy - pobierz najnowsze zmiany
    git pull origin main
    
    # Po skończeniu pracy - wyślij swoje zmiany
    git push origin moja-galez
    ```

## 📝 Zadania

1. Przenieś swoje zmiany po wykonanym poprzednim zadaniu do staging area.
2. Zacommituj je.
3. Wypchnij swoje zmiany do repozytorium zdalnego.

---

## Gałęzie

???+ info "Branches"
    
    Gałęzie pozwalają na jednoczesne rozwijanie różnych funkcji projektu bez wpływania na główną wersję kodu. To bardzo użyteczne, gdy kilka osób pracuje nad różnymi aspektami projektu lub gdy testujesz nowe funkcjonalności przed ich wdrożeniem do głównej wersji kodu.

    **Tworzenie nowej gałęzi**:
    ```sh
    git branch nazwa-galezi
    ```
    Tworzy nową gałąź, ale pozostajesz na obecnej.

    **Przełączanie się na nową gałąź**:
    ```sh
    git checkout nazwa-galezi
    ```
    Lub używając nowoczesnej wersji polecenia:
    ```sh
    git switch nazwa-galezi
    ```

    **Tworzenie i przejście na nową gałąź jednocześnie**:
    ```sh
    git checkout -b nazwa-galezi
    ```
    Dzięki temu unikasz konieczności tworzenia gałęzi i przełączania się na nią osobno.

    **Merging (scalanie gałęzi)**:

    Przykład: synchronizacja swojej gałęzi z najnowszymi zmianami z `main`:
    ```sh
    # 1. Przełącz się na swoją gałąź
    git checkout feature/kalkulator
    
    # 2. Pobierz najnowsze zmiany z main
    git pull origin main
    
    # 3. Scal zmiany z main do swojej gałęzi
    git merge main
    
    # 4. Wyślij zaktualizowaną gałąź
    git push origin feature/kalkulator
    ```
    
    **Kiedy to robić?**

    - Gdy ktoś inny dodał zmiany do main, a ty chcesz mieć najnowszą wersję.
    - Gdy chcesz uniknąć konfliktów przy końcowym mergowaniu.
    - Gdy chcesz przetestować swoją funkcję z najnowszymi zmianami.
    
    **Alternatywnie - rebase (bardziej eleganckie)**:
    ```sh
    git checkout feature/kalkulator
    git pull origin main
    git rebase main
    git push origin feature/kalkulator
    ```

## Współpraca przez repozytorium zdalne

???+ info "Pull Requests"

    Pull Requesty to sposób na proponowanie zmian w kodzie i ich review przed mergowaniem do głównej gałęzi.

    **Workflow**:

    1. Wyślij gałąź na serwer: `git push origin feature/kalkulator`.
    2. Na GitHubie kliknij "Compare & pull request".
    3. Wypełnij tytuł i opis zmian.
    4. Poproś o review od innych programistów.
    5. Po zaakceptowaniu - merge do głównej gałęzi.

    **Dlaczego PR?**

    - Code review przed mergowaniem.
    - Dokumentacja zmian i dyskusji.
    - Lepsza jakość kodu.
    - Współpraca w zespole.

---

Teraz masz podstawową wiedzę na temat gita. Możemy zaczynać pracę nad Pythonem!

!!! tip "Nowoczesne IDE mają moduły do obsługi kontroli wersji, więc możemy używać interfejsu graficznego zamiast poleceń w terminalu."

---

## Przydatne w codziennej pracy

???- tip "Revert i Reset - wycofywanie zmian"

    Błędy zdarzają się każdemu. Dlatego git oferuje kilka metod na ich cofnięcie.

    **Cofnięcie ostatniego commita**:
    ```sh
    git revert HEAD
    ```
    Tworzy nowy commit cofający zmiany z poprzedniego. Jest to bezpieczna metoda, ponieważ nie usuwa historii.

    Jeśli chcesz całkowicie usunąć commit:
    ```sh
    git reset --hard HEAD~1
    ```
    Ta opcja może być ryzykowna, bo usunie lokalne zmiany bez możliwości ich przywrócenia.

    **Usunięcie zmian w plikach przed commitem**:
    ```sh
    git checkout -- nazwa-pliku
    ```
    Przywraca wersję pliku do ostatniego stanu w repozytorium. Przydatne, jeśli zmieniłeś plik przez pomyłkę.

???- tip "Stash – przechowanie tymczasowych zmian"
    
    Czasami pracujemy nad jakąś zmianą, ale musimy pilnie przełączyć się na inną gałąź. W takich przypadkach możemy użyć `stash`.

    **Schowanie zmian**:
    ```sh
    git stash
    ```
    To powoduje zapisanie bieżących zmian na stosie i przywrócenie plików do stanu z ostatniego commita.

    **Przywrócenie ostatniego stasha**:
    ```sh
    git stash pop
    ```
    Dzięki temu odzyskujesz zapisane zmiany.

???- tip "Sprawdzanie historii commitów"

    Historia commitów pomaga w śledzeniu zmian w kodzie, co jest szczególnie przydatne w zespołach.

    **Podstawowa historia commitów**:
    ```sh
    git log
    ```

    **Skrócona i czytelniejsza wersja**:
    ```sh
    git log --oneline --graph --all
    ```
    Pozwala szybko zobaczyć strukturę commitów i ich relacje.

???- tip "Usuwanie plików z repozytorium i `.gitignore`"

    Nie zawsze chcemy, aby dany plik był częścią repozytorium.

    **Usunięcie pliku z repozytorium i lokalnego systemu plików**:
    ```sh
    git rm nazwa-pliku
    ```

    **Usunięcie pliku tylko z repozytorium, ale zachowanie go lokalnie**:
    ```sh
    git rm --cached nazwa-pliku
    ```
    To przydatne, jeśli przypadkowo dodaliśmy do repozytorium plik, który nie powinien się tam znaleźć (np. plik konfiguracyjny).

    **`.gitignore`**
    Plik `.gitignore` pozwala ignorować określone pliki i katalogi, aby nie były one dodawane do repozytorium.

    **Przykład `.gitignore`**:
    ```
    # Ignorowanie plików tymczasowych edytora
    *.swp
    *.swo

    # Ignorowanie katalogu build
    /build/

    # Ignorowanie plików konfiguracyjnych
    config.yaml
    .env
    ```
    Plik `.gitignore` powinien być umieszczony w głównym katalogu repozytorium.

???- tip "git LFS (Large File Storage)"

    Jeśli pracujesz z dużymi plikami, git może nie być optymalny do ich przechowywania. Git LFS (Large File Storage) to rozszerzenie gita, które pozwala przechowywać duże pliki osobno od kodu źródłowego.

    **Instalacja git LFS**:
    ```sh
    # Na systemach Linux/macOS
    curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
    sudo apt install git-lfs

    # Na Windows
    choco install git-lfs
    ```

    **Używanie git LFS**:
    ```sh
    git lfs install
    ```
    Następnie dodaj typ plików, które mają być przechowywane w LFS:
    ```sh
    git lfs track "*.psd"
    ```
    To oznacza, że pliki `.psd` będą przechowywane w git LFS zamiast w standardowym repozytorium gita.

    Dzięki temu repozytorium pozostaje lekkie, a duże pliki są przechowywane w zoptymalizowany sposób.