Mam nadzieję, że nie muszę mówić, że testowanie oprogramowania jest kluczowym elementem jakiegokolwiek procesu tworzenia aplikacji. Dzięki temu, możemy się upewnić, że kod działa poprawnie, jest odporny na błędy i spełnia wymagania funkcjonalne. Do tego przy rozwijaniu aplikacji mamy pewność, że zachowujemy wszystkie działające poprzednio elementy.

W Pythonie zwykle do testowania używa się pakietu `pytest` - zaawansowane funkcje, parametryzacja, wspiera testy w stylu funkcjonalnym i obiektowym, ale funkcjonują także inne: `unittest` - wbudowany w Pythona, podstawowe testowanie, `mock` - pozwala na tworzenie obiektów zastępczych, `tox` - umożliwia testowanie w wielu środowiskach.

## Rodzaje testów

Testy można podzielić na kilka kategorii w zależności od celu, który mają spełniać, oraz poziomu aplikacji, na którym działają.

### Testy jednostkowe (unit tests)

**Cel** - sprawdzanie działania najmniejszych jednostek kodu, takich jak funkcje, metody czy klasy, w izolacji od reszty aplikacji.

**Charakterystyka**:

- Skupiają się na jednej funkcjonalności w oderwaniu od innych części systemu.
- Nie wymagają dostępu do baz danych, API, czy zewnętrznych zasobów.
- Są szybkie w uruchamianiu.

```python
# Funkcja do testowania
def add(a, b):
    return a + b

# Test jednostkowy
def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0
```

### Testy integracyjne (integration tests)

**Cel** - weryfikacja, czy różne moduły aplikacji współpracują ze sobą poprawnie.

**Charakterystyka**:

- Obejmują interakcje między komponentami, np. komunikację z bazą danych czy integrację z API.
- Mogą być wolniejsze niż testy jednostkowe, ponieważ wymagają dostępu do zasobów zewnętrznych.

```python
# Funkcja zapisująca dane do bazy
def save_to_database(data, db_connection):
    cursor = db_connection.cursor()
    cursor.execute("INSERT INTO table_name (column) VALUES (?)", (data,))
    db_connection.commit()

# Test integracyjny
def test_save_to_database():
    db_connection = sqlite3.connect(":memory:")  # Tworzenie testowej bazy danych
    save_to_database("test_data", db_connection)
    cursor = db_connection.cursor()
    cursor.execute("SELECT column FROM table_name")
    result = cursor.fetchone()
    assert result == ("test_data",)
```

### Testy e2e (end-to-end tests)

**Cel** - sprawdzenie całego procesu użytkownika w aplikacji, od wejścia do wyjścia, wraz z interakcją między różnymi komponentami, takimi jak bazy danych, API, czy frontend i backend.

**Charakterystyka**:

- Testują aplikację jako całość, w pełnym środowisku, jak użytkownik końcowy.
- Obejmują wszystkie warstwy systemu (UI, backend, bazy danych, integracje zewnętrzne).
- Wymagają skonfigurowanego środowiska produkcyjnego lub stagingowego.
- Są czasochłonne, ponieważ uruchamiają całą aplikację.
- Wymagają częstych aktualizacji w miarę zmieniających się funkcjonalności systemu.

```python
# Przykład z użyciem Selenium do automatyzacji przeglądarki
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
import time

def test_purchase_workflow():
    # Uruchomienie przeglądarki
    driver = webdriver.Chrome()

    try:
        # 1. Otwórz stronę główną
        driver.get("https://example.com")

        # 2. Wyszukaj product
        search_box = driver.find_element(By.NAME, "search")
        search_box.send_keys("Laptop")
        search_box.send_keys(Keys.RETURN)

        time.sleep(2)  # Poczekaj na załadowanie wyników

        # 3. Dodaj product do koszyka
        add_to_cart_button = driver.find_element(By.CSS_SELECTOR, ".add-to-cart")
        add_to_cart_button.click()

        time.sleep(2)

        # 4. Przejdź do koszyka i dokonaj zakupu
        driver.get("https://example.com/cart")
        checkout_button = driver.find_element(By.ID, "checkout")
        checkout_button.click()

        # 5. Sprawdź, czy zakup zakończył się sukcesem
        success_message = driver.find_element(By.CLASS_NAME, "success")
        assert "Zakup zakończony pomyślnie" in success_message.text

    finally:
        # Zamknięcie przeglądarki
        driver.quit()
```

### Inne

??? - "Testy funkcjonalne (functional tests)"
    **Cel** - testowanie aplikacji z punktu widzenia użytkownika końcowego.

    **Charakterystyka**:

    - Sprawdzają pełne scenariusze działania aplikacji.
        - Mogą obejmować interfejs użytkownika (np. testowanie przeglądarki) lub operacje backendowe.
        - Wymagają uruchomienia pełnego środowiska aplikacji.

    ```python
    def test_user_registration(client):
        response = client.post("/register", data={"username": "test", "password": "pass"})
        assert response.status_code == 200
        assert b"Registration successful" in response.data
    ```

??? - "Testy systemowe (system tests)"
    **Cel** - testowanie aplikacji jako całości w środowisku jak najbardziej zbliżonym do produkcyjnego.

    **Charakterystyka**:

    - Obejmują wszystkie komponenty systemu, takie jak serwery, bazy danych i API.
    - Są najdroższe w utrzymaniu i najwolniejsze, ale dają pełen obraz działania systemu.

??? - "Testy regresjyjne (regression tests)"
    **Cel** - upewnienie się, że now zmiany w kodzie nie spowodowały błędów w działających wcześniej funkcjonalnościach.

    **Charakterystyka**:

    - Oparte na istniejących testach jednostkowych, integracyjnych i funkcjonalnych.
    - Automatyzowane w ramach Continuous Integration (CI).

??? - "Testy akceptacyjne (acceptance tests)"
    **Cel** - sprawdzanie, czy aplikacja spełnia wymagania biznesowe i jest gotowa do użycia.

    **Charakterystyka**:

    - Prowadzone na podstawie scenariuszy dostarczonych przez klienta lub zespół produktowy.
    - Mogą być przeprowadzane ręcznie lub automatycznie.

    ```python
    def test_shopping_cart_workflow(client):
        client.post("/add_to_cart", data={"product_id": 1})
        client.post("/add_to_cart", data={"product_id": 2})
        response = client.get("/cart")
        assert "product_id: 1" in response.data
        assert "product_id: 2" in response.data
    ```

??? - "Testy wydajnościowe (performance tests)"
    **Cel** - ocena, jak szybko działa aplikacja przy określonym obciążeniu.

    **Charakterystyka**:

    - Sprawdzają czas odpowiedzi, zużycie zasobów i zdolność do obsługi dużej liczby równoczesnych użytkowników.

??? - "Testy bezpieczeństwa (security tests)"
    **Cel** - znalezienie potencjalnych luk w zabezpieczeniach aplikacji.

    **Charakterystyka**:

    - Sprawdzają, czy aplikacja jest odporna na ataki typu SQL Injection, Cross-Site Scripting (XSS) itp.

    ```python
    def test_sql_injection(client):
        response = client.get("/search", query_string={"q": "' OR 1=1; --"})
        assert b"Unexpected error" not in response.data
    ```

??? - "Testy eksploracyjne (exploratory tests)"
    **Cel** - ręczne testowanie aplikacji w celu znalezienia nieoczekiwanych błędów.

    **Charakterystyka**:

    - Wykonywane przez doświadczonych testerów bez szczegółowych scenariuszy.
    - Koncentrują się na eksploracji aplikacji i szukaniu niestandardowych scenariuszy.

??? - "Testy smoke (smoke tests)"
    **Cel** - upewnienie się, że najważniejsze funkcjonalności działają po wdrożeniu lub aktualizacji aplikacji.

    **Charakterystyka**:

    - Szybkie, podstawowe testy wykonywane przed szczegółowymi testami.

    ```python
    def test_app_up(client):
        response = client.get("/")
        assert response.status_code == 200
    ```

## Organizacja testów w repozytorium

Jest to kluczowe dla utrzymania czytelności, łatwości utrzymania oraz szybkiego znajdowania odpowiednich przypadków testowych.

- **Dedykowany folder dla testów**

Testy powinny znajdować się w oddzielnym folderze, zwykle nazwanym `tests`, znajdującym się w głównym katalogu projektu.

- **Podfoldery odzwierciedlające rodzaje testów**

Podfoldery tworzone są zwykle według typu testów, czyli np. `unit` – dla testów jednostkowych, `integration` – dla testów integracyjnych i `e2e` – dla testów end-to-end.

- **Struktura testów odzwierciedlająca strukturę pakietów**

Kolejne podfoldery powinny odzwierciedlać strukturę pakietów, które testujemy.

```markdown
moj_projekt/tests
├── unit/
    ├── zajecia06/
        ├── test_module1.py
        ├── test_module2.py
        ├── moj_subpakiet/
            ├── test_submodule.py
```

- **Nazwy plików i testów**

Wszystkie pliki testowe powinny zaczynać się od `test_` lub kończyć na `_test.py` (przykład powyżej).

Nazwy funkcji testowych powinny zaczynać się od `test_`.

```python
def test_add_function():
    assert add(2, 3) == 5
```

## Organizacja zależności i konfiguracja testów

**Plik `conftest.py`**

W frameworku `pytest` pozwala centralizować konfigurację i współdzielone zależności testów. Jest to miejsce, gdzie można definiować:

- Fixtures – funkcje tworzące dane testowe lub konfiguracje.
- Funkcje pomocnicze – wspólne dla wielu testów.
- Konfiguracje specyficzne dla pytest.

Zwykle plik znajduje się w `./tests/conftest.py`.

```python
# Definiowanie fixtures w conftest.py
import pytest

@pytest.fixture
def sample_data():
    """Fixture zwracająca dane testowe."""
    return {"key": "value"}

@pytest.fixture
def database_connection():
    """Fixture symulująca połączenie z bazą danych."""
    class FakeDatabase:
        def query(self, query):
            return {"result": "fake_data"}
    return FakeDatabase()
```

```python
# Użycie fixture w testach

import pytest

@pytest.fixture
def sample_data():
    """Fixture zwracająca dane testowe."""
    return {"key": "value"}

@pytest.fixture
def database_connection():
    """Fixture symulująca połączenie z bazą danych."""
    class FakeDatabase:
        def query(self, query):
            return {"result": "fake_data"}
    return FakeDatabase()
```

**Mockowanie**

Mockowanie to technika zastępowania rzeczywistych zależności (np. połączeń z bazą danych, API) sztucznymi obiektami podczas testów. Dzięki temu:

- Możemy izolować testy od zewnętrznych zależności.
- Testy są szybsze i bardziej niezawodne.
- Możemy testować zachowanie kodu w trudnych do odtworzenia warunkach.

```python
# Mockowanie funkcji zewnętrznej
from unittest.mock import patch

@patch("my_package.module1.requests.get")
def test_get_data_from_api(mock_get):
    # Konfiguracja mocka
    mock_get.return_value.json.return_value = {"key": "mocked_value"}

    # Wywołanie funkcji
    from my_package.module1 import get_data_from_api
    result = get_data_from_api("http://example.com/api")

    # Sprawdzenie wyniku
    assert result == {"key": "mocked_value"}
    mock_get.assert_called_once_with("http://example.com/api")
```

```python
# Mockowanie klasy
from unittest.mock import MagicMock

def test_database_fetch_data():
    # Tworzenie mocka
    mock_database = MagicMock()
    mock_database.fetch_data.return_value = {"result": "mocked_data"}

    # Testowanie funkcji z mockiem
    result = mock_database.fetch_data("SELECT * FROM table")
    assert result == {"result": "mocked_data"}
```

```python
# Mockowanie z użyciem pytest-mock
def test_get_data_with_mocker(mocker):
    # Mockowanie requests.get
    mock_get = mocker.patch("my_package.module1.requests.get")
    mock_get.return_value.json.return_value = {"key": "mocked_value"}

    from my_package.module1 import get_data_from_api
    result = get_data_from_api("http://example.com/api")

    assert result == {"key": "mocked_value"}
    mock_get.assert_called_once_with("http://example.com/api")
```

## 📝 Zadania

1. Stwórz testy jednostkowe dla programu dla karetek oraz dla rezerwacji w kinie.
2. Stwórz testy e2e (w naszym przypadku będą to po prostu całe "symulacje" zaistniałych sytuacji w programie).
3. Uruchomy testy wykorzystując `pytest`.