W Pythonie nie ma prawdziwej prywatności - istnieją za to **dwie konwencje** sygnalizujące, że atrybut lub metoda nie są częścią publicznego interfejsu klasy:

- **Pojedyncze podkreślenie** `_nazwa` - czysta konwencja: „to jest szczegół implementacyjny, używaj ostrożnie". Python nie robi nic specjalnego z taką nazwą, ale IDE i inni programiści rozumieją, że nie należy jej używać z zewnątrz.
- **Podwójne podkreślenie** `__nazwa` - uruchamia mechanizm **name mangling**: Python automatycznie zmienia nazwę atrybutu tak, żeby ograniczyć przypadkowy dostęp z zewnątrz i uniknąć kolizji nazw przy dziedziczeniu. Nie jest to jednak pełna prywatność - dostęp nadal jest możliwy.

```python
class MojaKlasa:
    def __init__(self):
        self._chroniony = "widoczny, ale używaj ostrożnie"
        self.__ukryty = "name mangling w akcji"
```

Atrybuty, których nazwy zaczynają się od dwóch podkreślników, np. `__nazwa`, powodują, że Python stosuje *name mangling* - czyli zmienia nazwę atrybutu w taki sposób, że jest trudniej dostępna z zewnątrz klasy, ale nie jest całkowicie prywatna. Jest to bardziej forma ochrony niż pełne ukrycie atrybutów.

```python
class MojaKlasa:
    def __init__(self):
        self.__ukryty_atrybut = "tajne"

    def pokaz_ukryty(self):
        return self.__ukryty_atrybut

obiekt = MojaKlasa()
print(obiekt.pokaz_ukryty())  # Poprawne: "tajne"
print(obiekt._MojaKlasa__ukryty_atrybut)  # Dostęp poprzez name mangling: "tajne"
```

??? - tip "Po co używać atrybutów pseudoprywatnych?"
    - **Ochrona przed przypadkowym nadpisaniem** - przy dziedziczeniu klasy istnieje mniejsze ryzyko, że klasa pochodna przypadkowo nadpisze atrybut o tej samej nazwie.
    - **Czytelność** - pokazują, że atrybut nie jest przeznaczony do bezpośredniego użytku z zewnątrz klasy.