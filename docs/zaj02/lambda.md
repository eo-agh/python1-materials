## Funkcje anonimowe - lambda

Są to **krótkie, anonimowe funkcje**, które można definiować bez nadawania im nazwy. Służą one głównie do wykonywania prostych operacji, zwłaszcza wtedy, gdy funkcja jest potrzebna tylko w jednym, konkretnym miejscu. Zamiast stosować pełną definicję funkcji z `def`, używamy słowa kluczowego `lambda`, aby szybko stworzyć funkcję w jednej linii.

### Podstawowa składnia

```python
# Tak wygląda standardowo zdefiniowana funkcja
def add(x, y):
    return x + y
print(add(3, 5))  # 8

# A tak lambda
add = lambda x, y: x + y
print(add(3, 5))  # 8
```

### Lambda z `sorted()`

```python
# Sortowanie po dowolnym kluczu
osoby = [
    {"imie": "Anna", "wiek": 30},
    {"imie": "Jan", "wiek": 25},
    {"imie": "Piotr", "wiek": 35}
]

# Sortowanie według wieku
posortowane = sorted(osoby, key=lambda x: x["wiek"])
print(posortowane)  # [{'imie': 'Jan', 'wiek': 25}, ...]
```

### Lambda z `filter()`

```python
liczby = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
parzyste = list(filter(lambda x: x % 2 == 0, liczby))
print(parzyste)  # [2, 4, 6, 8, 10]
```

### Lambda z `map()`

```python
liczby = [1, 2, 3, 4, 5]
kwadraty = list(map(lambda x: x ** 2, liczby))
print(kwadraty)  # [1, 4, 9, 16, 25]
```

### Lambda z max/min

```python
teksty = ["Python", "Java", "C++", "JavaScript"]
najdluzszy = max(teksty, key=lambda x: len(x))
print(najdluzszy)  # "JavaScript"

# Znalezienie największej wartości w liście słowników
osoby = [{"imie": "Anna", "wiek": 30}, {"imie": "Jan", "wiek": 25}]
najstarszy = max(osoby, key=lambda x: x["wiek"])
print(najstarszy)  # {'imie': 'Anna', 'wiek': 30}
```

### Wielowierszowe lambdy? Nie zawsze!

Lambda powinny być proste i jednolinijkowe:

```python
# ✅ Dobrze - prosta operacja
square = lambda x: x ** 2

# ❌ Nie rób tego - lambda nie powinna zawierać print
warto_wykorzystac_def = lambda x: print(x) or x
```

!!! tip "Kiedy używać lambda?"
    - Do prostych transformacji w `map()`, `filter()`, `sorted()`.
    - Gdy funkcja jest potrzebna tylko raz.
    - Do sortowania według niestandardowego klucza.
    
!!! warning "Kiedy NIE używać lambda?"
    - Jeśli logika jest skomplikowana - użyj `def`.
    - Gdy funkcja jest długa (więcej niż 1-2 operacje)
    - Gdy potrzebujesz dokumentacji (docstring).
    - Jeśli funkcja będzie używana wielokrotnie - nazwij ją.

## 📝 Zadania

Stwórz plik `python1course/zaj02/lambda.py` i wykonaj w nim poniższe zadania.

1. Masz daną listę słowników reprezentujących informacje o książkach w bibliotece. Każdy słownik zawiera klucze: `tytul`, `autor` oraz `rok_wydania`.

    ```python
    ksiazki = [
        {"tytul": "Władca Pierścieni", "autor": "J.R.R. Tolkien", "rok_wydania": 1954},
        {"tytul": "Harry Potter i Kamień Filozoficzny", "autor": "J.K. Rowling", "rok_wydania": 1997},
        {"tytul": "Duma i uprzedzenie", "autor": "Jane Austen", "rok_wydania": 1813},
        {"tytul": "Rok 1984", "autor": "George Orwell", "rok_wydania": 1949},
        {"tytul": "Zbrodnia i kara", "autor": "Fiodor Dostojewski", "rok_wydania": 1866},
        {"tytul": "Mistrz i Małgorzata", "autor": "Michaił Bułhakow", "rok_wydania": 1967},
        {"tytul": "Hobbit", "autor": "J.R.R. Tolkien", "rok_wydania": 1937},
        {"tytul": "Sto lat samotności", "autor": "Gabriel García Márquez", "rok_wydania": 1967},
        {"tytul": "Imię róży", "autor": "Umberto Eco", "rok_wydania": 1980},
        {"tytul": "Solaris", "autor": "Stanisław Lem", "rok_wydania": 1961},
    ]
    ```

    Twoim zadaniem jest napisanie kodu, który wykonuje następujące operacje przy użyciu funkcji lambda:

    - Sortowanie książek według roku wydania: Posortuj listę książek w kolejności rosnącej według roku ich wydania.

    - Filtracja książek wydanych po 2000 roku: Utwórz nową listę zawierającą tylko te książki, które zostały wydane po roku 2000.

    - Transformacja listy do listy tytułów: Przekształć oryginalną listę książek w listę zawierającą tylko tytuły książek.

    Wykorzystaj funkcje `sorted()`, `filter()` oraz `map()` w połączeniu z funkcjami lambda do realizacji zadania.

2. Stwórz funkcję lambda, która:

    - Przyjmuje dwa argumenty: `x` i `y`,
    - Zwraca `True` jeśli oba są parzyste,
    - Zwraca `False` w przeciwnym razie.

    Przetestuj ją na kilku parach wartości.

3. Masz listę uczniów: `["Anna Kowalska", "Jan Nowak", "Piotr Wiśniewski", "Ewa Zielińska"]`. Napisz kod, który:

    - Uszereguje uczniów alfabetycznie według nazwiska (drugiego słowa),
    - Stworzy listę, która zawiera tylko inicjały każdego ucznia (np. "AK" dla "Anna Kowalska").

    Użyj `sorted()` i `map()` z funkcjami lambda.

    !!! tip "Wskazówka"
        Wykorzystaj metodę `.split()` do podziału nazwiska.
