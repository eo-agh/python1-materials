
## Generatory

Generatory to specjalne obiekty, które generują wyniki na żądanie - jeden po drugim - zamiast tworzyć i przechowywać całą serię wyników od razu. Dzięki temu generatory są wydajniejsze w pracy z dużymi zestawami danych, ponieważ zużywają mniej pamięci.

Generatory działają na zasadzie *lazy evaluation*, co oznacza, że nie obliczają wszystkich wyników od razu, lecz tylko wtedy, gdy są potrzebne.

### Funkcje generatorów

Tworzymy je tak samo jak zwykłe funkcje, ale zamiast `return` używamy `yield`, który zwraca wartość, a następnie "zawiesza" działanie funkcji. Gdy po raz kolejny wywołujemy `next()` na generatorze, funkcja kontynuuje od miejsca, w którym ostatnio się zatrzymała.

```python
def count_up_to(n):
    count = 1
    while count <= n:
        yield count  # Zwraca wartość, ale kontynuuje działanie po wywołaniu next()
        count += 1

# Tworzymy generator
counter = count_up_to(5)

print(next(counter))  # 1
print(next(counter))  # 2
# Możemy też przeiterować przez cały generator w pętli
for number in counter:
    print(number)  #  3, 4, 5
```

### Wyrażenia generatorów

To bardziej zwięzły sposób na tworzenie generatorów, podobny do list składanych. Różnią się jednak nawiasami: zamiast nawiasów kwadratowych `[]` (jak w listach składanych) używamy nawiasów okrągłych `()`.

```python
gen = (x ** 2 for x in range(5))

# Kolejne wartości możemy uzyskać wywołując next()
print(next(gen))  # 0
print(next(gen))  # 1

# Lub iterując po generatorze w pętli
for value in gen:
    print(value)  # 4, 9, 16
```

??? - notes "Czym generatory różnią się od iteratorów?"
    Różnią się w sposobie ich tworzenia:

    - **Iteratory** mogą być tworzone z dowolnej kolekcji iterowalnej (np. listy, krotki, słownika) przez wywołanie `iter()`, albo przez definiowanie klasy z metodą `__iter__()` i `__next__()`.

    - **Generatory** są tworzone przy użyciu funkcji z `yield` lub jako wyrażenia generatorów. Są to specjalne, uproszczone iteratory, które automatycznie obsługują stan i logikę `__next__()`.

    Różnią się w sposobie przechowywania stanu:

    - **Iteratory** muszą ręcznie przechowywać stan między kolejnymi wywołaniami `__next__()`, co wymaga więcej kodu i zarządzania.

    - **Generatory** automatycznie zapamiętują stan wewnętrzny przy każdym wywołaniu `yield`, dzięki czemu są prostsze do implementacji.

    Różnią się w sposobie przechodzenia przez dane:

    - **Iteratory** zazwyczaj są jednorazowe, ale jeśli iterator działa na strukturze danych, jak lista, można go ponownie utworzyć przez `iter()`.

    - **Generatory** są jednorazowego użytku - po przeiterowaniu przez wszystkie wartości kończą się, i nie można ich wznowić od początku.

    Przykład:

    ```python
    # Niestandardowy iterator, który iteruje od 1 do podanej liczby:
    class CountUpTo:
        def __init__(self, max_value):
            self.max_value = max_value
            self.current = 1

        def __iter__(self):
            return self

        def __next__(self):
            if self.current > self.max_value:
                raise StopIteration
            else:
                self.current += 1
                return self.current - 1

    counter = CountUpTo(3)
    for number in counter:
        print(number)
    ```

    ```python
    # Analogiczny generator z yield
    def count_up_to(max_value):
        current = 1
        while current <= max_value:
            yield current
            current += 1

    for number in count_up_to(3):
        print(number)
    ```

## 📝 Zadania

W ramach modułu `python1course.zaj05.generator`:

1. Napisz generator, który iteracyjnie zwraca nazwy dni tygodnia: od poniedziałku do niedzieli. Następnie, użyj tego generatora w pętli, aby wyświetlić każdy dzień tygodnia. Dodatkowo, zademonstruj, jak można użyć tego generatora do pobrania tylko pierwszych trzech dni tygodnia bez konieczności iterowania przez cały tydzień.

Kod wykonawczy umieść w tym samym pliku, co poprzednie zadania.

???+ tip
    To zadanie można wykonać zarówno funkcją jak i wyrażeniem.
