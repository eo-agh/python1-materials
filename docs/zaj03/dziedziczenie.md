## Wstęp

Kluczowa cecha programowania obiektowego, **pozwala na tworzenie nowych klas na bazie istniejących**. Umożliwia to wykorzystanie już zdefiniowanych atrybutów i metod w nowej klasie, co sprzyja oszczędności kodu i ułatwia jego zarządzanie.

!!! warning "Uwaga"
    Zwróć uwagę w poniższym przykładzie jak wygląda wywoływanie konstruktorów klas nadrzędnych.

```python
class Pojazd:  # Klasa bazowa
    def __init__(self, marka):
        self.marka = marka

    def uruchom(self):
        print(f"{self.marka} jest uruchamiany.")

class Samochod(Pojazd):  # Klasa pochodna
    def __init__(self, marka, model):
        super().__init__(marka)  # Wywołanie konstruktora klasy bazowej
        self.model = model
```

## Wyszukiwanie dziedziczenia

Gdy obiekt wywołuje metodę lub uzyskuje dostęp do atrybutu, Python rozpoczyna process wyszukiwania tego elementu zgodnie z tzw. **MRO** (**Method Resolution Order**) – to algorytm wyszukiwania dziedziczenia:

1. Najpierw sprawdzana jest klasa, do której należy dany obiekt (czyli klasa pochodna).
2. Następnie sprawdzane są klasy bazowe (superklasy) w kolejności od najbliższej do najdalszej.
3. Jeśli Python znajdzie odpowiedni element w pierwszej napotkanej klasie, kończy poszukiwanie.

Klasy mogą także dziedziczyć po więcej niż jednej klasie - nazywamy to **dziedziczeniem wielokrotnym**. W takim wypadku wyszukiwanie z pkt. 2 odbywa się od najbliższej klasy bazowej (wymieniona jako pierwsza przy definiowaniu dziedziczenia) do najdalszej.

```python
class Pojazd:
    def uruchom(self):
        print("Pojazd jest uruchamiany.")

class Silnik:
    def uruchom(self):
        print("Silnik jest uruchamiany.")

class Samochod(Pojazd, Silnik):
    pass

moj_samochod = Samochod()
moj_samochod.uruchom()

## Zwraca: Pojazd jest uruchamiany.
```

Aby sprawdzić kolejność MRO, można użyć metody `.__mro__` lub funkcji `help()`:

```python
print(Samochod.__mro__)
```

Jeśli metoda lub atrybut nie istnieje ani w klasie pochodnej, ani w żadnej z klas bazowych, Python zgłasza błąd, np. `AttributeError`.

## Nadpisywanie (*overriding*)

Process, w którym klasa pochodna definiuje własną wersję metody o tej samej nazwie, co metoda w klasie bazowej. Dzięki temu klasa pochodna może zmienić lub rozszerzyć działanie odziedziczonej metody, dostosowując ją do własnych potrzeb.

???+ warning "Uwaga"
    Zwróć uwagę w poniższym przykładzie jak wygląda specjalizacja odziedziczonych metod.

```python
class Pojazd:
    def uruchom(self):
        print("Pojazd jest uruchamiany.")

class Samochod(Pojazd):
    def uruchom(self):  # Nadpisywanie metody uruchom
        print("Samochód jest uruchamiany szybciej!")
        super().uruchom()  # Wywołanie oryginalnej metody klasy bazowej

moj_samochod = Samochod()
moj_samochod.uruchom()
```
