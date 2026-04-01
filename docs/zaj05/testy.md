# Testowanie w Pythonie

Testowanie pozwala upewnić się, że kod działa poprawnie, że nowe zmiany nie psują istniejących funkcjonalności.

W Pythonie standardem jest `pytest` - oferuje parametryzację, fixtures, czytelne asercje i bogaty ekosystem pluginów.

## Szybki start z pytest

```bash
# Instalacja
conda install pytest

# Uruchomienie testów
pytest                      # wszystkie testy
pytest tests/unit/          # tylko folder
pytest tests/test_math.py   # tylko plik
pytest -v                   # szczegółowy output
pytest -x                   # zatrzymaj po pierwszym błędzie
pytest -k "add"             # tylko testy zawierające "add" w nazwie
```

## Organizacja testów w repozytorium

```
<repo-main-folder>/
├── src/
│   └── myapp/
│       ├── __init__.py
│       ├── cinema.py
│       └── exceptions.py
├── tests/
│   ├── conftest.py          # Współdzielone fixtures
│   ├── unit/
│   │   ├── test_cinema.py
│   │   └── test_exceptions.py
│   ├── integration/
│   │   └── test_database.py
│   └── e2e/
│       └── test_workflows.py
├── pytest.ini
└── pyproject.toml
```

Przykładowy `pytest.ini`:

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_functions = test_*
addopts = -v --tb=short
markers =
    slow: marks tests as slow
    integration: marks integration tests
```

## Rodzaje testów

### Testy jednostkowe (unit tests)

Testują pojedyncze funkcje/metody w izolacji. Szybkie, bez zewnętrznych zależności.

```python
def add(a, b):
    return a + b

def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
```

### Testy integracyjne (integration tests)

Weryfikują współpracę między modułami (np. z bazą danych).

```python
import sqlite3

def test_save_and_retrieve():
    conn = sqlite3.connect(":memory:")
    conn.execute("CREATE TABLE users (name TEXT)")
    conn.execute("INSERT INTO users VALUES (?)", ("Jan",))
    
    result = conn.execute("SELECT name FROM users").fetchone()
    assert result[0] == "Jan"
```

### Testy e2e (end-to-end)

Testują całą aplikację z perspektywy użytkownika.

```python
def test_full_reservation_workflow():
    hall = CinemaHall(rows=2, seats_per_row=3)
    
    # Użytkownik rezerwuje miejsce
    hall.reserve("A1", "Jan Kowalski")
    
    # Próba rezerwacji zajętego miejsca
    with pytest.raises(SeatOccupiedError):
        hall.reserve("A1", "Anna Nowak")
    
    # Anulowanie i ponowna rezerwacja
    hall.cancel("A1", "Jan Kowalski")
    hall.reserve("A1", "Anna Nowak")
    
    assert hall.get_reservation("A1") == "Anna Nowak"
```

??? note "Inne rodzaje testów"
    - **Testy regresyjne** - sprawdzają, czy nowe zmiany nie zepsuły istniejących funkcjonalności
    - **Testy smoke** - szybkie testy czy aplikacja w ogóle działa
    - **Testy wydajnościowe** - mierzą czas odpowiedzi i zużycie zasobów
    - **Testy bezpieczeństwa** - szukają luk (SQL Injection, XSS)

## Asercje w pytest

Asercja (`assert`) to instrukcja sprawdzająca, czy dany warunek jest prawdziwy - jeśli nie, test kończy się niepowodzeniem. Dzięki asercjom możemy w prosty sposób weryfikować, czy wynik działania kodu zgadza się z oczekiwanym.

```python
def test_assertions():
    # Równość
    assert result == expected
    assert result != other
    
    # Prawdziwość
    assert condition
    assert not condition
    
    # Zawieranie
    assert item in collection
    assert "substring" in text
    
    # Typy
    assert isinstance(obj, MyClass)
    
    # Przybliżone porównania (float)
    assert result == pytest.approx(3.14, rel=1e-2)
    
    # Porównania
    assert value > 0
    assert 0 <= value <= 100
```

## Testowanie wyjątków

```python
import pytest

def divide(a, b):
    if b == 0:
        raise ValueError("Nie można dzielić przez zero")
    return a / b

def test_divide_by_zero():
    with pytest.raises(ValueError) as exc_info:
        divide(10, 0)
    
    assert "zero" in str(exc_info.value)

def test_divide_by_zero_simple():
    with pytest.raises(ValueError):
        divide(10, 0)

# Testowanie konkretnego komunikatu
def test_divide_by_zero_match():
    with pytest.raises(ValueError, match="zero"):
        divide(10, 0)
```

## Parametryzacja testów

Zamiast pisać wiele podobnych testów, używamy `@pytest.mark.parametrize`:

```python
import pytest

@pytest.mark.parametrize("a, b, expected", [
    (2, 3, 5),
    (-1, 1, 0),
    (0, 0, 0),
    (100, -50, 50),
])
def test_add(a, b, expected):
    assert add(a, b) == expected

# Parametryzacja z ID dla czytelności
@pytest.mark.parametrize("seat, user, should_raise", [
    ("A1", "Jan Kowalski", False),
    ("Z99", "Anna Nowak", True),  # nieprawidłowe miejsce
], ids=["valid_seat", "invalid_seat"])
def test_reserve(seat, user, should_raise):
    hall = CinemaHall()
    if should_raise:
        with pytest.raises(InvalidSeatError):
            hall.reserve(seat, user)
    else:
        hall.reserve(seat, user)
        assert hall.get_reservation(seat) == user
```

## Fixtures

Fixtures to funkcje przygotowujące dane lub zasoby dla testów.

### Podstawowe użycie

```python
import pytest

@pytest.fixture
def cinema_hall():
    """Tworzy salę kinową 3x3 do testów."""
    return CinemaHall(rows=3, seats_per_row=3)

def test_reserve_seat(cinema_hall):
    cinema_hall.reserve("A1", "Jan Kowalski")
    assert cinema_hall.get_reservation("A1") == "Jan Kowalski"

def test_hall_capacity(cinema_hall):
    assert cinema_hall.total_seats == 9
```

### Fixture z setup i teardown (yield)

Fixture z `yield` pozwala podzielić kod na dwie fazy: setup (przed `yield`) przygotowuje zasoby, a teardown (po `yield`) je sprząta. Dzięki temu test otrzymuje gotowy zasób, a po jego zakończeniu następuje automatyczne zwolnienie zasobów - nawet jeśli test zakończy się błędem.

```python
@pytest.fixture
def database():
    # Setup
    conn = sqlite3.connect(":memory:")
    conn.execute("CREATE TABLE users (id INTEGER, name TEXT)")
    
    yield conn  # Tu test się wykonuje
    
    # Teardown
    conn.close()

def test_insert_user(database):
    database.execute("INSERT INTO users VALUES (1, 'Jan')")
    result = database.execute("SELECT name FROM users").fetchone()
    assert result[0] == "Jan"
```

### Scope fixtures

```python
@pytest.fixture(scope="function")  # domyślny - nowa instancja dla każdego testu
def fresh_hall():
    return CinemaHall()

@pytest.fixture(scope="module")  # jedna instancja dla całego modułu
def shared_config():
    return load_config()

@pytest.fixture(scope="session")  # jedna instancja dla całej sesji testów
def database_connection():
    return create_connection()
```

### Plik `conftest.py`

Fixtures zdefiniowane w `conftest.py` są dostępne dla wszystkich testów w danym folderze i podfolderach.

```python
# tests/conftest.py
import pytest

@pytest.fixture
def sample_user():
    return {"name": "Jan", "email": "jan@example.com"}

@pytest.fixture
def cinema_hall():
    return CinemaHall(rows=5, seats_per_row=10)
```

Użycie fixtures w testach:

```python
# tests/unit/test_cinema.py
import pytest

def test_reserve_seat(cinema_hall, sample_user):
    """Test używa fixtures z conftest.py - nie trzeba ich importować."""
    cinema_hall.reserve("A1", sample_user["name"])
    assert cinema_hall.get_reservation("A1") == sample_user["name"]

def test_hall_capacity(cinema_hall):
    """Każdy test może używać tylko potrzebnych fixtures."""
    assert cinema_hall.total_seats == 50
```

## Markery

Markery pozwalają kategoryzować i kontrolować wykonanie testów.

```python
import pytest

# Pomijanie testu
@pytest.mark.skip(reason="Funkcjonalność w trakcie implementacji")
def test_future_feature():
    pass

# Warunkowe pomijanie
@pytest.mark.skipif(sys.platform == "win32", reason="Nie działa na Windows")
def test_unix_only():
    pass

# Oczekiwany błąd
@pytest.mark.xfail(reason="Znany bug #123")
def test_known_bug():
    assert broken_function() == expected

# Własne markery
@pytest.mark.slow
def test_heavy_computation():
    pass

# Uruchomienie: pytest -m "not slow"
```

Rejestracja własnych markerów w `pytest.ini`:

```ini
[pytest]
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
```

## Mockowanie

Mockowanie zastępuje rzeczywiste zależności sztucznymi obiektami.

### Podstawowy mock

Podstawowy mock (`MagicMock`) to sztuczny obiekt, który tworzysz od zera i **ręcznie** przekazujesz do testowanego kodu. Musisz sam zadbać o to, żeby testowana funkcja używała tego mocka (np. przez argument lub dependency injection).

```python
from unittest.mock import MagicMock, patch

def test_with_mock():
    # Tworzenie mocka
    mock_db = MagicMock()
    mock_db.get_user.return_value = {"name": "Jan"}
    
    # Użycie
    result = mock_db.get_user(1)
    
    # Weryfikacja
    assert result["name"] == "Jan"
    mock_db.get_user.assert_called_once_with(1)
```

### Patchowanie (podmiana w runtime)

Patchowanie (`patch`) **automatycznie** podmienia istniejący obiekt w module na mock w runtime. Nie musisz zmieniać sposobu wywołania - patch "wchodzi" do kodu i podmienia import, co jest przydatne gdy testujesz kod, który sam importuje zależności.

```python
from unittest.mock import patch

# Jako dekorator
@patch("myapp.services.requests.get")
def test_api_call(mock_get):
    mock_get.return_value.json.return_value = {"status": "ok"}
    
    result = fetch_status()
    
    assert result == "ok"
    mock_get.assert_called_once()

# Jako context manager
def test_api_call_v2():
    with patch("myapp.services.requests.get") as mock_get:
        mock_get.return_value.status_code = 200
        
        result = check_health()
        
        assert result is True
```

### Mock z pytest-mock (czystszy syntax)

```python
def test_with_mocker(mocker):
    mock_get = mocker.patch("myapp.api.requests.get")
    mock_get.return_value.json.return_value = {"data": "test"}
    
    result = get_data()
    
    assert result == {"data": "test"}
```

## Pokrycie kodu (coverage)

```bash
# Instalacja
pip install pytest-cov

# Uruchomienie z coverage
pytest --cov=myapp tests/

# Raport HTML
pytest --cov=myapp --cov-report=html tests/

# Minimalne pokrycie (fail jeśli poniżej)
pytest --cov=myapp --cov-fail-under=80 tests/
```

Przykładowy output:

```
---------- coverage: platform linux, python 3.11 ----------
Name                    Stmts   Miss  Cover
-------------------------------------------
myapp/__init__.py           2      0   100%
myapp/cinema.py            45      3    93%
myapp/exceptions.py        12      0   100%
-------------------------------------------
TOTAL                      59      3    95%
```

## Wzorzec AAA (Arrange-Act-Assert)

Każdy test powinien mieć trzy wyraźne sekcje:

```python
def test_reserve_seat():
    # Arrange - przygotowanie danych
    hall = CinemaHall(rows=3, seats_per_row=3)
    user = "Jan Kowalski"
    seat = "A1"
    
    # Act - wykonanie akcji
    hall.reserve(seat, user)
    
    # Assert - sprawdzenie wyniku
    assert hall.get_reservation(seat) == user
    assert hall.available_seats == 8
```

## Dobre praktyki

### Nazewnictwo testów

```python
# ❌ Źle
def test_1():
def test_function():

# ✅ Dobrze - opisuje co testujemy i oczekiwany wynik
def test_reserve_valid_seat_succeeds():
def test_reserve_occupied_seat_raises_error():
def test_cancel_with_wrong_user_raises_error():
```

### Izolacja testów

```python
# ❌ Źle - testy zależą od siebie
hall = CinemaHall()

def test_reserve():
    hall.reserve("A1", "Jan")

def test_cancel():
    hall.cancel("A1", "Jan")  # Zależy od test_reserve!

# ✅ Dobrze - każdy test niezależny
def test_reserve():
    hall = CinemaHall()
    hall.reserve("A1", "Jan")
    assert hall.get_reservation("A1") == "Jan"

def test_cancel():
    hall = CinemaHall()
    hall.reserve("A1", "Jan")  # Setup w teście
    hall.cancel("A1", "Jan")
    assert hall.get_reservation("A1") is None
```

### Jeden test = jedna rzecz

```python
# ❌ Źle - testuje zbyt wiele
def test_cinema_hall():
    hall = CinemaHall()
    hall.reserve("A1", "Jan")
    assert hall.get_reservation("A1") == "Jan"
    hall.cancel("A1", "Jan")
    assert hall.get_reservation("A1") is None
    with pytest.raises(SeatOccupiedError):
        hall.reserve("A1", "Anna")
        hall.reserve("A1", "Piotr")

# ✅ Dobrze - osobne testy
def test_reserve_seat():
    ...

def test_cancel_reservation():
    ...

def test_reserve_occupied_seat_raises():
    ...
```

## 📝 Zadania

Testy piszemy dla klas z projektu `python1course.zaj03` - `Incident`, `Ambulance` i `IncidentQueue`.

### 1. Testy jednostkowe klasy `Incident`

- test tworzenia instancji z poprawnymi danymi,
- test `validate_priority()` dla poprawnych i niepoprawnych wartości,
- test `update_status()` - zmiana na poprawny i niepoprawny status (oczekiwany `ValueError`),
- test `get_age_in_minutes()` - sprawdź, że zwraca wartość >= 0.

### 2. Testy jednostkowe klasy `Ambulance`

- test walidacji statusu przez setter `@property` - poprawny status i oczekiwany `ValueError` dla niepoprawnego,
- test `validate_id()` dla różnych typów danych z użyciem parametryzacji,
- test `update_location()`.

### 3. Testy klasy `IncidentQueue`

- test dodawania incydentów (`+=`),
- test `__contains__` - incydent jest/nie jest w kolejce,
- test `__call__` - wyszukiwanie po ID (istniejący i nieistniejący),
- test `__len__`,
- test iteracji - sprawdź, że `for incident in queue` przechodzi przez wszystkie elementy w poprawnej kolejności,
- test zagnieżdżonej iteracji - jeśli przeprowadziłeś refaktoryzację z `zaj04` na `IncidentQueueIterator`, sprawdź że dwie niezależne pętle po tej samej kolejce działają poprawnie.

### 4. Fixtures i `conftest.py`

Stwórz w `conftest.py` fixtures:

- `incident()` - zwraca gotową instancję `Incident` z przykładowymi danymi,
- `ambulance()` - zwraca dostępną karetkę,
- `queue_with_incidents()` - zwraca `IncidentQueue` z 3 incydentami.

### 5. Uruchom testy z pokryciem kodu

```bash
pytest --cov=python1course.zaj03 tests/
```

???+ tip "Struktura plików"
    ```
    tests/
    ├── conftest.py
    └── unit/
        └── zaj05/
            ├── test_incident.py
            ├── test_ambulance.py
            └── test_incident_queue.py
    ```

??? - note "Połączenie z `zaj04` - `pytest.raises` to context manager"
    Zwróć uwagę, że `pytest.raises` używasz dokładnie tak jak `with FileLock(...)` z poprzednich zajęć:

    ```python
    with pytest.raises(ValueError):
        ambulance.status = "zły_status"
    ```

    To nie przypadek - `pytest.raises` jest menadżerem kontekstu (`__enter__`/`__exit__`). Wewnątrz bloku `with` pytest "łapie" wyjątek zamiast pozwolić mu przerwać test.

??? - note "Połączenie z `zaj04` - fixture z `yield` to ten sam wzorzec co context manager"
    Fixture z `yield`:

    ```python
    @pytest.fixture
    def tmp_file(tmp_path):
        path = tmp_path / "test.txt"
        path.touch()
        yield path        # tu wykonuje się test
        path.unlink()     # teardown - sprzątanie po teście
    ```

    To dokładnie ten sam wzorzec co `__enter__` / `__exit__`:
    
    - kod przed `yield` = `__enter__` (setup),
    - kod po `yield` = `__exit__` (teardown, wywołany zawsze - nawet gdy test się wysypie).
