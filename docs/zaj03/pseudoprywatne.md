Atrybuty, których nazwy zaczynają się od dwóch podkreślników, np. `__nazwa`. Taka konwencja nazw powoduje, że Python stosuje *name mangling* – czyli zmienia nazwę atrybutu w taki sposób, że jest trudniej dostępna z zewnątrz klasy, ale nie jest całkowicie prywatna. Jest to bardziej forma ochrony, niż pełne ukrycie atrybutów.

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
    - **Ochrona przed przypadkowym nadpisaniem** – przy dziedziczeniu klasy istnieje mniejsze ryzyko, że klasa pochodna przypadkowo nadpisze atrybut o tej samej nazwie.
    - **Czytelność** – pokazują, że atrybut nie jest przeznaczony do bezpośredniego użytku z zewnątrz klasy.