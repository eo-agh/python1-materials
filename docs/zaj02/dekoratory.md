## Dekoratory

Dekoratory to specjalne funkcje, które pozwalają na **rozszerzanie funkcjonalności innych funkcji bez modyfikacji ich kodu**. Umożliwiają dodanie dodatkowego działania przed lub po wykonaniu oryginalnej funkcji - np. logowanie, pomiar czasu, sprawdzanie uprawnień.

Dekorator to nic innego jak **funkcja przyjmująca funkcję jako argument i zwracająca nową funkcję** - czyli bezpośrednie zastosowanie domknięć.

---

## Podstawowe działanie

```python
def przykladowy_dekorator(funkcja):
    def wrapper():
        print("Przed wywołaniem funkcji.")
        funkcja()
        print("Po wywołaniu funkcji.")
    return wrapper
```

Możemy zastosować dekorator ręcznie:

```python
def moja_funkcja():
    print("Oryginalna funkcja.")

moja_funkcja = przykladowy_dekorator(moja_funkcja)
moja_funkcja()
```

## Składnia z `@`

Python oferuje skróconą składnię - znak `@` przyklejony bezpośrednio nad definicją funkcji robi dokładnie to samo co ręczne przypisanie powyżej:

```python
@przykladowy_dekorator
def moja_funkcja():
    print("Oryginalna funkcja.")

moja_funkcja()
```

---

## Dekorator obsługujący argumenty

Żeby dekorator działał z dowolną funkcją (niezależnie od jej argumentów), `wrapper` powinien przyjmować `*args` i `**kwargs` i przekazywać je dalej:

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

---

## Typowe zastosowania

??? - note "Logowanie"
    Automatyczne logowanie wywołań funkcji.

    ```python
    def loguj(funkcja):
        def wrapper(*args, **kwargs):
            print(f"Wywołano: {funkcja.__name__} | args={args}, kwargs={kwargs}")
            return funkcja(*args, **kwargs)
        return wrapper
    ```

??? - note "Uwierzytelnianie"
    Sprawdzanie uprawnień przed wykonaniem funkcji.

    ```python
    def wymaga_uprawnien(funkcja):
        def wrapper(*args, **kwargs):
            if not uzytkownik_ma_uprawnienia():
                raise PermissionError("Brak uprawnień!")
            return funkcja(*args, **kwargs)
        return wrapper
    ```

??? - note "Mierzenie czasu wykonania"
    Pomiar czasu wykonania dowolnej funkcji.

    ```python
    import time

    def zmierz_czas(funkcja):
        def wrapper(*args, **kwargs):
            start = time.time()
            wynik = funkcja(*args, **kwargs)
            koniec = time.time()
            print(f"Czas wykonania {funkcja.__name__}: {koniec - start:.4f} s")
            return wynik
        return wrapper
    ```

---

## Dekorowanie klas

Dekoratory można stosować również do całych klas - dekorator przyjmuje klasę jako argument i zwraca zmodyfikowaną wersję:

```python
def dodaj_metode(cls):
    cls.opis = lambda self: f"Obiekt klasy {cls.__name__}"
    return cls

@dodaj_metode
class MojaKlasa:
    def __init__(self):
        self.wartosc = 42

obiekt = MojaKlasa()
print(obiekt.opis())  # Obiekt klasy MojaKlasa
```

---

## 📝 Zadania

Na potrzeby tego rozdziału stwórz plik `python1course/zaj02/dekorator.py`, w którym zdefiniujesz dekorator. Kod wywołujący umieść w osobnym pliku `python1course/zaj02/main.py`.

Stwórz dekorator `zmierz_czas`, który zmierzy i wyświetli czas wykonania dekorowanej funkcji.

Wymagania:

- Dekorator przyjmuje jeden argument `unit` o wartości `'seconds'` lub `'microseconds'`, określający jednostkę wyświetlanego czasu.
- Mierzy czas wykonania dekorowanej funkcji.
- Wyświetla czas po zakończeniu jej wykonania.
- Działa zarówno z funkcjami jak i metodami klas (użyj `*args, **kwargs`).

Przykład użycia:

```python
@zmierz_czas(unit='microseconds')
def wolna_funkcja():
    import time
    time.sleep(0.1)

wolna_funkcja()
# Czas wykonania wolna_funkcja: 100523 µs
```

!!! tip "Wskazówka"
    Dekorator z argumentem ma trzy poziomy zagnieżdżenia: funkcja przyjmująca `unit` → funkcja przyjmująca `funkcja` → `wrapper`.
