Są to narzędzia, które pomagają w **zarządzaniu zasobami**, takimi jak pliki, połączenia sieciowe, czy połączenia z bazami danych. Dzięki menadżerom kontekstu można upewnić się, że zasoby zostaną poprawnie otwarte, a następnie zamknięte - nawet jeśli w trakcie korzystania z zasobu wystąpi wyjątek.

## Instrukcja `with`

Python zapewnia obsługę menadżerów kontekstu za pomocą instrukcji `with`, która gwarantuje poprawne zarządzanie zasobami. Gdy używamy `with`, zasób jest automatycznie otwierany i zamykany, co zmniejsza ryzyko wycieków pamięci i innych błędów wynikających z niezamknięcia zasobów.

```python
with open("plik.txt", "w") as plik:
    plik.write("Witaj, świecie!")
```

## Tworzenie własnych menadżerów kontekstu

Aby utworzyć własny menadżer kontekstu, wystarczy zdefiniować klasę z dwiema metodami:

- `__enter__` – ta metoda jest wywoływana na początku bloku `with` i powinna zwracać zasób, którym będziemy zarządzać.
- `__exit__` – ta metoda jest wywoływana na końcu bloku `with` i służy do czyszczenia zasobu (np. zamknięcia go), bez względu na to, czy wystąpił wyjątek.

Jako przykład, menadżer kontekstu dla połączenia z bazą danych:

```python
class PolaczenieBazaDanych:
    def __enter__(self):
        print("Nawiązywanie połączenia z bazą danych...")
        # Symulacja połączenia, np. self.conn = connect_to_database()
        self.polaczenie = "Połączenie aktywne"
        return self.polaczenie

    def __exit__(self, exc_type, exc_value, traceback):
        print("Zamykanie połączenia z bazą danych...")
        # Symulacja zamknięcia połączenia, np. self.conn.close()
        self.polaczenie = None

# Użycie menadżera kontekstu
with PolaczenieBazaDanych() as polaczenie:
    print(polaczenie)
    # Wykonanie operacji na bazie danych
```

### Obsługa wyjątków w `__exit__`

Metoda `__exit__` otrzymuje trzy argumenty: `exc_type`, `exc_value` i `traceback`, które są związane z wyjątkiem, który mógł wystąpić wewnątrz bloku `with`. Możemy dzięki nim obsłużyć wyjątek wewnątrz `__exit__` lub po prostu pozwolić, aby wyjątek został propagowany dalej.

```python
class PolaczenieBazaDanych:
    def __enter__(self):
        print("Nawiązywanie połączenia z bazą danych...")
        self.polaczenie = "Połączenie aktywne"
        return self.polaczenie

    def __exit__(self, exc_type, exc_value, traceback):
        if exc_type:
            print(f"Wystąpił wyjątek: {exc_value}")
        print("Zamykanie połączenia z bazą danych...")
        self.polaczenie = None

# Użycie menadżera kontekstu z wyjątkiem
try:
    with PolaczenieBazaDanych() as polaczenie:
        print(polaczenie)
        raise ValueError("Symulowany błąd!")
except ValueError:
    print("Obsłużono wyjątek!")
```

## 📝 Zadania

**Zadanie 1**: FileLock - Blokada pliku

Stwórz menadżer kontekstu `FileLock` w module `python1course.zaj05.file_lock`, który zapobiega równoczesnemu dostępowi do pliku przez różne procesy lub wątki. Blokada działa poprzez tworzenie specjalnego pliku lock (np. `nazwa_pliku.lock`), który sygnalizuje, że plik jest w użyciu.

**Wymagania:**

1. **Klasa `FileLock`** powinna przyjmować w konstruktorze:
    - `filepath` (str) - ścieżka do pliku, który ma być zablokowany,
    - `timeout` (int, opcjonalne, domyślnie 10) - maksymalny czas oczekiwania na zwolnienie blokady (w sekundach).

2. **Metoda `__enter__`** powinna:
    - Sprawdzać, czy plik lock już istnieje,
    - Jeśli istnieje, czekać na jego zwolnienie (sprawdzać co `timeout` sekund),
    - Jeśli po upływie `timeout` lock nadal istnieje, zgłosić wyjątek `TimeoutError` z komunikatem: `"Nie można uzyskać blokady pliku {filepath} - timeout"`,
    - Jeśli lock nie istnieje lub został zwolniony, utworzyć plik lock (możesz użyć `pathlib.Path`).

3. **Metoda `__exit__`** powinna:
    - Usunąć plik lock, jeśli istnieje,
    - Działać poprawnie zarówno przy normalnym zakończeniu, jak i przy wystąpieniu wyjątku.

4. **Dodatkowe wymagania:**
    - Użyj modułu `time` do implementacji oczekiwania.
    - Użyj `pathlib.Path` do pracy z plikami.
    - Plik lock powinien mieć nazwę: `{oryginalna_nazwa_pliku}.lock`.

**Przykład użycia:**

```python
from pathlib import Path
import time

# Przykład 1: Normalne użycie
with FileLock("data.txt"):
    with open("data.txt", "a") as f:
        f.write("Dane\n")
    # Plik lock zostanie automatycznie usunięty

# Przykład 2: Z timeoutem
try:
    with FileLock("data.txt", timeout=5):
        # Długotrwała operacja
        time.sleep(10)
except TimeoutError as e:
    print(e)  # "Nie można uzyskać blokady pliku data.txt - timeout"

# Przykład 3: Obsługa wyjątków
try:
    with FileLock("data.txt"):
        with open("data.txt", "a") as f:
            f.write("Dane\n")
            raise ValueError("Błąd podczas zapisu!")
except ValueError:
    print("Wystąpił błąd, ale lock został zwolniony")
    # Lock powinien być usunięty mimo wyjątku
```

**Wskazówki:**

- Użyj `Path.exists()` do sprawdzania istnienia pliku lock.
- Użyj `Path.touch()` do tworzenia pliku lock.
- Użyj `Path.unlink()` do usuwania pliku lock.
- W pętli oczekiwania sprawdzaj co sekundę (lub częściej), czy lock został zwolniony.
- Pamiętaj, że `__exit__` jest wywoływana zawsze, nawet przy wyjątku.
