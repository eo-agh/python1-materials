## Zakres zmiennych (scope)

Każda zmienna w Pythonie istnieje w określonym **zakresie** (ang. *scope*) - obszarze kodu, w którym jest dostępna. Python wyszukuje nazwy zmiennych według reguły **LEGB**:

1. **L**ocal - wnętrze bieżącej funkcji,
2. **E**nclosing - funkcja zewnętrzna (dla funkcji zagnieżdżonych),
3. **G**lobal - poziom modułu (skryptu),
4. **B**uilt-in - wbudowane nazwy Pythona, np. `print`, `len`.

```python
x = "globalny"

def zewnetrzna():
    x = "zewnętrzny"

    def wewnetrzna():
        x = "lokalny"
        print(x)  # lokalny

    wewnetrzna()
    print(x)  # zewnętrzny

zewnetrzna()
print(x)  # globalny
```

Python szuka `x` zawsze od najwęższego zakresu ku najszerszemu i zatrzymuje się przy pierwszym trafieniu.

---

## Słowo kluczowe `global`

Wewnątrz funkcji przypisanie do nazwy **zawsze tworzy nową zmienną lokalną**, nawet jeśli istnieje zmienna globalna o tej samej nazwie. Żeby zmodyfikować zmienną globalną, trzeba ją jawnie zadeklarować słowem `global`.

```python
licznik = 0

def zwieksz():
    licznik += 1  # UnboundLocalError! Python widzi przypisanie i traktuje licznik jako lokalny

zwieksz()
```

Poprawne rozwiązanie:

```python
licznik = 0

def zwieksz():
    global licznik
    licznik += 1

zwieksz()
zwieksz()
print(licznik)  # 2
```

!!! warning "Ogranicz użycie `global`"
    Zmienne globalne utrudniają testowanie i śledzenie przepływu danych. Zamiast nich lepiej przekazywać wartości przez argumenty i zwracać przez `return`. `global` może być uzasadniony np. dla liczników lub flag w małych skryptach.

---

## Słowo kluczowe `nonlocal`

`nonlocal` działa jak `global`, ale dotyczy zmiennej z **najbliższej otaczającej funkcji** (nie globalnej). Jest niezbędne, gdy chcemy zmodyfikować zmienną z funkcji zewnętrznej wewnątrz funkcji zagnieżdżonej.

```python
def zewnetrzna():
    licznik = 0

    def wewnetrzna():
        nonlocal licznik
        licznik += 1

    wewnetrzna()
    wewnetrzna()
    print(licznik)  # 2

zewnetrzna()
```

Bez `nonlocal` przypisanie `licznik += 1` wewnątrz `wewnetrzna` rzuciłoby `UnboundLocalError`, bo Python potraktowałby `licznik` jako zmienną lokalną.

---

## 📝 Zadania

Stwórz plik `python1course/zaj02/zakres_zmiennych.py` i wykonaj w nim poniższe zadania.

1. Uruchom poniższy kod i wyjaśnij wynik. Następnie popraw go tak, żeby funkcja `resetuj` rzeczywiście zerowała licznik globalny.

    ```python
    licznik = 10

    def resetuj():
        licznik = 0

    resetuj()
    print(licznik)
    ```

2. Napisz funkcję `stworz_licznik()`, która używa `nonlocal` do zarządzania wewnętrznym stanem. Funkcja powinna zwracać dwie inne funkcje: `zwieksz` i `pobierz`, które odpowiednio inkrementują i odczytują wewnętrzny licznik.

    Przykład użycia:
    ```python
    zwieksz, pobierz = stworz_licznik()
    zwieksz()
    zwieksz()
    zwieksz()
    print(pobierz())  # 3
    ```

3. Napisz funkcję `stworz_konto(saldo_poczatkowe)`, która zwraca trzy funkcje: `wplac(kwota)`, `wyplac(kwota)` i `sprawdz()`. Każda z nich operuje na wewnętrznym saldzie konta poprzez `nonlocal`. Funkcja `wyplac` powinna wypisać ostrzeżenie, jeśli saldo byłoby ujemne po wypłacie.

    ```python
    wplac, wyplac, sprawdz = stworz_konto(100)
    wplac(50)
    wyplac(30)
    print(sprawdz())  # 120
    wyplac(200)       # Ostrzeżenie: niewystarczające środki
    ```
