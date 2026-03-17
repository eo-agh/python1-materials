## Pętle z licznikami

`range(n)` tworzy sekwencję liczb od `0` do `n-1`. Możemy używać tej funkcji do iteracji w pętli, np. do kontrolowania liczby powtórzeń.

```python
for i in range(6):
    print(f"Licznik: {i}")
```

Funkcja `enumerate()` pozwala jednocześnie uzyskać indeks i wartość z listy.

```python
owoce = ["jabłko", "banan", "wiśnia"]
for idx, owoc in enumerate(owoce):
    print(f"Indeks: {idx}, Owoc: {owoc}")
```

## Skanowanie sekwencji

Skanowanie sekwencji oznacza przechodzenie przez każdy element w liście, krotce, lub innym iterowalnym obiekcie.

```python
owoce = ['jabłko', 'banan', 'pomarańcza']
for owoc in owoce:
    print(owoc)
```

## Przetasowanie sekwencji

`range(len(x))` iteruje po indeksach sekwencji. Może być użyte do manipulacji elementami na podstawie ich indeksów.

```python
owoce = ['jabłko', 'banan', 'pomarańcza']
for i in range(len(owoce)):
    owoce[i] = owoce[i].upper()
print(owoce)
```

## Przechodzenie niewyczerpujące

`range(0, len(x), 2)` lub `x[::2]`

```python
liczby = [1, 2, 3, 4, 5, 6]
for i in range(0, len(liczby), 2):
    print(liczby[i])

# Alternatywnie, z użyciem slicing
print(liczby[::2])
```

## Przechodzenie równoległe

`zip` umożliwia równoległe iterowanie po wielu sekwencjach, a `map` stosuje funkcję do każdego elementu sekwencji.

```python
imiona = ['Anna', 'Jan', 'Piotr']
wieki = [25, 30, 35]
for imie, wiek in zip(imiona, wieki):
    print(f"{imie} ma {wiek} lat")
```

```python
def kwadrat(liczba):
    return liczba ** 2

liczby = [1, 2, 3, 4]
kwadraty = list(map(kwadrat, liczby))
print(kwadraty)
```

## 📝 Zadania

Stwórz plik `python1course/zaj02/petle.py` i wykonaj w nim poniższe zadania.

1. Mając dwie listy, `imiona = ['Anna', 'Jan', 'Ewa']` i `oceny = [5, 4, 3]`, użyj `zip` do stworzenia pary każdego imienia z odpowiadającą mu oceną. Następnie, iteruj przez te pary, wyświetlając imię wraz z oceną. Co się stanie, jeśli listy będą miały różne długości?

2. Stwórz grę w zgadywanie liczby. Program losuje liczbę z zakresu 1-100, a użytkownik próbuje ją odgadnąć. Program powinien informować, czy liczba jest za duża czy za mała, aż użytkownik trafi. Zliczaj liczbę prób.

    !!! tip "Wskazówka"
        Aby wczytać dane od użytkownika, użyj funkcji `input()`: `odpowiedz = input("Podaj liczbę: ")`. Funkcja zwraca string, więc aby porównać z liczbą, musisz użyć `int(odpowiedz)` lub `int(input("Podaj liczbę: "))`. Do losowania liczby użyj modułu `random` z funkcją `random.randint(1, 100)`.

3. Masz listę trójkątów reprezentowanych jako krotki: `[(3, 4, 5), (5, 12, 13), (7, 8, 9), (8, 15, 17)]`, gdzie każda krotka to (bok_a, bok_b, bok_c). Napisz kod, który używa `enumerate` do:

    - Wyświetlenia indeksu każdego trójkąta,
    - Policzenia pola każdego trójkąta (wzór Herona),
    - Wskazania, który trójkąt jest prostokątny (a² + b² = c²).

    Wypisz informacje tylko dla trójkątów prostokątnych.
