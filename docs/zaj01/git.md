W tym module poznasz podstawowe informacje o systemie kontroli wersji git oraz nauczysz się, jak połączyć się z repozytorium zdalnym. Omówimy instalację gita, konfigurację użytkownika, klonowanie repozytorium oraz wysyłanie i pobieranie zmian.

## Czym jest git?

Git to rozproszony system kontroli wersji, który umożliwia śledzenie zmian w plikach, współpracę między programistami i zarządzanie kodem źródłowym w sposób efektywny i bezpieczny. Jest szeroko stosowany w projektach open-source oraz w firmach na całym świecie.

### Kluczowe cechy gita
- **Rozproszony system** – każdy użytkownik ma pełną kopię repozytorium,
- **Wersjonowanie** – śledzenie zmian i możliwość powrotu do wcześniejszych wersji kodu,
- **Gałęzie (branches)** – możliwość pracy nad różnymi wersjami kodu równocześnie,
- **Integracja z platformami zdalnymi** – np. GitHub, GitLab, Bitbucket.

---

## Połączenie z repozytorium zdalnym

Aby połączyć się z repozytorium zdalnym, musisz wykonać następujące kroki:

### 1. Instalacja gita
Jeśli nie masz jeszcze gita, możesz go zainstalować:

- **Linux (Ubuntu/Debian):**
  ```sh
  sudo apt update
  sudo apt install git
  ```

- **Windows:** Pobierz instalator ze strony [git-scm.com](https://git-scm.com/) i zainstaluj.

- **macOS:**
  ```sh
  brew install git
  ```

Po instalacji sprawdź wersję:
```sh
git --version
```

### 2. Konfiguracja użytkownika
Przed rozpoczęciem pracy skonfiguruj swoje dane:
```sh
git config user.name "Twoje Imię i Nazwisko"
git config user.email "twoj@email.com"
```

!!! danger "Link do Twojego repozytorium zdalnego dostaniesz od prowadzącego zajęcia!"

### 3. Klonowanie repozytorium zdalnego

!!! danger "Tego kroku nie robimy, to tylko przykład, nas interesuje punkt 4."

Jeśli chcesz pobrać istniejące repozytorium, użyj komendy:
```sh
git clone https://github.com/uzytkownik/nazwa-repozytorium.git
```
Lub jeśli używasz SSH:
```sh
git clone git@github.com:uzytkownik/nazwa-repozytorium.git
```

### 4. Połączenie istniejącego katalogu z repozytorium
Jeśli masz już lokalny projekt i chcesz połączyć go z repozytorium zdalnym:
```sh
git init
```
Dodaj zdalne repozytorium:
```sh
git remote add origin https://github.com/uzytkownik/nazwa-repozytorium.git
```
Sprawdź, czy połączenie zostało poprawnie dodane:
```sh
git remote -v
```

### 5. Wysyłanie zmian do repozytorium
Dodaj pliki do wersjonowania:
```sh
git add .
```
Zatwierdź zmiany:
```sh
git commit -m "First commit"
```
Wypchnij zmiany na zdalne repozytorium:
```sh
git push -u origin lokalna-nazwa-galezi:main --force
```

### 6. Pobieranie zmian z repozytorium
Aby pobrać najnowsze zmiany z repozytorium:
```sh
git pull origin main
```

---

Teraz masz podstawową wiedzę na temat gita i połączenia się z repozytorium zdalnym. Możesz rozpocząć pracę nad swoimi zadaniami!

!!! tip "Nowoczesne IDE mają moduły do obsługi kontroli wersji, więc możemy używać interfejsu graficznego zamiast poleceń w terminalu."

???- tip "Praca z gałęziami"
    ## Gałęzie (branches)
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
    Aby scalić zmiany z innej gałęzi do głównej (`main`):
    ```sh
    git checkout main
    ```
    ```sh
    git merge nazwa-galezi
    ```
    Scalanie przydaje się, gdy zakończysz prace nad nową funkcjonalnością i chcesz dodać ją do głównej wersji kodu.

???- tip "Revert i Reset"
    ## Wycofywanie zmian

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
    ## Przechowywanie tymczasowych zmian (stash)
    
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
    ## Historia commitów

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
    ## .gitignore

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
    ## git LFS

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