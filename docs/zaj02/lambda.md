## Funkcje anonimowe – lambda

Są to **krótkie, anonimowe funkcje**, które można definiować bez nadawania im nazwy. Służą one głównie do wykonywania prostych operacji, zwłaszcza wtedy, gdy funkcja jest potrzebna tylko w jednym, konkretnym miejscu. Zamiast stosować pełną definicję funkcji z def, używamy słowa kluczowego lambda, aby szybko stworzyć funkcję w jednej linii.

```python
# Tak wygląda standardowo zdefiniowana funkcja
def add(x, y):
    return x + y
print(add(3, 5))

# A tak lambda
add = lambda x, y: x + y
print(add(3, 5))
```

## 📝 Zadania

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