## Wyjątki

Są to błędy, które występują podczas wykonywania programu i mogą powodować jego nagłe zakończenie, jeśli nie są odpowiednio obsłużone.

Zarządzanie wyjątkami, czyli kontrola nad tym, jak program zachowuje się w sytuacji, gdy wystąpi błąd, obejmuje wykorzystanie słów kluczowych `try`, `except`, `else` i `finally` oraz ręczne wywoływanie wyjątków za pomocą `raise`.

---

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

Można obsłużyć wiele typów wyjątków osobno (jak wyżej) lub użyć bazowej klasy `Exception`, żeby złapać każdy błąd i przy okazji odczytać jego treść:

```python
try:
    liczba = int(input("Podaj liczbę: "))
    wynik = 10 / liczba
    print(f"Wynik: {wynik}")
except Exception as e:
    print(f"Wystąpił błąd: {e}")
```

---

## Dodatkowe bloki `else` i `finally`

- Kod w bloku `else` wykona się tylko wtedy, gdy **żaden wyjątek nie wystąpił** w bloku `try`.
- Blok `finally` wykona się **zawsze**, niezależnie od tego, czy wyjątek wystąpił. Używany głównie do zwalniania zasobów (zamykanie plików, połączeń itp.).

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
    print("Blok finally wykonuje się zawsze.")
```

---

## Ręczne wywoływanie wyjątków - `raise`

Funkcje powinny aktywnie zgłaszać wyjątki, gdy otrzymują niepoprawne dane - zamiast cicho zwracać błędne wyniki. To właśnie widziałeś już w sekcji o docstringach (`raise ValueError`).

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

Większość wyjątków to klasy wbudowane dostępne w standardowej bibliotece - pełna lista w [dokumentacji](https://docs.python.org/3/library/exceptions.html#).

??? - tip "Najczęściej wykorzystywane wyjątki"
    - `ValueError` - argument ma poprawny typ, ale niepoprawną wartość.
    - `TypeError` - argument jest niepoprawnego typu.
    - `IndexError` - indeks jest poza zakresem listy, krotki itp.
    - `KeyError` - klucz nie istnieje w słowniku.
    - `AttributeError` - obiekt nie ma określonego atrybutu.
    - `FileNotFoundError` - określony plik nie istnieje.
    - `ZeroDivisionError` - próba dzielenia przez zero.
    - `PermissionError` - brak uprawnień do wykonania operacji.
    - `RuntimeError` - wyjątek ogólny, gdy żaden inny nie pasuje.
    - `NotImplementedError` - metoda zamierzona do implementacji w przyszłości.
    - `OverflowError` - wynik operacji matematycznej zbyt duży do przetworzenia.
    - `ImportError` - nie udało się zaimportować modułu lub elementu.
    - `AssertionError` - sprawdzenie `assert` zakończyło się niepowodzeniem.

---

## Tworzenie własnych wyjątków

!!! info "Wymaga znajomości klas"
    Ta sekcja korzysta z dziedziczenia klas - mechanizmu omawianego w późniejszych zajęciach z programowania obiektowego. Możesz ją teraz przejrzeć poglądowo i wrócić po przerobieniu OOP.

Własne wyjątki tworzy się przez zdefiniowanie nowej klasy dziedziczącej po `Exception`. Pozwala to tworzyć błędy dopasowane do konkretnego kontekstu aplikacji.

??? - note "Prosty przykład"
    ```python
    class BrakSrodkowError(Exception):
        """Wyjątek sygnalizujący brak wystarczających środków na koncie."""
        pass

    def wyplac(saldo, kwota):
        if kwota > saldo:
            raise BrakSrodkowError(
                f"Brak środków: saldo {saldo} zł, potrzebne {kwota} zł"
            )
        return saldo - kwota

    try:
        wyplac(100, 150)
    except BrakSrodkowError as e:
        print(f"Błąd: {e}")
    ```

??? - note "Bardziej rozbudowany przykład z atrybutami"
    ```python
    class BladZakresu(Exception):
        def __init__(self, wartosc, zakres_min, zakres_max):
            super().__init__(
                f"Wartość {wartosc} jest poza zakresem ({zakres_min}-{zakres_max})."
            )
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
        print(f"Wartość: {e.wartosc}, zakres: {e.zakres_min}-{e.zakres_max}")
    ```

### Hierarchia wyjątków

Własne wyjątki można układać w hierarchię - ogólna klasa bazowa i bardziej szczegółowe klasy pochodne:

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
    print(f"Błąd aplikacji: {e}")  # łapie oba typy pochodne
```

???+ warning "Zapis `except Exception as e`"
    `except Exception as e` przechwytuje **wszystkie** wyjątki, bo wszystkie wbudowane wyjątki dziedziczą po `Exception`. Dlatego własne wyjątki też powinny po niej dziedziczyć.

---

## 📝 Zadania

Stwórz plik `python1course/zaj02/wyjatki.py` i wykonaj w nim poniższe zadania.

1. Napisz funkcję `konwertuj_na_int(tekst)`, która próbuje zamienić podany ciąg znaków na liczbę całkowitą. Jeśli się nie uda, powinna wypisać czytelny komunikat o błędzie i zwrócić `None`. Przetestuj ją na kilku przypadkach: `"42"`, `"abc"`, `"3.14"`, `""`.

2. Napisz funkcję `oblicz_bmi(masa, wzrost)`, która:
    - Zgłasza `TypeError`, jeśli któryś z argumentów nie jest liczbą,
    - Zgłasza `ValueError`, jeśli masa ≤ 0 lub wzrost ≤ 0,
    - W pozostałych przypadkach zwraca wynik (`masa / wzrost ** 2`).

    Napisz kod wywołujący, który testuje wszystkie przypadki błędów z użyciem `try/except`.

3. Napisz funkcję `wczytaj_plik(sciezka)`, która otwiera plik tekstowy i zwraca jego zawartość jako string. Użyj bloku `with open()`. Obsłuż wyjątki `FileNotFoundError` i `PermissionError` - w obu przypadkach wypisz czytelny komunikat i zwróć `None`.

4. Napisz funkcję `polacz_z_baza(host)`, która symuluje nawiązywanie połączenia z bazą danych. Użyj bloku `finally`, żeby zawsze wypisać komunikat o zakończeniu próby połączenia - niezależnie od tego, czy się powiodła. Zasymuluj błąd dla wybranych hostów (np. `raise ConnectionError` gdy `host == "zly-host"`).

    !!! tip "Kiedy `finally` ma sens?"
        `with` rozwiązuje problem zamykania plików i innych zasobów obsługujących protokół kontekstu. `finally` przydaje się wszędzie tam, gdzie tego protokołu nie ma - np. połączenia sieciowe, logi, liczniki prób, zwalnianie blokad.

5. Napisz funkcję `pobierz_wartosc(slownik, klucz)`, która zwraca wartość ze słownika dla podanego klucza. Jeśli klucz nie istnieje, nie powinna rzucić `KeyError` - zamiast tego niech zgłosi `ValueError` z komunikatem wskazującym, jakiego klucza brakuje i jakie klucze są dostępne.
