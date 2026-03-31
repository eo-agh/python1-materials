## Menadżery kontekstu

Menadżer kontekstu to mechanizm, który pomaga bezpiecznie zarządzać zasobami (np. plikami, połączeniami, blokadami).
Najważniejsza zaleta: kod sprzątający wykona się zawsze, nawet gdy w środku bloku wystąpi wyjątek.

## Instrukcja `with`

Instrukcja `with` upraszcza klasyczny wzorzec `try/finally`.
Zamiast ręcznie pamiętać o zwalnianiu zasobu, przekazujemy to menadżerowi kontekstu.

```python
with open("plik.txt", "w") as plik:
    plik.write("Witaj, świecie!")
```

Równoważny schemat bez `with`:

```python
plik = open("plik.txt", "w")
try:
    plik.write("Witaj, świecie!")
finally:
    plik.close()
```

## Co dokładnie robi `with`?

W uproszczeniu:

1. Tworzy obiekt menadżera kontekstu.
2. Woła `__enter__()` i przypisuje wynik po `as`.
3. Wykonuje kod wewnątrz bloku `with`.
4. Zawsze woła `__exit__(...)`, niezależnie od tego, czy był wyjątek.

## Tworzenie własnych menadżerów kontekstu

Aby utworzyć własny menadżer kontekstu, definiujemy klasę z dwiema metodami:

- `__enter__()` - uruchamiana na początku bloku `with`; zwykle przygotowuje zasób i go zwraca,
- `__exit__(exc_type, exc_value, traceback)` - uruchamiana na końcu bloku; sprząta zasób.

Przykład:

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

Metoda `__exit__` otrzymuje:

- `exc_type` - typ wyjątku,
- `exc_value` - instancję wyjątku,
- `traceback` - ślad wykonania.

Jeśli w `__exit__` zwrócisz:

- `False` (albo nic) - wyjątek jest propagowany dalej,
- `True` - wyjątek zostaje stłumiony.

```python
class LoggerBledu:
    def __enter__(self):
        print("Start")
        return self

    def __exit__(self, exc_type, exc_value, traceback):
        if exc_type:
            print(f"Wystąpił wyjątek: {exc_value}")
        print("Koniec")
        return False  # wyjątek NIE jest tłumiony

try:
    with LoggerBledu():
        raise ValueError("Symulowany błąd!")
except ValueError:
    print("Obsłużono wyjątek!")
```

??? - warning "Kiedy zwracać `True` w `__exit__`?"
    Raczej rzadko. Tłumienie wyjątków bywa przydatne, ale łatwo ukryć błąd.
    Domyślnie bezpieczniej jest nie tłumić wyjątków.

## 📝 Zadania

### **Zadanie 1**: FileLock - Blokada pliku

Stwórz menadżer kontekstu `FileLock` w module `python1course.zaj04.file_lock`, który zapobiega równoczesnemu dostępowi do pliku.
Blokada działa przez tworzenie pliku lock (np. `nazwa_pliku.lock`), który sygnalizuje, że zasób jest zajęty.

**Wymagania:**

1. **Klasa `FileLock`** powinna przyjmować w konstruktorze:
    - `filepath` (str) - ścieżka do pliku, który ma być zablokowany,
    - `timeout` (int, opcjonalne, domyślnie 10) - maksymalny czas oczekiwania na zwolnienie blokady (w sekundach).

2. **Metoda `__enter__`** powinna:
    - Sprawdzać, czy plik lock już istnieje,
    - Jeśli istnieje, czekać na jego zwolnienie (sprawdzanie cykliczne),
    - Jeśli po upływie `timeout` lock nadal istnieje, zgłosić wyjątek `TimeoutError` z komunikatem: `"Nie można uzyskać blokady pliku {filepath} - timeout"`,
    - Jeśli lock nie istnieje lub został zwolniony, utworzyć plik lock.

3. **Metoda `__exit__`** powinna:
    - Usunąć plik lock, jeśli istnieje,
    - Działać poprawnie zarówno przy normalnym zakończeniu, jak i przy wyjątku.

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
    # Plik lock zostanie automatycznie usunięty po wyjściu z bloku with

# Przykład 2: Sprawdzenie czy lock został utworzony
lock_path = Path("data.txt.lock")
with FileLock("data.txt"):
    print(f"Czy plik lock istnieje? {lock_path.exists()}")  # True
    # Operacje na pliku
print(f"Czy plik lock istnieje po wyjściu? {lock_path.exists()}")  # False

# Przykład 3: Timeout - jeśli plik jest już zablokowany
# Sztucznie tworzymy plik lock, symulując sytuację gdy inny proces go trzyma:
lock_file = Path("data.txt.lock")
lock_file.touch()  # Tworzymy plik lock

# Teraz próbujemy uzyskać lock - to spowoduje TimeoutError,
# bo plik lock już istnieje i nie zostanie zwolniony w ciągu 2 sekund:
with FileLock("data.txt", timeout=2):
    # Ten kod się nie wykona, bo TimeoutError zostanie zgłoszony
    pass

# Pamiętaj, żeby usunąć sztucznie utworzony plik lock po teście:
lock_file.unlink()
```

**Kryteria zaliczenia zadania**

- Lock jest tworzony przy wejściu do `with`.
- Lock jest usuwany po wyjściu z `with` (także przy wyjątku).
- Timeout działa poprawnie.
- Implementacja nie zostawia "osieroconych" plików lock po błędzie.

**Wskazówki**

- Użyj `Path.exists()` do sprawdzania istnienia pliku lock.
- Użyj `Path.touch()` do tworzenia pliku lock.
- Użyj `Path.unlink()` do usuwania pliku lock.
- W pętli oczekiwania sprawdzaj np. co 0.1-0.5 sekundy, czy lock został zwolniony.
- Pamiętaj, że `__exit__` jest wywoływana zawsze, nawet przy wyjątku.

---

### **Zadanie 2**: ReportWriter - menadżer kontekstu w projekcie z `zaj03`

W projekcie z `zaj03` `IncidentManager` potrafi wyciągać statystyki systemu (`get_statistics()`).
Dodaj możliwość zapisywania raportu do pliku w bezpieczny sposób - z użyciem własnego menadżera kontekstu.

Stwórz klasę `ReportWriter` w module `python1course.zaj04.report_writer`:

1. Konstruktor przyjmuje `filepath` (str) - ścieżka do pliku raportu.
2. `__enter__` otwiera plik do zapisu i zwraca obiekt, na którym można wywołać `write_report(manager)`:
    - zapisuje do pliku wynik `manager.get_statistics()`,
    - każde wywołanie `write_report` dopisuje nowy wpis z aktualnym znacznikiem czasu.
3. `__exit__` zamyka plik - zawsze, nawet gdy `write_report` rzuci wyjątek.

**Przykład użycia:**

```python
with ReportWriter("raport.txt") as writer:
    writer.write_report(manager)
# plik jest zamknięty, wpis jest zapisany
```

Następnie połącz oba zadania z `zaj04`: użyj `FileLock` razem z `ReportWriter`, żeby zapis raportu był bezpieczny przed równoczesnym dostępem:

```python
with FileLock("raport.txt"):
    with ReportWriter("raport.txt") as writer:
        writer.write_report(manager)
```

??? - tip "Co ćwiczymy tym zadaniem?"
    - zagnieżdżanie menadżerów kontekstu (`with` w `with`),
    - separację odpowiedzialności: `FileLock` pilnuje dostępu, `ReportWriter` pilnuje zapisu,
    - połączenie nowych narzędzi (`zaj04`) z istniejącym kodem domenowym (`zaj03`).
