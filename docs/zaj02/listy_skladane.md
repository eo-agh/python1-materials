## Listy składane (list comprehension)

Lista składana to wyrażenie, które pozwala na tworzenie nowych list w zwięzły sposób, zwykle z użyciem pętli. To elegancka składnia w Pythonie, która łączy w sobie mapowanie i filtrowanie elementów.

### Podstawowa składnia

```python
kwadraty = [x ** 2 for x in range(6)]
print(kwadraty)  # [0, 1, 4, 9, 16, 25]
```

### Lista składana z warunkiem

Można dodać warunek filtrujący elementy:

```python
liczby = [1, 2, 3, 4, 5, 6]
parzyste = [liczba for liczba in liczby if liczba % 2 == 0]
print(parzyste)  # [2, 4, 6]
```

### Lista składana z wyrażeniem warunkowym (ternary operator)

```python
liczby = [1, 2, 3, 4, 5, 6]
parzystosc = ['parzysta' if liczba % 2 == 0 else 'nieparzysta' for liczba in liczby]
print(parzystosc)  # ['nieparzysta', 'parzysta', 'nieparzysta', 'parzysta', 'nieparzysta', 'parzysta']
```

### Zagnieżdżone pętle

Listy składane mogą zawierać zagnieżdżone pętle, pozwalając na generowanie kombinacji elementów:

```python
# Iloczyn kartezjański (każdy z każdym)
pary = [(x, y) for x in [1, 2, 3] for y in [10, 20]]
print(pary)  # [(1, 10), (1, 20), (2, 10), (2, 20), (3, 10), (3, 20)]

# Flattenowanie listy (spłaszczanie)
lista_list = [[1, 2, 3], [4, 5], [6, 7, 8, 9]]
spłaszczona = [element for podlista in lista_list for element in podlista]
print(spłaszczona)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### Zagnieżdżone listy składane

Można tworzyć struktury wielowymiarowe:

```python
# Macierz 3x3
macierz = [[j for j in range(3)] for i in range(3)]
print(macierz)  # [[0, 1, 2], [0, 1, 2], [0, 1, 2]]
```

### Porównanie z tradycyjnym podejściem

```python
# Tradycyjne podejście
kwadraty = []
for x in range(6):
    kwadraty.append(x ** 2)

# Lista składana - zwięźlejsza i czytelniejsza
kwadraty = [x ** 2 for x in range(6)]
```

!!! tip "Zalety list składanych"
    - **Zwięzłość** - mniej kodu do napisania
    - **Czytelność** - łatwiej zrozumieć intencję
    - **Wydajność** - często szybsze niż tradycyjne pętle
    - **Ekspresywność** - Pythonowy, idiomatyczny sposób

!!! warning "Uwaga"
    Listy składane z zagnieżdżonymi pętlami i warunkami mogą być trudne do odczytania. Jeśli lista składana staje się zbyt skomplikowana, warto rozważyć powrót do tradycyjnego podejścia lub podział na kilka mniejszych list składanych.

## 📝 Zadania

1. Stwórz listę zawierającą kwadraty liczb od 1 do 20, które są podzielne przez 3. 

2. Masz dwie listy `imiona = ['Anna', 'Jan', 'Ewa', 'Piotr']` i `oceny = [5, 4, 3, 5]`. Stwórz listę słowników, gdzie każdy słownik zawiera pary klucz-wartość dla imienia i oceny. Można skorzystać z funkcji `zip()`.

3. Napisz listę składaną, która dla każdej liczby od 1 do 50 zwraca jej reprezentację stringową (`str`) tylko wtedy, gdy liczba jest równocześnie:

    - Wielokrotnością 3,
    - Wielokrotnością 5.

4. Masz listę list: `[[1, 2, 3], [4, 5, 6], [7, 8, 9]]`. Stwórz listę składaną, która spłaszczy tę strukturę (utworzy jedną płaską listę), a następnie podnieś każdy element do kwadratu.

5. Stwórz listę składaną, która dla każdej liczby z zakresu 1-100 zwraca jej kwadrat, ale tylko jeśli pierwiastek kwadratowy z tej liczby jest liczbą całkowitą (np. 1, 4, 9, 16...).

    !!! tip "Wskazówka"
        Możesz użyć `math.sqrt()` z modułu `math` i sprawdzić czy wynik jest liczbą całkowitą używając porównania `== int(...)` lub operacji modulo na zmiennoprzecinkowej wersji.
