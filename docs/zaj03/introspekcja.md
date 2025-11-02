Introspekcja pozwala na dynamiczne badanie obiektów, ich struktur oraz cech w czasie działania programu.

### `__class__`

Atrybut ten pozwala na sprawdzenie klasy, do której należy dany obiekt.

```python
class Zwierze:
    pass

class Pies(Zwierze):
    pass

reksio = Pies()
print(reksio.__class__)  # <class '__main__.Pies'>
print(reksio.__class__.__name__)  # Pies
```

### `__dict__`

Atrybut ten to słownik, który przechowuje wszystkie atrybuty instancji obiektu. Dzięki `__dict__` można dynamicznie uzyskać dostęp do atrybutów obiektu, modyfikować je, dodawać now lub iterować po nich.

```python
class Samochod:
    def __init__(self, marka, model, rok):
        self.marka = marka
        self.model = model
        self.rok = rok

moj_samochod = Samochod("Toyota", "Corolla", 2020)
print(moj_samochod.__dict__) # {'marka': 'Toyota', 'model': 'Corolla', 'rok': 2020}
```