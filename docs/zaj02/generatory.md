## Generatory

Generatory to specjalne obiekty, które generują wyniki **na żądanie** - jeden po drugim - zamiast tworzyć i przechowywać całą serię wyników od razu. Dzięki temu są wydajniejsze przy pracy z dużymi zestawami danych, ponieważ zużywają znacznie mniej pamięci.

Generatory działają na zasadzie **lazy evaluation** - nie obliczają wszystkich wyników z góry, lecz tylko wtedy, gdy są potrzebne.

---

## Funkcje generatorów

Tworzymy je tak samo jak zwykłe funkcje, ale zamiast `return` używamy `yield`. Słowo kluczowe `yield` zwraca wartość, a następnie **zawiesza** działanie funkcji - zapamiętując cały jej stan (zmienne lokalne, miejsce wykonania). Przy kolejnym wywołaniu `next()` funkcja wznawia działanie od miejsca, w którym się zatrzymała.

```python
def licz_do(n):
    count = 1
    while count <= n:
        yield count
        count += 1

counter = licz_do(5)

print(next(counter))  # 1
print(next(counter))  # 2

# Możemy też przeiterować przez resztę pętlą
for number in counter:
    print(number)  # 3, 4, 5
```

!!! warning "Generatory są jednorazowe"
    Po wyczerpaniu wszystkich wartości generator jest pusty - nie można go przewinąć do początku. Żeby przeiterować ponownie, trzeba stworzyć nowy obiekt generatora.

    ```python
    counter = licz_do(3)
    print(list(counter))  # [1, 2, 3]
    print(list(counter))  # [] - już wyczerpany
    ```

---

## Wyrażenia generatorów

Bardziej zwięzły sposób na tworzenie generatorów, analogiczny do list składanych. Różnica: nawiasy okrągłe `()` zamiast kwadratowych `[]`. W przeciwieństwie do listy składanej - **nie tworzy całej kolekcji w pamięci od razu**.

```python
# Lista składana - oblicza wszystkie wartości natychmiast
lista = [x ** 2 for x in range(1_000_000)]

# Wyrażenie generatora - oblicza wartości na żądanie
gen = (x ** 2 for x in range(1_000_000))
```

```python
gen = (x ** 2 for x in range(5))

print(next(gen))  # 0
print(next(gen))  # 1

for value in gen:
    print(value)  # 4, 9, 16
```

---

## Praktyczne zastosowanie - nieskończone sekwencje

Generator może generować wartości w nieskończoność, bo nie przechowuje ich wszystkich w pamięci:

```python
def kolejne_liczby(start=0):
    n = start
    while True:
        yield n
        n += 1

gen = kolejne_liczby(10)
print(next(gen))  # 10
print(next(gen))  # 11
print(next(gen))  # 12
# ... można wywoływać w nieskończoność
```

---

??? - note "Czym generatory różnią się od iteratorów?"
    Różnią się sposobem tworzenia:

    - **Iteratory** tworzy się z dowolnej kolekcji przez `iter()` lub definiując klasę z metodami `__iter__()` i `__next__()` (wymaga znajomości OOP).
    - **Generatory** tworzy się funkcją z `yield` lub wyrażeniem generatora - Python automatycznie obsługuje cały mechanizm stanu.

    Generatory są uproszczonymi iteratorami - robią to samo, ale wymagają znacznie mniej kodu. Iteratory (jako klasy) zostaną omówione po przerobieniu programowania obiektowego.

    ```python
    # Pełny iterator jako klasa (wymaga OOP):
    class LiczDo:
        def __init__(self, max_value):
            self.max_value = max_value
            self.current = 1

        def __iter__(self):
            return self

        def __next__(self):
            if self.current > self.max_value:
                raise StopIteration
            value = self.current
            self.current += 1
            return value

    # Odpowiednik jako generator (5 linii zamiast 15):
    def licz_do(max_value):
        current = 1
        while current <= max_value:
            yield current
            current += 1
    ```

---

## 📝 Zadania

Stwórz plik `python1course/zaj02/generatory.py` i wykonaj w nim poniższe zadania.

1. Napisz generator `dni_tygodnia()`, który zwraca nazwy dni tygodnia od poniedziałku do niedzieli. Następnie:
    - Użyj go w pętli `for`, żeby wypisać wszystkie dni,
    - Utwórz nowy obiekt generatora i użyj `next()`, żeby pobrać tylko pierwsze trzy dni bez iterowania przez cały tydzień.

    !!! tip
        To zadanie można wykonać zarówno funkcją z `yield`, jak i wyrażeniem generatora.

2. Napisz generator `fibonacci()`, który w nieskończoność zwraca kolejne liczby Fibonacciego (0, 1, 1, 2, 3, 5, 8, ...). Pobierz i wypisz pierwszych 10 wyrazów.

3. Napisz wyrażenie generatora, które z listy słów zwraca tylko te, które mają więcej niż 4 znaki, zamienione na wielkie litery. Przetestuj na liście:
    ```python
    slowa = ["python", "to", "swietny", "jezyk", "do", "nauki"]
    ```
    Porównaj zużycie pamięci z analogiczną listą składaną (możesz użyć modułu `sys` i `sys.getsizeof()`).
