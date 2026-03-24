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

## Kiedy NIE używać `__slots__`

`__slots__` to optymalizacja - stosuj ją świadomie, gdy faktycznie ma sens:

- **Potrzebujesz dynamicznych atrybutów** - jeśli w programie dodajesz atrybuty do obiektów w czasie działania (`obj.nowy = ...`), `__slots__` to uniemożliwi.
- **Klasa korzysta z mixinów lub wielokrotnego dziedziczenia** - łatwo trafić na konflikty, gdy kilka klas w hierarchii definiuje własne `__slots__`.
- **Klasa jest rzadko tworzona lub nie ma jej wiele instancji** - narzut pamięciowy `__dict__` jest wtedy pomijalny, a kod jest prostszy bez `__slots__`.
- **Używasz bibliotek opartych na `__dict__`** - niektóre frameworki (np. ORM-y, biblioteki serializacji) oczekują obecności `__dict__`, a jego brak może powodować błędy.

!!! tip
    Zasada: zacznij bez `__slots__`. Dodaj je tylko wtedy, gdy profilowanie wskazuje na rzeczywisty problem z pamięcią i tworzysz naprawdę wiele instancji danej klasy.

## Przykład

Zapoznaj się z nowym zakomentowanym kodem w `python1course.zaj03.fleet.ambulance` (linie kodu: 2 oraz 55-57).