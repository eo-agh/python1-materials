Specjalny mechanizm, który pozwala na optymalizację pamięci obiektów klasy, poprzez ograniczenie listy atrybutów, które można dodać do instancji danej klasy.

```python
class Osoba:
    __slots__ = ['imie', 'wiek']  # Ograniczamy atrybuty tylko do 'imie' i 'wiek'

    def __init__(self, imie, wiek):
        self.imie = imie
        self.wiek = wiek

osoba = Osoba("Jan", 30)
print(osoba.imie)  # Jan
print(osoba.wiek)  # 30

# Próba dodania nowego atrybutu zgłosi błąd
osoba.address = "Warszawa"  # AttributeError: 'Osoba' object has no attribute 'address'
```

Python przestaje używać dynamicznego słownika `__dict__` do przechowywania atrybutów obiektu, co ogranicza możliwość dodawania nowych atrybutów poza tymi zdefiniowanymi w `__slots__`, ale jednocześnie zmniejsza ilość zużywanej pamięci.

## Przykład

Zapoznaj się z nowym zakomentowanym kodem w `python1course.zajecia04.fleet.ambulance` (linie kodu: 2 oraz 55-57).