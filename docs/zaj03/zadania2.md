## 📝 Zadania cz. 2

Dodaj następujące funkcjonalności do programu z zajęć. Dla wszystkich dodanych funkcjonalności stwórz przykładowy kod potwierdzający, że działają (rozbudowując kod w `main_zaj03.py`).

### 1. Automatyczne zarządzanie identyfikatorami z użyciem metod klasy

Zmodyfikuj każdą klasę (oprócz `IncidentQueue`) tak, żeby posiadała atrybut klasy `__max_id`, który będzie wykorzystywany do automatycznego nadawania identyfikatorów kolejnym stworzonym instancjom (zamiast podawania go jako argument przy inicjalizacji).

**Wymagania:**

- Użyj metod klasy (`@classmethod`) do zarządzania `__max_id`:
    - Stwórz metodę klasy `get_next_id(cls)`, która zwraca kolejny dostępny ID i zwiększa `__max_id`
- Zmodyfikuj konstruktory klas, aby automatycznie przypisywały ID przy tworzeniu instancji
- Dodaj metodę klasy `reset_id_counter(cls)`, która resetuje licznik ID

**Przykład użycia:**
```python
# Przed: incident = Incident(1, "Description")
# Po: incident = Incident("Description")  # ID przypisane automatycznie
print(incident.id)  # 1
incident2 = Incident("Description 2")
print(incident2.id)  # 2
```

---

??? - warning "Dla chętnych - użycie stałych zbiorów wartości dla stanów (np. priorytetów zdarzeń czy statusów karetek)"

    W obecnej implementacji używamy stringów do reprezentowania priorytetów zdarzeń (`"low"`, `"critical"`) i statusów karetek (`"available"`, `"on_mission"`). To działa, ale ma wady:

    - Podatność na błędy literowe: `"critcal"` zamiast `"critical"` nie zostanie wykryte
    - Brak walidacji - można użyć dowolnego stringa
    - Trudniejsze porównania priorytetów (nie można łatwo porównać `"low" < "high"`)
    - Brak wsparcia IDE (autouzupełnianie nie podpowiada dostępnych wartości)

    Rozwiązanie: **Enum**
    
    `Enum` (wyliczenie) to **specjalny typ klasy** w Pythonie, który reprezentuje zbiór stałych, nazwanych wartości. Enum to klasa, która dziedziczy po klasie bazowej z modułu `enum`. Każdy członek Enum jest instancją tej klasy i ma przypisaną stałą wartość.
    
    ```python
    from enum import Enum
    
    class Priority(Enum):
        LOW = 1
        MEDIUM = 2
        HIGH = 3
    
    # Priority.LOW to instancja klasy Priority
    print(type(Priority.LOW))  # <enum 'Priority'>
    print(Priority.LOW.name)   # "LOW" - nazwa członka
    print(Priority.LOW.value)  # 1 - wartość
    ```
    
    Python oferuje kilka wariantów Enum. Dla naszego przypadku najlepsze są:

    - **`Enum`** - podstawowa klasa wyliczeniowa, członkowie nie są porównywalni z `int`/`str`
    - **`IntEnum`** - dziedziczy po `int` i `Enum`, członkowie działają jak liczby całkowite (można porównywać, sortować)
    - **`StrEnum`** - dziedziczy po `str` i `Enum`, członkowie działają jak stringi (można porównywać ze stringami)
    
    **Różnice:**
    ```python
    from enum import Enum, IntEnum, StrEnum
    
    class Basic(Enum):
        VALUE = 1
    
    class Int(IntEnum):
        VALUE = 1
    
    class Str(StrEnum):
        VALUE = "value"
    
    # Enum - nie działa porównanie z int
    Basic.VALUE == 1  # False!
    Basic.VALUE.value == 1  # True (trzeba użyć .value)
    
    # IntEnum - działa jak int
    Int.VALUE == 1  # True - działa bezpośrednio!
    Int.VALUE < 2  # True - można porównywać
    
    # StrEnum - działa jak str
    Str.VALUE == "value"  # True - działa bezpośrednio!
    ```

    **Przykładowa implementacja dla naszych przypadków:**

    ```python
    from enum import IntEnum, StrEnum

    class Priority(IntEnum):
        """Priorytet incydentu - używa IntEnum dla łatwych porównań"""
        LOW = 1
        MEDIUM = 2
        HIGH = 3
        CRITICAL = 4

    class AmbulanceStatus(StrEnum):
        """Status karetki - używa StrEnum dla kompatybilności ze stringami"""
        AVAILABLE = "available"
        ON_MISSION = "on_mission"
        ...
    ```

    **Zalety tego podejścia:**

    1. **Bezpieczeństwo typów** - nie można użyć nieprawidłowej wartości
    2. **Łatwe porównania priorytetów** (dzięki `IntEnum`):
    ```python
    Priority.LOW < Priority.HIGH  # True
    Priority.CRITICAL > Priority.MEDIUM  # True
    
    # Sortowanie po priorytecie:
    incidents = [incident1, incident2, incident3]
    sorted_incidents = sorted(incidents, key=lambda x: x.priority, reverse=True)
    ```

    3. **Autouzupełnianie w IDE** - `Priority.` pokaże wszystkie dostępne wartości

    4. **Kompatybilność** - `StrEnum` działa jak stringi:
    ```python
    ambulance.status = AmbulanceStatus.AVAILABLE
    if ambulance.status == "available":  # True - działa!
        pass
    ```

    5. **Czytelność kodu**:
    ```python
    # Przed:
    if incident.priority == "critical":
    
    # Po:
    if incident.priority == Priority.CRITICAL:  # Bardziej czytelne!
    ```

    **Użycie w kodzie:**

    ```python
    # Tworzenie incydentów z Enum
    incident = Incident(
        "Fire in building",
        priority=Priority.CRITICAL,  # Zamiast "critical"
        reporter_name="John Doe",
        reporter_phone="123456789"
    )

    # Porównywanie priorytetów
    if incident.priority >= Priority.HIGH:
        print("High priority incident!")

    # Statusy karetek
    ambulance.status = AmbulanceStatus.ON_MISSION
    if ambulance.status == AmbulanceStatus.AVAILABLE:
        print("Ambulance is available")

    # Nadal działa z stringami (dzięki StrEnum):
    if ambulance.status == "available":  # True
        pass
    ```

    **Zadanie:** Zmodyfikuj klasy `Incident` i `Ambulance`, aby używały `Priority` i `AmbulanceStatus` zamiast stringów. Zaktualizuj pozostałe miejsca w kodzie.

### 2. Rozbudowa klasy `Incident` z wykorzystaniem slotów

Rozbuduj klasę `Incident` o następujące atrybuty:

- **Priorytet zdarzenia** (np. "low", "medium", "high", "critical")
- **Czas zgłoszenia** (obiekt `datetime`)
- **Informacje o zgłaszającym** (imię, nazwisko, numer telefonu)
- **Status incydentu** (np. "reported", "assigned", "in_progress", "resolved") - śledzi etap obsługi incydentu

**Wymagania:**

- Użyj **`__slots__`** do optymalizacji pamięci (klasa `Incident` będzie miała wiele instancji)
- Dodaj **metodę statyczną** (`@staticmethod`) `validate_priority(priority)`, która sprawdza, czy priorytet jest poprawny - logika działania dowolna
- Dodaj **`@property`** dla atrybutu `status` z setterem, który akceptuje wyłącznie dozwolone wartości (`"reported"`, `"assigned"`, `"in_progress"`, `"resolved"`) i rzuca `ValueError` dla innych - wzoruj się na klasie `Ambulance` z repozytorium
- Dodaj **metodę instancji** `get_age_in_minutes()`, która zwraca liczbę minut od zgłoszenia
- Dodaj **metodę instancji** `update_status(new_status)`, która aktualizuje status incydentu korzystając z settera powyższego property (np. gdy zostanie przydzielona karetka, zmienia status z `"reported"` na `"assigned"`)
- Zaimplementuj metodę `__str__()`, która wyświetla informacje o incydencie w czytelny sposób

**Przykład użycia:**
```python
from datetime import datetime

incident = Incident(
    "Fire in building",
    priority="critical",
    reporter_name="John Doe",
    reporter_phone="123456789"
)
print(incident.status)  # "reported" - domyślny status przy tworzeniu
print(incident.get_age_in_minutes())  # Liczba minut od zgłoszenia

# Aktualizacja statusu
incident.update_status("assigned")  # Karetka została przydzielona
incident.update_status("in_progress")  # Karetka jest w drodze/na miejscu
incident.update_status("resolved")  # Incydent został obsłużony
```

---

### 3. Klasa `Station` z abstrakcyjną klasą nadrzędną

Zaprojektuj w ramach subpakietu `fleet` klasę `Station`. Każda stacja ma posiadać:

- Identyfikator (automatyczny, jak w zadaniu 1)
- Lokalizację (współrzędne x, y)
- Karetkę (instancję klasy `Ambulance`)
- Kierowcę (instancję klasy `Driver`)
- 1 dodatkowego pracownika (instancję klasy `Employee`)

**Wymagania:**

- Stwórz **abstrakcyjną klasę nadrzędną** `BaseStation` (używając `ABC`), która definiuje:
    - Abstrakcyjną metodę `is_ambulance_available()` - sprawdza czy karetka jest dostępna
    - Abstrakcyjną metodę `get_station_info()` - zwraca informacje o stacji
- Klasa `Station` powinna dziedziczyć po `BaseStation` i implementować te metody
- Dodaj **metodę instancji** `is_ambulance_at_station()`, która sprawdza czy karetka jest na miejscu (czy zgadzają się lokalizacje karetki i stacji)
- Użyj **metody statycznej** `calculate_distance(loc1, loc2)` do obliczania odległości między lokalizacjami

**Przykład użycia:**
```python
# Tworzenie stacji
station = Station(
    location=(10, 20),
    ambulance=ambulance,
    driver=driver,
    employee=employee
)

# Sprawdzenie czy karetka jest na stacji (lokalizacja się zgadza)
print(station.is_ambulance_at_station())  # True/False

# Sprawdzenie czy karetka jest dostępna
# Karetka jest dostępna gdy:
# - Jest na stacji (lokalizacja się zgadza)
# - Jest w stanie "available" (nie jest w drodze, nie jest na miejscu zdarzenia)
print(station.is_ambulance_available())  # True/False
```

---

### 4. System zarządzania incydentami

Rozbuduj aplikację o możliwość zarządzania incydentami - przydzielanie karetek do zgłaszanych zdarzeń. Te funkcjonalności muszą uwzględniać:

- **Priorytet** i **czas, który upłynął od zgłoszenia**,
- **Aktualny stan**, w którym znajduje się karetka,
- **Odległość** karetki od zdarzenia.

**Wymagania:**

Stwórz klasę `IncidentManager`, która:

- **Używa `IncidentQueue`** do przechowywania wszystkich incydentów (zamiast zwykłej listy) - wykorzystaj magiczne metody `IncidentQueue`, takie jak `+=` do dodawania incydentów
- Przechowuje listę wszystkich stacji
- Ma metodę `add_incident(incident)` - dodaje incydent do systemu (użyj operatora `+=` z `IncidentQueue`)
- Ma metodę `assign_ambulance(incident)` - przydziela karetkę do incydentu na podstawie:
    - Priorytetu incydentu (wyższy priorytet = szybsze przydzielenie)
    - Czasu od zgłoszenia (dłuższy czas = wyższy priorytet)
    - Dostępności karetki (tylko dostępne karetki)
    - Odległości od incydentu (najbliższa dostępna karetka)
    - Po przydzieleniu karetki, aktualizuje status incydentu z "reported" na "assigned" (użyj metody `update_status()`)
- Wykorzystaj możliwości `IncidentQueue` - użyj metody `__call__` do wyszukiwania incydentów po ID, iteracji przez incydenty, sprawdzania czy incydent jest w kolejce (`in`)
- Dodaj metodę `get_incidents_by_status(status)` - zwraca listę incydentów o danym statusie (np. wszystkie "reported" czekające na przydzielenie)

**Metody pomocnicze:**

- Dodaj **metodę statyczną** `calculate_priority_score(incident)` - oblicza "wynik priorytetu" na podstawie priorytetu i czasu od zgłoszenia
- Dodaj **metodę instancji** `get_available_ambulances()` - zwraca listę dostępnych karetek
- Dodaj **metodę instancji** `get_statistics()` - zwraca statystyki systemu (ile incydentów obsłużono, średni czas reakcji) - wykorzystaj `len()` na `IncidentQueue`

**Przykład użycia:**
```python
manager = IncidentManager(stations=[station1, station2])

incident1 = Incident("Fire", priority="critical")
incident2 = Incident("Injury", priority="medium")

# Dodawanie incydentów (wykorzystuje operator += z IncidentQueue)
manager.add_incident(incident1)
manager.add_incident(incident2)

# Wykorzystanie magicznych metod IncidentQueue
print(len(manager.incidents))  # Liczba incydentów
print(incident1 in manager.incidents)  # Sprawdzenie przynależności

# Wyszukiwanie incydentu po ID (wykorzystuje __call__)
found_incident = manager.incidents(incident1.id)

# Iteracja przez incydenty
for incident in manager.incidents:
    print(incident)

# Automatyczne przydzielanie karetek
manager.assign_ambulance(incident1)
print(incident1.status)  # "assigned" - status został zaktualizowany

manager.assign_ambulance(incident2)
print(incident2.status)  # "assigned"

# Pobranie incydentów o danym statusie
reported_incidents = manager.get_incidents_by_status("reported")  # Incydenty czekające
assigned_incidents = manager.get_incidents_by_status("assigned")  # Incydenty z przydzieloną karetką

# Sprawdzenie statystyk
stats = manager.get_statistics()
print(stats)
```

---

## 💡 Wskazówki

1. **Metody klasy** - pamiętaj, że `@classmethod` przyjmuje `cls` jako pierwszy argument i działa na klasie, nie na instancji
2. **Sloty** - `__slots__` musi zawierać wszystkie atrybuty, które będą używane w klasie
3. **Abstrakcyjne klasy** - użyj `from abc import ABC, abstractmethod` i oznacz metody jako `@abstractmethod`
4. **Metody statyczne** - `@staticmethod` nie potrzebuje `self` ani `cls`, to po prostu funkcje powiązane z klasą

