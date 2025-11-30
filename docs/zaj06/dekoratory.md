Dekoratory to specjalne funkcje, które pozwalają na rozszerzanie funkcjonalności innych funkcji lub klas bez modyfikacji ich kodu. Umożliwiają one dodanie dodatkowego działania przed lub po wykonaniu oryginalnej funkcji, co jest przydatne w wielu scenariuszach, takich jak logowanie, uwierzytelnianie, zarządzanie czasem wykonania funkcji itp.

## Podstawowe działanie

Dekorator to funkcja, która przyjmuje inną funkcję jako argument i zwraca nową funkcję, która rozszerza działanie oryginalnej. Dzięki temu możemy dodać dodatkową funkcjonalność bez modyfikowania ciała dekorowanej funkcji.

```python
def przykladowy_dekorator(funkcja):
    def wrapper():
        print("Dodatkowa funkcjonalność przed wywołaniem funkcji.")
        funkcja()
        print("Dodatkowa funkcjonalność po wywołaniu funkcji.")
    return wrapper
```

## Dekorowanie funkcji

Aby zastosować dekorator, można go „przykleić” do funkcji za pomocą znaku `@`.

```python
@przykladowy_dekorator
def moja_funkcja():
    print("Oryginalna funkcja.")

moja_funkcja()
```

### Dekorator z argumentami funkcji

Aby stworzyć dekorator, który obsłuży funkcje z argumentami, wystarczy zmodyfikować wewnętrzną funkcję `wrapper`, aby przyjmowała dowolne argumenty i przekazywała je do oryginalnej funkcji.

```python
def przykladowy_dekorator(funkcja):
    def wrapper(*args, **kwargs):
        print("Przed wywołaniem funkcji.")
        wynik = funkcja(*args, **kwargs)
        print("Po wywołaniu funkcji.")
        return wynik
    return wrapper

@przykladowy_dekorator
def dodaj(a, b):
    return a + b

print(dodaj(3, 5))
```

## Dekorowanie klas

Dekoratory mogą być również stosowane do całych klas, modyfikując ich zachowanie lub dodając dodatkowe funkcje. Dekorator klasy przyjmuje klasę jako argument i zwraca zmodyfikowaną wersję tej klasy.

```python
def dodaj_metode(cls):
    cls.nowa_metoda = lambda self: "Nowa metoda w klasie!"
    return cls

@dodaj_metode
class MojaKlasa:
    def __init__(self):
        self.wartosc = 42

# Tworzenie instancji i użycie dodanej metody
obiekt = MojaKlasa()
print(obiekt.nowa_metoda())  # Nowa metoda w klasie!
```

## Typowe zastosowania dekoratorów

??? - note "Logowanie"
    Logowanie - automatyczne logowanie wywołań funkcji.

    ```python
    def loguj(funkcja):
        def wrapper(*args, **kwargs):
            print(f"Wywołano funkcję: {funkcja.__name__} z argumentami: {args}, {kwargs}")
            return funkcja(*args, **kwargs)
        return wrapper
    ```

??? - note "Uwierzytelnianie"
    Uwierzytelnianie – sprawdzanie uprawnień przed wykonaniem funkcji.

    ```python
    def wymaga_uprawnien(funkcja):
        def wrapper(*args, **kwargs):
            if not uzytkownik_ma_uprawnienia():
                raise PermissionError("Brak uprawnień!")
            return funkcja(*args, **kwargs)
        return wrapper
    ```

??? - note "Mierzenie czasu wykonania funkcji"
    Czas wykonania funkcji – pomiar czasu, jaki zajmuje wykonanie funkcji.

    ```python
    import time

    def zmierz_czas(funkcja):
        def wrapper(*args, **kwargs):
            start = time.time()
            wynik = funkcja(*args, **kwargs)
            end = time.time()
            print(f"Czas wykonania: {end - start} sekund")
            return wynik
        return wrapper
    ```

## 📝 Zadania

Stwórz program, w którym zdefiniujesz dekorator oraz przedstawisz jego działanie.

Ma to być dekorator, który zmierzy i wyświetli czas wykonania dekorowanej funkcji. Dekorator powinien przyjmować jeden argument, który określa jednostkę czasu (sekundy lub mikrosekundy) dla wyniku.

Wymagania:

- Ma przyjmować argument o nazwie unit o wartości 'seconds' lub 'microseconds', określający jednostkę, w której ma być wyświetlony czas wykonania funkcji.
- Ma mierzyć czas wykonania dekorowanej funkcji.
- Ma wyświetlić czas wykonania funkcji w wybranej jednostce po zakończeniu jej wykonania.

???+ danger "Uwaga"
    Dekorator jak i funkcję przykładową umieść w `python1course/zaj06/dekorator.py`, kod wykonawczy w `main_zajecia06.py`.