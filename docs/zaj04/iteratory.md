## Iteratory

Iterator to obiekt, który zwraca kolejne elementy **na żądanie**.
W Pythonie większość konstrukcji iteracyjnych (`for`, `list(...)`, `sum(...)`, `any(...)`) działa właśnie na iteratorach.

Najważniejsza idea: przetwarzamy elementy jeden po drugim, zamiast budować całą kolekcję naraz.

## Iterable vs iterator

To dwa różne pojęcia:

- **Iterable**: obiekt, po którym można iterować (np. `list`, `tuple`, `str`), czyli ma `__iter__()`.
- **Iterator**: obiekt, który pamięta aktualny stan iteracji i ma `__next__()`.

W praktyce:

- `iterable --> iter() --> iterator`
- `next(iterator)` zwraca kolejne elementy
- po wyczerpaniu danych `next(...)` zgłasza `StopIteration`

```python
liczby = [10, 20, 30]  # iterable
it = iter(liczby)      # iterator

print(next(it))  # 10
print(next(it))  # 20
print(next(it))  # 30
```

## Jak działa pętla `for` "pod maską"?

Pętla:

```python
for x in [1, 2, 3]:
    print(x)
```

jest koncepcyjnie równoważna:

```python
it = iter([1, 2, 3])
while True:
    try:
        x = next(it)
        print(x)
    except StopIteration:
        break
```

## Własny iterator (klasa)

Poniżej prosty iterator odliczający w dół:

```python
class CountdownIterator:
    def __init__(self, start):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current < 0:
            raise StopIteration
        value = self.current
        self.current -= 1
        return value


for number in CountdownIterator(3):
    print(number)  # 3, 2, 1, 0
```

## Kiedy iterator klasowy ma sens?

- gdy potrzebujesz pełnej kontroli nad stanem iteracji,
- gdy logika jest bardziej złożona niż jedno `yield`,
- gdy iterator ma mieć dodatkowe metody pomocnicze.

## Iterator vs generator

Generator to wygodny sposób tworzenia iteratora.
Oba podejścia robią podobną rzecz (zwracają kolejne elementy na żądanie), ale implementuje się je inaczej:

- **Iterator (klasa)** - samodzielnie implementujesz `__iter__()` i `__next__()`, więc masz pełną kontrolę nad stanem i logiką.
- **Generator (`yield`)** - Python automatycznie tworzy obiekt iteratora, a stan jest zapamiętywany między kolejnymi `yield`.

```python
# Generator:
def countdown_generator(start):
    while start >= 0:
        yield start
        start -= 1
```

### Dlaczego robimy oba podejścia?

Wcześniej (w `zaj02/generatory.md`) ciąg Fibonacciego był realizowany przez **generator**.
Teraz implementujemy ten sam problem jako **własny iterator klasowy** `FibonacciIterator(max_elements)`, żeby przećwiczyć protokół iteratora (`__iter__`, `__next__`, `StopIteration`) i zobaczyć różnicę w podejściu.

## 📝 Zadania

W ramach nowego modułu `python1course.zaj04.fibonacci`:

1. Stwórz własny iterator (klasę) `FibonacciIterator(max_elements)`, który generuje ciąg liczb Fibonacciego.
   Ciąg Fibonacciego zaczyna się od `0, 1`, a każda kolejna liczba to suma dwóch poprzednich.

2. Obsłuż przypadki brzegowe:
    - `max_elements = 0` (brak elementów),
    - `max_elements = 1` (tylko `0`),
    - niepoprawna wartość (np. liczba ujemna) - rzuć `ValueError`.

3. Dodaj krótki kod porównawczy:
    - uruchomienie wersji iteratorowej (to zadanie),
    - uruchomienie wersji generatorowej z wcześniejszych zajęć (`zaj02`),
    - porównanie czy obie dają ten sam wynik dla np. 10 elementów.

Kod wykonawczy umieść w głównym folderze projektu, np. `main_zajecia04.py`.

## 📝 Zadanie integracyjne - iterator w projekcie z `zaj03`

W `zaj03` klasa `IncidentQueue` ma już zaimplementowane `__iter__()` i `__next__()` - działa, ale ma pewną wadę projektową.

Zmodyfikuj `IncidentQueue` tak, żeby zamiast tego implementowała własny, klasowy iterator:

1. Stwórz klasę `IncidentQueueIterator` z metodami `__iter__()` i `__next__()`, która iteruje po incydentach z kolejki.
2. Zmień `IncidentQueue.__iter__()` tak, żeby zwracała instancję `IncidentQueueIterator`.
3. Upewnij się, że zewnętrzny kod korzystający z `for incident in queue:` działa identycznie jak przed zmianą.

??? - warning "Jaki problem ma obecna implementacja?"
    Wróć do rozróżnienia z początku tego tematu: **iterable** to obiekt po którym można iterować, **iterator** to obiekt który pamięta stan iteracji. To dwa różne byty.

    Aktualna `IncidentQueue` jest jednocześnie iterable i iteratorem - ma `__iter__` zwracający `self` **oraz** `__next__`:

    ```python
    def __iter__(self):
        self._index = 0  # stan na obiekcie kolejki!
        return self      # kolejka zwraca samą siebie jako iterator

    def __next__(self):
        ...
    ```

    Innymi słowy: `IncidentQueue` jest iterable i iteratorem w jednym. Taki obiekt może istnieć tylko w **jednej iteracji naraz** - bo ma tylko jeden `_index`.

    Przy jednej pętli działa poprawnie. Problem pojawia się przy zagnieżdżonych pętlach:

    ```python
    for outer in queue:
        for inner in queue:  # to wywołuje queue.__iter__() i resetuje _index = 0
            print(inner)     # inner iteruje od początku przy każdym obrocie outer
        # outer nigdy nie dojdzie do kolejnych elementów - _index jest cały czas zerowany
    ```

    Zagnieżdżona iteracja jest rzadka, ale realna - np. gdy porównujesz każdy incydent z każdym innym.

    Po refaktoryzacji na `IncidentQueueIterator` każda pętla dostaje **własną instancję iteratora** z własnym `_index`, więc działają niezależnie:

    ```python
    def __iter__(self):
        return IncidentQueueIterator(self.__queue)  # nowy obiekt z własnym stanem
    ```

    ```python
    for outer in queue:
        for inner in queue:   # tworzy nowy IncidentQueueIterator, nie resetuje outer
            print(inner)      # działa poprawnie
    ```
