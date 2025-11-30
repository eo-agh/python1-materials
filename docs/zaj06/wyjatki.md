Są to błędy, które występują podczas wykonywania programu i mogą powodować jego nagłe zakończenie, jeśli nie są odpowiednio obsłużone.

Zarządzanie wyjątkami, czyli kontrola nad tym, jak program zachowuje się w sytuacji, gdzy wystąpi błąd, obejmuje wykorzystanie słów kluczowych `try`, `except`, `else` i `finally` oraz ręczne wywoływanie wyjątków za pomocą `raise`.

## Podstawowa obsługa wyjątków

Podstawowy blok obsługi wyjątków składa się z `try` i `except`.

- W bloku `try` umieszczamy kod, który może wywołać wyjątek,
- a blok `except` przechwytuje ten wyjątek i wykonuje odpowiednie działania.

```python
try:
    liczba = int(input("Podaj liczbę: "))
    wynik = 10 / liczba
    print(f"Wynik: {wynik}")
except ZeroDivisionError:
    print("Nie można dzielić przez zero!")
except ValueError:
    print("To nie jest poprawna liczba!")
```

Dodatkowo, jeśli chcemy mieć możliwość dostępu do treści błędu, można stosować następujący zapis:

```python
try:
    liczba = int(input("Podaj liczbę: "))
    wynik = 10 / liczba
    print(f"Wynik: {wynik}")
except Exception as e:
    print(f"Wystąpił błąd: {e}")
```

## Dodatkowe bloki `else` i `finally`

- Kod w bloku `else` wykona się tylko wtedy, gdy żaden wyjątek nie został wywołany w bloku `try`.
- Blok `finally` wykona się zawsze, niezależnie od tego, czy wyjątek wystąpił, czy nie. Zazwyczaj używany jest do zamykania zasobów lub wykonania czynności końcowych.

```python
try:
    liczba = int(input("Podaj liczbę: "))
    wynik = 10 / liczba
except ZeroDivisionError:
    print("Nie można dzielić przez zero!")
except ValueError:
    print("To nie jest poprawna liczba!")
else:
    print(f"Wynik: {wynik}")
finally:
    print("To był przykład obsługi wyjątków.")
```

## Ręczne wywoływanie wyjątków - `raise`

```python
def sprawdz_wiek(wiek):
    if wiek < 18:
        raise ValueError("Wiek musi być co najmniej 18 lat.")
    return "Dostęp dozwolony"

try:
    print(sprawdz_wiek(15))
except ValueError as e:
    print(f"Błąd: {e}")
```

Większość wyjątków to klasy wbudowane, które są dostępne bezpośrednio w standardowej bibliotece. Można je znaleźć w [dokumentacji](https://docs.python.org/3/library/exceptions.html#).

??? - tip "Najczęściej wykorzystywane wyjątki"
    Te najczęściej wykorzystywane to np.:

    - `ValueError` - gdy argument ma poprawny typ, ale niepoprawną wartość.
    - `TypeError` - gdy argument jest niepoprawnego typu.
    - `IndexError`- gdy indeks jest poza zakresem listy, krotki itp.
    - `KeyError` - gdy klucz nie istnieje w słowniku.
    - `AttributeError` - gdy obiekt nie ma określonego atrybutu.
    - `FileNotFoundError` - gdy określony plik nie istnieje.
    - `ZeroDivisionError` - gdy występuje próba dzielenia przez zero.
    - `PermissionError` - gdy brak jest uprawnień do wykonania operacji, np. przy otwieraniu pliku.
    - `RuntimeError` - wyjątek ogólny, stosowany w nietypowych sytuacjach, gdy żaden inny wyjątek nie jest odpowiedni.
    - `NotImplementedError` - gdy metoda jest zamierzona do implementacji w przyszłości (używany np. w metodach abstrakcyjnych).
    - `OverflowError` - gdy wynik operacji matematycznej jest zbyt duży do przetworzenia.
    - `ImportError` - gdy nie uda się zaimportować modułu lub elementu z modułu.
    - `AssertionError` - gdy `assert` sprawdzenie kończy się niepowodzeniem.

## Tworzenie własnych wyjątków

Zbudowane są na bazie klas, co pozwala na tworzenie własnych, spersonalizowanych wyjątków poprzez dziedziczenie (najczęściej po klasie `Exception`).

Dzięki temu można definiować specyficzne błędy, które są bardziej dopasowane do konkretnego kontekstu aplikacji. Tworzenie własnych wyjątków pomaga również w precyzyjniejszym zarządzaniu błędami i zapewnia bardziej czytelny kod.

Aby stworzyć własny wyjątek, definiujemy nową klasę, która dziedziczy po `Exception` lub jednej z jej klas pochodnych. Możemy także dodać do tej klasy specjalne atrybuty czy metody, które będą specyficzne dla naszego błędu.

??? - note "Prosty przykład"
    ```python
    class BrakSrodkowError(Exception):
        """Wyjątek sygnalizujący brak wystarczających środków na koncie."""
        pass

    class KontoBankowe:
        def __init__(self, saldo=0):
            self.saldo = saldo

        def wyplac(self, kwota):
            if kwota > self.saldo:
                raise BrakSrodkowError(f"Brak wystarczających środków: saldo {self.saldo} zł, potrzebne {kwota} zł")
            self.saldo -= kwota
            print(f"Wypłacono {kwota}. Aktualne saldo: {self.saldo}")

    # Przykład użycia
    konto = KontoBankowe(100)

    try:
        konto.wyplac(150)
    except BrakSrodkowError as e:
        print(f"Błąd: {e}")
    ```

??? - note "Bardziej skomplikowany przykład"
    ```python
    class BladZakresu(Exception):
        def __init__(self, wartosc, zakres_min, zakres_max):
            super().__init__(f"Wartość {wartosc} jest poza dozwolonym zakresem ({zakres_min} - {zakres_max}).")
            self.wartosc = wartosc
            self.zakres_min = zakres_min
            self.zakres_max = zakres_max

    def sprawdz_zakres(wartosc):
        if not (0 <= wartosc <= 100):
            raise BladZakresu(wartosc, 0, 100)

    try:
        sprawdz_zakres(150)
    except BladZakresu as e:
        print(f"Błąd: {e}")
        print(f"Szczegóły: Wartość = {e.wartosc}, Zakres = ({e.zakres_min} - {e.zakres_max})")
    ```

### Hierarchia wyjątków

Tworzenie własnych wyjątków daje również możliwość tworzenia hierarchii wyjątków. Można stworzyć ogólną klasę wyjątku, po której dziedziczą bardziej szczegółowe klasy wyjątków, co pozwala na precyzyjniejsze zarządzanie błędami.

```python
class BladAplikacji(Exception):
    pass

class BladPolaczenia(BladAplikacji):
    pass

class BladAutoryzacji(BladAplikacji):
    pass

try:
    raise BladPolaczenia("Błąd podczas łączenia z serwerem.")
except BladAplikacji as e:
    print(f"Błąd aplikacji: {e}")
```

`BladAplikacji` jest ogólnym wyjątkiem aplikacji, a `BladPolaczenia` i `BladAutoryzacji` dziedziczą po nim.

Blok except `BladAplikacji` przechwytuje każdy wyjątek pochodny od `BladAplikacji`, co daje elastyczność w obsłudze błędów.

???+ warning "Zapis `except Exception as e`"
    To samo dzieje się w podstawowym zapisie `except Exception as e:`, który przechwytuje wszystkie wyjątki. Tłumaczy to dlaczego wszystkie nasze wyjątki powinny dziedziczyć po klasie `Exception`.

## 📝 Zadania

1. Napisz program do rezerwacji miejsc w kinie, który:

    - Przechowuje stan miejsc (np. słownik lub macierz),
    - Umożliwia rezerwację miejsca (np. "A2") dla użytkownika (imię i nazwisko),
    - Umożliwia anulowanie rezerwacji.

    **Rezerwacja miejsca:**

    - Zgłoś wyjątek, gdy brak wolnych miejsc,
    - Zgłoś wyjątek, gdy miejsce jest już zarezerwowane,
    - Zgłoś wyjątek, gdy użytkownik ma już rezerwację (1 użytkownik = 1 miejsce),
    - W przeciwnym razie zarezerwuj miejsce.

    **Anulowanie rezerwacji:**

    - Anuluj tylko, gdy numer miejsca i użytkownik się zgadzają,
    - W przeciwnym razie zgłoś wyjątek.

???- tip "Propozycja organizacji modułów i klas"
    
    Utwórz moduł `python1course/zaj06/cinema.py` zawierający:
    
    - **Własne wyjątki** (dziedziczące po `Exception`):
        - `NoFreeSeatsError` - gdy brak wolnych miejsc,
        - `SeatOccupiedError` - gdy miejsce już zarezerwowane,
        - `UserAlreadyHasReservationError` - gdy użytkownik ma już rezerwację,
        - `InvalidCancellationError` - gdy anulowanie nie może być wykonane.
    
    - **Klasa `CinemaHall`**:
        - Przechowuje stan miejsc (np. słownik: `{"A1": "Jan Kowalski", "A2": None, ...}`) i inne atrybuty dot. numeru sali, seansu itp.,
        - Metoda `reserve(seat, user)` - rezerwuje miejsce (miejsce przydzielone automatycznie jak nie zostanie podane),
        - Metoda `cancel(seat, user)` - anuluje rezerwację.
    
    Przykładowe użycie możesz umieścić w pliku `main_zajecia06.py`.

???+ danger "Uwaga"
    Użyj zarówno obsługi wyjątków (`try/except`) jak i własnych wyjątków (klasy dziedziczące po `Exception`).