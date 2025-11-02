Technika, która pozwala definiować, jak klasy będą odpowiadać na standardowe operacje, takie jak np. dodawanie, porównywanie czy wyświetlanie reprezentacji tekstowej.

W Pythonie przeciążanie odbywa się przez definiowanie w klasie specjalnych metod (tzw. *dunder methods* – od *double underscore methods*), które zaczynają i kończą podwójnym podkreśleniem (np. `__init__`, `__add__` czy `__str__`).

```python
class Wektor:
    # Ta metoda odpowiada za inicjalizację
    def __init__(self, x, y):
        self.x = x
        self.y = y

    # Metoda przeciążająca operator +
    # Definiuje dodawanie wiektora do wektora
    def __add__(self, inny_wektor):
        return Wektor(self.x + inny_wektor.x, self.y + inny_wektor.y)

    # Metoda przeciążająca operator <
    def __lt__(self, inny_wektor):
        return (self.x**2 + self.y**2) < (inny_wektor.x**2 + inny_wektor.y**2)

    # Definiuje tekstową reprezentację obiektu
    def __str__(self):
        return f"Wektor({self.x}, {self.y})"

wektor1 = Wektor(2, 3)
wektor2 = Wektor(1, 1)
suma = wektor1 + wektor2
print(suma)
print(wektor1 < wektor2)
```

??? - "Rozszerzona lista metod do przeciążania operatorów"
    | Metoda                                                     | Przeciąża                         | Wywoływana dla                                                                                                                |
    | ---------------------------------------------------------- | --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
    | `__init__`                                                 | Konstruktor                       | Tworzenie obiektu - `x = Klasa(args)`                                                                                         |
    | `__del__`                                                  | Destructor                        | Zwolnienie obiektu `x`                                                                                                        |
    | `__add__`                                                  | Operator `+`                      | `x + y`, `x += y`, jeśli nie ma `__iadd__`                                                                                    |
    | `__or__`                                                   | Operator \`                       | \` (OR poziomu bitowego)                                                                                                      |
    | `__repr__`, `__str__`                                      | Wyświetlanie, konwersje           | `print(x)`, `repr(x)`, `str(x)`                                                                                               |
    | `__call__`                                                 | Wywołanie funkcji                 | `x(*args, **kargs)`                                                                                                           |
    | `__getattr__`                                              | Odczytanie atrybutu               | `x.niezdefiniowany_atrybuty`                                                                                                  |
    | `__setattr__`                                              | Przypisanie atrybutu              | `x.atrybut = wartosc`                                                                                                         |
    | `__delattr__`                                              | Usuwanie atrybutu                 | `del x.atrybut`                                                                                                               |
    | `__getattribute__`                                         | Przechwytywanie atrybutu          | `x.atrybut`                                                                                                                   |
    | `__getitem__`                                              | Indeksowanie, wycinanie, iteracje | `x[klucz]`, `x[i:j]`, pętle `for` oraz inne iteracje, jeśli nie ma `__iter__`                                                 |
    | `__setitem__`                                              | Przypisanie indeksu i wycinka     | `x[klucz] = wartosc`, `x[i:j] = sekwencja`                                                                                    |
    | `__delitem__`                                              | Usuwanie indeksu i wycinka        | `del x[klucz]`, `del x[i:j]`                                                                                                  |
    | `__len__`                                                  | Długość                           | `len(x)`, testy prawdziwości, jeśli nie ma `__bool__`                                                                         |
    | `__bool__`                                                 | Testy logiczne                    | `bool(x)`, testy prawdziwości                                                                                                 |
    | `__lt__`, `__gt__`, `__le__`, `__ge__`, `__eq__`, `__ne__` | Porównania                        | `x < y`, `x > y`, `x <= y`, `x >= y`, `x == y`, `x != y`                                                                      |
    | `__radd__`                                                 | Prawostronny operator `+`         | `nieinstancja + x`                                                                                                            |
    | `__iadd__`                                                 | Dodawanie w miejscu (rozszerzone) | `x += y`                                                                                                                      |
    | `__iter__`, `__next__`                                     | Konteksty iteracyjne              | `i = iter(x)`, `next(i)`; pętle `for`, jeśli nie ma `__contains__`, testy `in`, wszystkie listy składanie, funkcje `map(f,x)` |
    | `__contains__`                                             | Test przynależności               | `item in x` (dowolny iterator)                                                                                                |
    | `__index__`                                                | Wartość całkowita                 | `hex(x)`, `bin(x)`, `oct(x)`, `o[x]`, `o[x:]`                                                                                 |
    | `__enter__`, `__exit__`                                    | Menedżer konktekstu               | `with obj as var:`                                                                                                            |
    | `__get__`, `__set__`, `__delete__`                         | Atrybuty deskryptorów             | `x.attr`, `x.attr = value`, `del x.attr`                                                                                      |
    | `__new__`                                                  | Tworzenie instancji               | Tworzenie instancji, przed `__init__`                                                                                         |
