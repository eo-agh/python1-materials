Introspekcja pozwala na dynamiczne badanie obiektów, ich struktur oraz cech w czasie działania programu.

---

### `__class__`

Pozwala sprawdzić klasę, do której należy dany obiekt.

```python
class Zwierze:
    pass

class Pies(Zwierze):
    pass

reksio = Pies()
print(reksio.__class__)        # <class '__main__.Pies'>
print(reksio.__class__.__name__)  # Pies
```

---

### `__dict__`

Słownik przechowujący wszystkie atrybuty instancji. Pozwala dynamicznie uzyskać do nich dostęp, modyfikować je lub iterować po nich.

```python
class Samochod:
    def __init__(self, marka, model, rok):
        self.marka = marka
        self.model = model
        self.rok = rok

moj_samochod = Samochod("Toyota", "Corolla", 2020)
print(moj_samochod.__dict__)  # {'marka': 'Toyota', 'model': 'Corolla', 'rok': 2020}
```

---

### `isinstance()` i `issubclass()`

`isinstance(obiekt, klasa)` sprawdza, czy obiekt jest instancją danej klasy **lub którejkolwiek klasy nadrzędnej**.  
`issubclass(klasa_pochodna, klasa_bazowa)` sprawdza relację między klasami.

```python
class Zwierze:
    pass

class Pies(Zwierze):
    pass

reksio = Pies()

print(isinstance(reksio, Pies))     # True
print(isinstance(reksio, Zwierze))  # True  - bo Pies dziedziczy po Zwierze
print(isinstance(reksio, str))      # False

print(issubclass(Pies, Zwierze))    # True
print(issubclass(Zwierze, Pies))    # False
```

!!! tip
    Preferuj `isinstance()` zamiast `type(x) == Klasa` - ta pierwsza uwzględnia dziedziczenie.

---

### `hasattr()`, `getattr()`, `setattr()`

Pozwalają dynamicznie sprawdzać i operować na atrybutach obiektów po nazwie (jako string).

```python
class Samochod:
    def __init__(self, marka, rok):
        self.marka = marka
        self.rok = rok

auto = Samochod("Toyota", 2020)

# Sprawdzenie czy atrybut istnieje
print(hasattr(auto, "marka"))   # True
print(hasattr(auto, "kolor"))   # False

# Odczyt atrybutu po nazwie
print(getattr(auto, "marka"))          # Toyota
print(getattr(auto, "kolor", "brak"))  # brak  - wartość domyślna gdy atrybut nie istnieje

# Ustawienie atrybutu po nazwie
setattr(auto, "kolor", "czerwony")
print(auto.kolor)  # czerwony
```

Szczególnie przydatne gdy nazwa atrybutu jest znana dopiero w czasie działania programu (np. pochodzi z konfiguracji lub danych wejściowych).

---

### `dir()`

Zwraca posortowaną listę wszystkich atrybutów i metod dostępnych dla danego obiektu - łącznie z odziedziczonymi i dunder methods.

```python
class Pies:
    def __init__(self, imie):
        self.imie = imie

    def szczekaj(self):
        return "Hau!"

reksio = Pies("Reksio")
print(dir(reksio))
# ['__class__', '__delattr__', ..., 'imie', 'szczekaj']
```

Przydatne do eksploracji nieznanego obiektu - zobaczysz co oferuje bez czytania dokumentacji.