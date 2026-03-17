## Funkcje jako obiekty pierwszej klasy

W Pythonie funkcje są **pełnoprawnymi obiektami** - tak samo jak liczby, listy czy słowniki. Oznacza to, że można je:

- przypisywać do zmiennych,
- przechowywać w kolekcjach (listach, słownikach),
- przekazywać jako argumenty do innych funkcji,
- zwracać z innych funkcji.

### Przypisanie funkcji do zmiennej

```python
def powitaj(imie):
    return f"Cześć, {imie}!"

przywitaj = powitaj  # przypisanie referencji - bez nawiasów!
print(przywitaj("Ania"))   # Cześć, Ania!
print(powitaj is przywitaj)  # True - obie zmienne wskazują na ten sam obiekt
```

### Funkcja jako argument

Możemy przekazać funkcję do innej funkcji, tak jak każdą inną wartość:

```python
def zastosuj(funkcja, wartosc):
    return funkcja(wartosc)

def podwoj(x):
    return x * 2

def kwadrat(x):
    return x ** 2

print(zastosuj(podwoj, 5))   # 10
print(zastosuj(kwadrat, 5))  # 25
```

To wzorzec powszechnie spotykany w bibliotekach - np. `sorted(lista, key=...)` czy `map(funkcja, iterable)`.

### Funkcje w kolekcjach

```python
operacje = {
    "dodaj":   lambda a, b: a + b,
    "odejmij": lambda a, b: a - b,
    "pomnoz":  lambda a, b: a * b,
}

nazwa = "dodaj"
print(operacje[nazwa](3, 4))  # 7
```

---

## Zagnieżdżone funkcje i domknięcia (closures)

Funkcja może być zdefiniowana wewnątrz innej funkcji. Taka **funkcja zagnieżdżona** ma dostęp do zmiennych z zakresu funkcji zewnętrznej - nawet po zakończeniu jej wykonania. Ten mechanizm nazywamy **domknięciem** (ang. *closure*).

```python
def stworz_mnoznik(n):
    def mnoz(x):
        return x * n  # n pochodzi z zewnętrznej funkcji i jest "zapamiętane"
    return mnoz

razy3 = stworz_mnoznik(3)
razy5 = stworz_mnoznik(5)

print(razy3(10))  # 30
print(razy5(10))  # 50
```

`razy3` i `razy5` to dwa osobne domknięcia - każde „pamięta" swoją wartość `n`. Można to potwierdzić:

```python
print(razy3.__closure__[0].cell_contents)  # 3
print(razy5.__closure__[0].cell_contents)  # 5
```

### Praktyczne zastosowanie: fabryki funkcji

Domknięcia pozwalają tworzyć **sparametryzowane funkcje** bez powtarzania kodu:

```python
def stworz_walidator(min_val, max_val):
    def waliduj(x):
        return min_val <= x <= max_val
    return waliduj

ocena_szkolna = stworz_walidator(1, 6)
temperatura = stworz_walidator(-50, 60)

print(ocena_szkolna(5))   # True
print(ocena_szkolna(7))   # False
print(temperatura(100))   # False
```

???- note "Kiedy domknięcie, kiedy klasa?"
    Domknięcia są lżejszą alternatywą dla klas z jedną metodą (`__call__`). Jeśli potrzebujesz przechować jeden lub dwa elementy stanu i jednej operacji - domknięcie jest czytelniejsze. Gdy stan jest bardziej złożony lub potrzebujesz wielu metod - sięgnij po klasę.

---

## 📝 Zadania

Stwórz plik `python1course/zaj02/funkcje_jako_obiekty.py` i wykonaj w nim poniższe zadania.

1. Napisz funkcję `zastosuj_do_listy(funkcja, lista)`, która zwraca nową listę z wynikami zastosowania `funkcja` do każdego elementu. Nie używaj `map()` - zaimplementuj to ręcznie pętlą. Przetestuj z co najmniej trzema różnymi funkcjami (np. `str.upper`, własna funkcja, lambda).

2. Masz słownik operacji matematycznych:
    ```python
    operacje = {"dodaj": ..., "odejmij": ..., "pomnoz": ..., "podziel": ...}
    ```
    Uzupełnij go lambdami. Następnie napisz funkcję `kalkulator(a, op, b)`, która pobiera dwie liczby i nazwę operacji (string), wykonuje ją i zwraca wynik. Obsłuż przypadek, gdy podana operacja nie istnieje.

3. Napisz funkcję `stworz_potege(n)`, która zwraca funkcję podnoszącą liczbę do potęgi `n`. Stwórz na jej podstawie `kwadrat` i `szescian`, a następnie przetestuj je.

    ```python
    kwadrat  = stworz_potege(2)
    szescian = stworz_potege(3)
    print(kwadrat(4))   # 16
    print(szescian(3))  # 27
    ```

4. Napisz funkcję `stworz_akumulator(poczatkowa=0)`, która korzysta z domknięcia do przechowywania bieżącej sumy. Funkcja powinna zwracać funkcję wewnętrzną `dodaj(n)`, która dodaje `n` do sumy i zwraca aktualną wartość.

    ```python
    akumulator = stworz_akumulator(10)
    print(akumulator(5))   # 15
    print(akumulator(3))   # 18
    print(akumulator(2))   # 20
    ```
