# Logowanie w Pythonie

Logowanie pozwala rejestrować zdarzenia podczas działania programu - do debugowania, monitorowania i analizy błędów.

`print()` jest wygodny, ale nie skaluje się: brak poziomów ważności, brak zapisu do pliku, brak informacji skąd pochodzi komunikat. Biblioteka `logging` rozwiązuje te problemy.

## Poziomy logowania

| Poziom | Wartość | Zastosowanie |
|--------|---------|--------------|
| `DEBUG` | 10 | Szczegółowe informacje diagnostyczne |
| `INFO` | 20 | Potwierdzenie, że program działa poprawnie |
| `WARNING` | 30 | Coś nieoczekiwanego, ale program działa |
| `ERROR` | 40 | Poważny problem, funkcja nie mogła się wykonać |
| `CRITICAL` | 50 | Krytyczny błąd, program może się zakończyć |

Ustawiony poziom oznacza: loguj ten poziom i wszystkie wyższe.

```python
import logging

logging.basicConfig(level=logging.WARNING)

logging.debug("Nie zostanie wyświetlone")    # poziom 10 < 30
logging.info("Nie zostanie wyświetlone")     # poziom 20 < 30
logging.warning("Zostanie wyświetlone")      # poziom 30 >= 30
logging.error("Zostanie wyświetlone")        # poziom 40 >= 30
```

## Podstawowa konfiguracja

```python
import logging

logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

logging.debug("Aplikacja uruchomiona")
logging.info("Połączono z bazą danych")
logging.warning("Brak pliku konfiguracyjnego, używam domyślnych wartości")
logging.error("Nie udało się zapisać do pliku")
```

Output:

```
2024-01-15 10:30:45,123 - DEBUG - Aplikacja uruchomiona
2024-01-15 10:30:45,124 - INFO - Połączono z bazą danych
2024-01-15 10:30:45,125 - WARNING - Brak pliku konfiguracyjnego, używam domyślnych wartości
2024-01-15 10:30:45,126 - ERROR - Nie udało się zapisać do pliku
```

### Logowanie do pliku

```python
import logging

logging.basicConfig(
    filename="app.log",
    filemode="a",  # append (domyślnie) lub "w" (nadpisz)
    level=logging.INFO,
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)

logging.info("Start aplikacji")
logging.error("Błąd połączenia z API")
```

### Logowanie do konsoli i pliku jednocześnie

```python
import logging

# Konfiguracja root loggera
logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s - %(levelname)s - %(message)s",
    handlers=[
        logging.FileHandler("app.log"),
        logging.StreamHandler()
    ]
)

logging.info("Ten komunikat trafi do konsoli i pliku")
```

## Format komunikatów

Dostępne atrybuty w formacie:

| Atrybut | Opis |
|---------|------|
| `%(asctime)s` | Czas w formacie `YYYY-MM-DD HH:MM:SS,mmm` |
| `%(name)s` | Nazwa loggera |
| `%(levelname)s` | Poziom: DEBUG, INFO, WARNING, ERROR, CRITICAL |
| `%(message)s` | Treść komunikatu |
| `%(filename)s` | Nazwa pliku źródłowego |
| `%(lineno)d` | Numer linii |
| `%(funcName)s` | Nazwa funkcji |
| `%(module)s` | Nazwa modułu |

Przykład szczegółowego formatu:

```python
FORMAT = "%(asctime)s | %(name)s | %(levelname)-8s | %(filename)s:%(lineno)d | %(message)s"

logging.basicConfig(level=logging.DEBUG, format=FORMAT)
```

Output:

```
2024-01-15 10:30:45,123 | root | DEBUG    | main.py:15 | Szczegółowy komunikat
```

## Loggery, Handlery, Formattery

Architektura logowania w Pythonie:

```
Logger (tworzy komunikaty)
   │
   ├── Handler 1 (StreamHandler → konsola)
   │      └── Formatter
   │
   └── Handler 2 (FileHandler → plik)
          └── Formatter
```

### Tworzenie loggera z handlerami

```python
import logging

# 1. Tworzenie loggera
logger = logging.getLogger("myapp")
logger.setLevel(logging.DEBUG)

# 2. Tworzenie formattera
formatter = logging.Formatter(
    "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)

# 3. Handler do konsoli
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)  # INFO i wyższe do konsoli
console_handler.setFormatter(formatter)

# 4. Handler do pliku
file_handler = logging.FileHandler("app.log")
file_handler.setLevel(logging.ERROR)  # tylko ERROR i CRITICAL do pliku
file_handler.setFormatter(formatter)

# 5. Dodanie handlerów do loggera
logger.addHandler(console_handler)
logger.addHandler(file_handler)

# Użycie
logger.debug("Nie wyświetli się nigdzie (poziom loggera DEBUG, ale handlery mają wyższe)")
logger.info("Tylko konsola")
logger.error("Konsola i plik")
```

### Rotacja plików logów

Dla długo działających aplikacji - automatyczne tworzenie nowych plików:

```python
from logging.handlers import RotatingFileHandler, TimedRotatingFileHandler

# Rotacja po rozmiarze (max 5MB, max 3 pliki backup)
handler = RotatingFileHandler(
    "app.log",
    maxBytes=5*1024*1024,  # 5 MB
    backupCount=3
)

# Rotacja po czasie (nowy plik co dzień, max 7 dni)
handler = TimedRotatingFileHandler(
    "app.log",
    when="midnight",
    interval=1,
    backupCount=7
)
```

## Logger per moduł

**Dobra praktyka**: każdy moduł ma własny logger o nazwie `__name__`.

### Fabryka loggerów

Pomocnicza funkcja do tworzenia skonfigurowanych loggerów:

```python
# myapp/utils/logging.py
import logging
from pathlib import Path
from datetime import datetime

def get_logger(
    name: str,
    level: int = logging.INFO,
    log_dir: Path = Path("logs")
) -> logging.Logger:
    """Tworzy skonfigurowany logger z handlerami do konsoli i pliku."""
    
    logger = logging.getLogger(name)
    
    # Unikaj duplikowania handlerów
    if logger.handlers:
        return logger
    
    logger.setLevel(level)
    
    formatter = logging.Formatter(
        "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
    )
    
    # Handler konsoli
    console_handler = logging.StreamHandler()
    console_handler.setLevel(logging.INFO)
    console_handler.setFormatter(formatter)
    logger.addHandler(console_handler)
    
    # Handler pliku
    log_dir.mkdir(exist_ok=True)
    timestamp = datetime.now().strftime("%Y%m%d")
    file_handler = logging.FileHandler(
        log_dir / f"app_{timestamp}.log",
        encoding="utf-8"
    )
    file_handler.setLevel(logging.DEBUG)
    file_handler.setFormatter(formatter)
    logger.addHandler(file_handler)
    
    return logger
```

Użycie w modułach:

```python
# myapp/database.py
from myapp.utils.logging import get_logger

logger = get_logger(__name__)

def connect():
    logger.info("Łączenie z bazą...")
```

```python
# myapp/api.py
from myapp.utils.logging import get_logger

logger = get_logger(__name__)  # logger o nazwie "myapp.api"

def fetch_data(url):
    logger.info(f"Pobieranie danych z {url}")
    # ...
```

Output:

```
2024-01-15 10:30:45 - myapp.database - INFO - Łączenie z bazą danych...
2024-01-15 10:30:45 - myapp.database - DEBUG - Połączenie nawiązane
2024-01-15 10:30:46 - myapp.api - INFO - Pobieranie danych z https://api.example.com
```

## Hierarchia loggerów

Loggery tworzą hierarchię opartą na nazwach (separator: `.`):

```
root
├── myapp
│   ├── myapp.database
│   └── myapp.api
└── urllib3
```

Komunikaty propagują w górę - jeśli `myapp.database` nie ma handlerów, użyje handlerów z `myapp` lub `root`.

```python
# Wyłączenie propagacji
logger = logging.getLogger("myapp.database")
logger.propagate = False  # Nie przekazuj do loggerów nadrzędnych
```

## Logowanie wyjątków

```python
import logging

logger = logging.getLogger(__name__)

def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        logger.exception("Błąd dzielenia")  # automatycznie dołącza traceback
        raise

# Alternatywnie
try:
    result = divide(10, 0)
except ZeroDivisionError:
    logger.error("Dzielenie nie powiodło się", exc_info=True)
```

Output:

```
2024-01-15 10:30:45 - __main__ - ERROR - Błąd dzielenia
Traceback (most recent call last):
  File "main.py", line 7, in divide
    return a / b
ZeroDivisionError: division by zero
```

## Dobre praktyki

### 1. Używaj `__name__` jako nazwy loggera

```python
from myapp.utils.logging import get_logger

# ✅ Dobrze
logger = get_logger(__name__)

# ❌ Źle
logger = get_logger("my_logger")
```

### 2. Konfiguruj logowanie raz, w głównym pliku

Z tego korzystamy jedynie jak nie mamy swojej funkcji `get_logger()`.

```python
# main.py - tutaj konfiguracja
logging.basicConfig(...)

# inne moduły - tylko tworzenie loggera
logger = logging.getLogger(__name__)
```

### 3. Używaj odpowiednich poziomów

```python
# ❌ Źle - wszystko jako INFO
logger.info("Start")
logger.info("Błąd połączenia!")
logger.info("Szczegóły debugowania...")

# ✅ Dobrze - odpowiednie poziomy
logger.info("Start aplikacji")
logger.error("Błąd połączenia z bazą danych")
logger.debug("Parametry połączenia: host=localhost, port=5432")
```

### 4. Loguj wartości, nie tylko komunikaty

```python
# ❌ Źle
logger.error("Błąd")

# ✅ Dobrze
logger.error(f"Błąd połączenia z {host}:{port} - {error}")
```

### 5. Używaj `logger.exception()` w blokach except

```python
try:
    risky_operation()
except Exception:
    logger.exception("Operacja nie powiodła się")  # dołącza traceback
```

### 6. Nie loguj wrażliwych danych

```python
# ❌ Źle
logger.info(f"Logowanie użytkownika {username}, hasło: {password}")

# ✅ Dobrze
logger.info(f"Logowanie użytkownika {username}")
```

## 📝 Zadania

1. Skonfiguruj logowanie w aplikacji kina (`CinemaHall`):
    - Logi `INFO` i wyższe do konsoli,
    - Logi `DEBUG` i wyższe do pliku `logs/cinema.log`,
    - Format: `%(asctime)s - %(name)s - %(levelname)s - %(message)s`.

2. Dodaj logowanie do metod `reserve()` i `cancel()`:
    - `DEBUG`: szczegóły operacji (miejsce, użytkownik),
    - `INFO`: potwierdzenie sukcesu,
    - `WARNING`: próba rezerwacji zajętego miejsca,
    - `ERROR`: nieudana operacja z komunikatem błędu.

3. Stwórz funkcję `get_logger()` w module `python1course/zaj07/utils/logging.py`:
    - Parametry: `name`, `level`, `log_dir`,
    - Zwraca skonfigurowany logger z handlerami do konsoli i pliku,
    - Pliki logów z datą w nazwie: `app_YYYYMMDD.log`.

4. Użyj `get_logger(__name__)` w co najmniej 2 modułach i zaprezentuj działanie.

???+ tip "Struktura plików"
    ```
    <repo-main-folder>/
    ├── python1course/zaj07/
    │   ├── utils/
    │   │   ├── __init__.py
    │   │   └── logging.py      # funkcja get_logger()
    │   └── cinema.py           # CinemaHall z logowaniem
    └── logs/
        └── app_20240115.log
    ```
