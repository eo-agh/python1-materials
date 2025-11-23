## Iteratory

Iterator to obiekt w Pythonie, który pozwala na sekwencyjne przetwarzanie elementów kolekcji (np. list, krotek, zbiorów) **bez załadowania całej struktury do pamięci**. Iteratory umożliwiają przechodzenie po elementach jeden po drugim, co jest efektywne, szczególnie przy pracy z dużymi zbiorami danych.

Każda pętla `for` działa na iteratorach w tle.

??? - note "Jak działają iteratory?"
    - Każdy obiekt, który ma metodę `__iter__()` i `__next__()`, jest iteratorem.
    - Metoda `__iter__()` zwraca iterator, a `__next__()` zwraca kolejny element. Gdy elementów zabraknie, zgłaszany jest wyjątek `StopIteration`.

??? - note "Po co nam iteratory?"
    - Efektywność pamięciowa: Przetwarzają elementy na bieżąco, nie muszą trzymać całej kolekcji w pamięci.
    - Niekończące się sekwencje: Można tworzyć iteratory, które generują nieskończone sekwencje danych, np. liczby losowe.

```python
liczby = [1, 2, 3]
it = iter(liczby)
print(next(it))
print(next(it))
print(next(it))
```

## 📝 Zadania

W ramach nowego modułu `python1course.zaj05.fibonacci`:

1. Stwórz własny iterator (klasę) `FibonacciIterator(max_elements)`, który generuje ciąg liczb Fibonacciego. Ciąg Fibonacciego to sekwencja, w której każda kolejna liczba jest sumą dwóch poprzednich, a zaczyna się od 0 i 1.

Kod wykonawczy umieść w głównym folderze projektu, może być `main_zajecia05.py`.
