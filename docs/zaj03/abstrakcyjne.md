Są to klasy służące jako szablony, które definiują strukturę i wymuszone metody dla klas pochodnych, ale same nie mogą być inicjalizowane. Używają dekoratora `@abstractmethod` do oznaczenia metod, które muszą być zaimplementowane w klasach pochodnych.

??? - tip "Korzyści wynikające z wykorzystywania abstrakcyjnych klas nadrzędnych"
    - **Spójność** - wymusza implementację określonych metod.
    - **Reużywalność** - pozwala dzielić metody między klasami.
    - **Polimorfizm** - umożliwia jednolite używanie różnych klas.

```python
from abc import ABC, abstractmethod

class Zwierze(ABC):
    # Ta metoda jest abstrakcyjna,
    # wymagana jest jej implementacja w klasach pochodnych
    @abstractmethod
    def wydaj_dzwiek(self):
        pass

class Pies(Zwierze):
    def wydaj_dzwiek(self):
        return "Hau hau!"

class Kot(Zwierze):
    def wydaj_dzwiek(self):
        return "Miau miau!"
```

## ABC vs. `raise NotImplementedError`

Zanim pojawiły się klasy ABC, metody wymuszane na klasach pochodnych pisano przez ręczne zgłaszanie wyjątku:

```python
class Zwierze:
    def wydaj_dzwiek(self):
        raise NotImplementedError("Klasa pochodna musi zaimplementować tę metodę!")
```

Różnica jest istotna:

| | `@abstractmethod` (ABC) | `raise NotImplementedError` |
|---|---|---|
| Kiedy wykrywa błąd | Przy **tworzeniu instancji** klasy pochodnej | Dopiero przy **wywołaniu** metody w czasie działania |
| Czy można stworzyć obiekt bez implementacji | **Nie** - Python odmówi | **Tak** - błąd pojawi się później |
| Czytelność intencji | Wysoka | Niższa |

```python
from abc import ABC, abstractmethod

class Zwierze(ABC):
    @abstractmethod
    def wydaj_dzwiek(self):
        pass

class Ryba(Zwierze):
    pass  # brak implementacji wydaj_dzwiek

r = Ryba()  # TypeError: Can't instantiate abstract class Ryba
            # without an implementation for abstract method 'wydaj_dzwiek'
```

Preferuj ABC - błąd zostaje wykryty jak najwcześniej, zanim obiekt trafi do reszty kodu.

## Przykład

Ta sama implementacja klasy Employee, ale jako abstrakcyjna klasa nadrzędna:

```python
from abc import ABC, abstractmethod

class Employee(ABC):
    def __init__(self, first_name, last_name, employee_id, salary):
        self.first_name = first_name
        self.last_name = last_name
        self.employee_id = employee_id
        self.salary = salary

    @abstractmethod
    def display_info(self):
        pass
```