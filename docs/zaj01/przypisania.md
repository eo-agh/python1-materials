## Przypisania

W Pythonie **przypisania zmiennych tworzą referencje do obiektów**, co oznacza, że zmienne działają jak wskaźniki. Zmienne nie przechowują samych wartości, lecz odnoszą się do obiektów w pamięci.

Zmienne są **tworzone automatycznie przy pierwszym przypisaniu** wartości, bez potrzeby deklarowania ich wcześniej.

Przed użyciem zmiennej należy najpierw przypisać do niej wartość, w przeciwnym razie Python zgłosi błąd.

**Rozpakowywanie** pozwala przypisać wiele wartości do zmiennych jednocześnie, np. z listy, krotki lub innej iterowalnej struktury.

```python
a, b, c = [1, 2, 3]

skladniki = ['mąka', 'jajka', 'mleko', 'cukier', 'sól']
baza, *glowne_skladniki, przyprawy = skladniki
print(f"Baza przepisu to {baza}")
print(f"Główne składniki to {glowne_skladniki}")
print(f"A {przyprawy} to użyte przyprawy.")
```

## 📝 Zadania

1. Mając daną krotkę `dane = (2024, 'Python', 3.8)`, przypisz każdy element krotki do odpowiednich zmiennych: `rok`, `jezyk` i `wersja`. Wyświetl te zmienne.

2. Mając listę `oceny = [4, 3, 5, 2, 5, 4]`, przypisz pierwszą wartość do zmiennej `pierwsza`, ostatnią do `ostatnia`, a pozostałe do listy `srodek`. Wykorzystaj `*` do zgromadzenia środkowych wartości. Wyświetl te zmienne.

3. Dla krotki `info = ('Jan', 'Kowalski', 30, 'Polska', 'programista')`, przypisz imię do zmiennej `imie`, nazwisko do `nazwisko`, a zawód do `zawod`, ignorując pozostałe wartości. Do ignorowania wykorzystaj znak `_`. Wyświetl przypisane zmienne.

4. Mając zagnieżdżoną strukturę `dane = (2024, ['Python', 3.8, ('Stabilna', 'Wersja')])`, przypisz rok do zmiennej `rok`, nazwę języka do `jezyk`, wersję do `wersja` i opis wersji do zmiennej `opis`. Wyświetl te zmienne.

## Przypisania z wieloma celami i współdzielone referencje

**Współdzielone referencje** - zmienne mogą odnosić się do tego samego obiektu w pamięci. Zmiana jednego obiektu może mieć wpływ na inny, jeśli oba mają tę samą referencję.

```python
a = b = [1, 2, 3]
b[0] = 'zmieniono'
print(a, b)

# Kopiowanie listy a do nowej listy c
c = list(a)  # Można również użyć a[:] dla płytkiej kopii

# Modyfikacja pierwszego elementu w c
c[0] = 'nowa wartość'

# Wyświetlenie wszystkich list
print(f"Lista a: {a}, lista b: {b}, lista c: {c}")

```

??? - note "Jaka płytka i głęboka kopia?"
    **Płytka kopia** tworzy nową instancję obiektu, ale nie kopiuje obiektów wewnętrznych (czyli elementów, do których ten obiekt odnosi się) (np. poprzez `copy.copy()`).

    **Głęboka kopia** tworzy nową instancję obiektu, a także kopiuje wszystkie obiekty zagnieżdżone wewnątrz niego. To oznacza, że głęboka kopia tworzy całkowicie nową strukturę danych, która nie współdzieli referencji z oryginalnym obiektem (np. poprzez `copy.deepcopy()`).

## 📝 Zadania

1. Stwórz zmienną `a` oraz `b`, użyj przypisania z wieloma celami i przypisz im listę \[1, 2, 3\]: `a = b = [1, 2, 3]`. Zmodyfikuj pierwszy element listy `b` przez przypisanie `b[0] = 'zmieniono'`. Wyświetl obie listy `a` i `b`, a następnie wyjaśnij, dlaczego zmiana w `b` wpłynęła również na `a`. Czy listy są obiektami mutowalnymi?

2. Korzystając z poprzedniego przykładu, utwórz zmienną `c` i przypisz jej kopię listy `a` (możesz użyć metody `list()` lub składni `a[:]`). Następnie zmodyfikuj pierwszy element w `c` i przypisz mu wartość `'nowa wartość'`. Wyświetl listy `a`, `b` i `c`, zauważając, że tym razem zmiana w `c` nie wpłynęła na `a` ani `b`. Wyjaśnij, dlaczego kopiowanie listy zapobiegło współdzieleniu referencji.

3. Utwórz zmienną `x` oraz `y`, przypisz im wartość `10` poprzez `x = y = 10`. Zwiększ wartość `y` o 1 (np. `y = y + 1`). Wyświetl wartości `x` i `y`, a następnie wyjaśnij, dlaczego modyfikacja `y` nie wpłynęła na wartość `x`. Czy integery są obiektami mutowalnymi?

??? - note "Jakie obiekty mutowalne i niemutowalne?"
    W Pythonie obiekty dzielą się na mutowalne (zmienne) i niemutowalne (niezmienne). Kluczową różnicą między nimi jest to, czy zawartość obiektu może być zmieniona w miejscu (bez tworzenia nowego obiektu) po jego utworzeniu.

    - Obiekty mutowalne: Ich zawartość może być zmieniana po utworzeniu. Zmiany są dokonywane bez tworzenia nowej referencji w pamięci. Na przykład: **listy, słowniki czy zbiory (sets)**.
    - Obiekty niemutowalne: Nie można zmienić ich zawartości. Każda próba modyfikacji tworzy nowy obiekt. Na przykład: **liczby całkowite (integers), liczby zmiennoprzecinkowe (floats), krotki (tuples) czy łańcuchy znaków (strings)**.

    ```python
    # Przykład z obiektem mutowalnym - lista
    mut_list = [1, 2, 3]
    print("Początkowa lista:", mut_list)
    print("Początkowy identyfikator listy:", id(mut_list))

    # Zmiana elementu listy
    mut_list[0] = 'zmienione'
    print("\nLista po modyfikacji:", mut_list)
    print("Identyfikator listy po modyfikacji:", id(mut_list))  # Ten sam identyfikator

    # Przykład z obiektem niemutowalnym - liczba całkowita
    num = 10
    print("Początkowa wartość num:", num)
    print("Początkowy identyfikator num:", id(num))

    # Zmiana wartości liczby
    num = num + 1
    print("\nWartość num po modyfikacji:", num)
    print("Identyfikator num po modyfikacji:", id(num))  # Nowy identyfikator

    # Przykład z obiektem niemutowalnym - krotka
    print("\nObiekty niemutowalne (krotka):")
    immut_tuple = (1, 2, 3)
    print("Początkowa krotka:", immut_tuple)
    print("Początkowy identyfikator krotki:", id(immut_tuple))

    # Próba modyfikacji krotki - TypeError
    try:
        immut_tuple[0] = 'zmienione'  # Spowoduje błąd
    except TypeError as e:
        print("\nBłąd podczas modyfikacji krotki:", e)
    ```

## Przypisania rozszerzone i współdzielone referencje

**Przypisania rozszerzone** to operatory takie jak `+=`, `-=`, `*=`, które modyfikują wartość zmiennej i przypisują wynik.

```python
x = 5
x += 2  # to samo co x = x + 2
print(x)
```

## 📝 Zadania

1. Wyzwól następujący kod, wyświetl K, L, M i N. Wyjaśnij w jaki sposób konkatenacja zachowuje się inaczej od przypisania rozszerzonego.

```python
K = [1, 2]
L = K
# konkatenacja
K = K + [3, 4]
M = [1, 2]
N = M
# przypisanie rozszerzone
M += [3, 4]
```

## Słowa zarezerwowane

**Słowa zarezerwowane** w Pythonie mają specjalne znaczenie i nie mogą być używane jako nazwy zmiennych. Przykładami takich słów są `if`, `else`, `for`, `while`, `def`, `return` itp.
