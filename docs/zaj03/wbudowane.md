Może być przydatne, gdy chcemy dodać dodatkową funkcjonalność lub dostosować zachowanie istniejących typów (np. `list`, `dict`, `str`) do specyficznych potrzeb projektu.

### Za pomocą osadzania (kompozycja)

Poprzez utworzenie nowej klasy, która wewnętrznie przechowuje instancję typu wbudowanego jako atrybut.

W ten sposób klasa ta może wykorzystywać typ wbudowany i rozszerzać jego funkcjonalność, delegując operacje na ten typ, ale nie dziedziczy jego metod bezpośrednio. Osadzanie jest przydatne, gdy chcemy dodać nowe funkcje bez ingerencji w istniejące metody typu wbudowanego.

```python
class MojaLista:
    def __init__(self, elementy):
        self._lista = elementy  # Osadzenie typu wbudowanego list

    def suma(self):
        return sum(self._lista)

    def dodaj(self, element):
        self._lista.append(element)

    def __str__(self):
        return str(self._lista)

moja_lista = MojaLista([1, 2, 3])
moja_lista.dodaj(4)
print(moja_lista)        # [1, 2, 3, 4]
print(moja_lista.suma()) # 10
```

### Za pomocą klas podrzędnych (dziedziczenia)

Poprzez utworzenie klasy podrzędnej, która dziedziczy po typie wbudowanym. Dzięki temu klasa podrzędna automatycznie przejmuje wszystkie metody i atrybuty typu bazowego, co pozwala na łatwe dodanie nowych funkcji lub nadpisanie istniejących metod.

```python
class MojaLista(list):
    def suma(self):
        return sum(self)

moja_lista = MojaLista([1, 2, 3, 4])
print(moja_lista)         # [1, 2, 3, 4]
print(moja_lista.suma())  # 10
```

### Za i przeciw

??? - tip "Za i przeciw dla obu sposobów"
    |               | Za                                                                                                                                                                                                                                   | Przeciw                                                                                                                                                                                                                                                                                                                                |
    | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | Osadzanie     | Daje pełną kontrolę nad metodami, które są dostępne dla użytkownika klasy. Izoluje funkcjonalność rozszerzonego typu od interfejsu klasy bazowej, co może zwiększyć bezpieczeństwo i ułatwić utrzymanie kodu.                        | Wymaga ręcznego implementowania delegacji metod, jeśli potrzebujemy pełnego dostępu do funkcji typu wbudowanego. Może być mniej wydajne i bardziej czasochłonne niż dziedziczenie, jeśli chcemy używać większości metod typu wbudowanego.                                                                                            |
    | Dziedziczenie | Klasa pochodna automatycznie przejmuje wszystkie metody typu wbudowanego, co ułatwia tworzenie nowych funkcji. Jest bardziej ekonomiczne i intuicyjne w implementacji, szczególnie gdy potrzebujemy tylko kilku dodatkowych funkcji. | Trudniej jest zmodyfikować sposób działania niektórych metod w typach wbudowanych, ponieważ metody te mogą wywoływać bezpośrednie operacje na strukturze danych. Dziedziczenie może prowadzić do problemów z nieoczekiwanym zachowaniem, jeśli metody typu wbudowanego nie są dobrze przystosowane do nowych funkcji klasy pochodnej. |


## Przykład - rozszerzanie za pomocą osadzania

Nasza klasa `IncidentQueue` w `python1course.zaj03.operations.incident_queue` rozszerza typ wbudowany `list`:

```python
from .incident import Incident

class IncidentQueue:
    def __init__(self):
        self.__queue = []  # Kompozycja: osadzamy listę jako prywatny atrybut
```

**Jak to działa w tym przypadku?**

Klasa `IncidentQueue` **nie dziedziczy** po `list`, ale **osadza** listę jako prywatny atrybut `__queue`. Aby zachowywać się jak lista, implementuje **magiczne metody (dunder methods)**, które Python wywołuje automatycznie przy określonych operacjach.

??? - info "1. Dostęp do elementów"

    Gdy piszemy `queue[0]`, Python automatycznie wywołuje `queue.__getitem__(0)`.

    Gdy piszemy `queue[0] = incident`, Python automatycznie wywołuje `queue.__setitem__(0, incident)`.

??? - info "2. Iteracja"

    Gdy piszemy `for incident in queue:`, Python:

    - Wywołuje `queue.__iter__()` aby uzyskać iterator,
    - W każdej iteracji wywołuje `__next__()` aż do `StopIteration`.

??? - info "3. Sprawdzanie przynależności"

    Gdy piszemy `incident1 in queue`, Python wywołuje `queue.__contains__(incident1)`.

??? - info "4. Długość"

    Gdy piszemy `len(queue)`, Python wywołuje `queue.__len__()`.

??? - info "5. Operatory arytmetyczne"

    `__add__` - normalne dodawanie

    Gdy piszemy `queue + incident`, Python wywołuje `queue.__add__(incident)`. Ta metoda tworzy **nowy obiekt** `IncidentQueue` z kopią elementów i dodaje nowy incydent.

    `__radd__` - prawostronne dodawanie

    Gdy piszemy `incident + queue`, Python najpierw próbuje wywołać `incident.__add__(queue)`. Jeśli `Incident` nie ma metody `__add__` (lub zwraca `NotImplemented`), Python wywołuje `queue.__radd__(incident)`. Ta metoda również tworzy **nowy obiekt**.

    `__iadd__` - dodawanie w miejscu

    Gdy piszemy `queue += incident`, Python wywołuje `queue.__iadd__(incident)`. Ta metoda **modyfikuje istniejący obiekt** (nie tworzy nowego) i zwraca `self`.

    **Różnica:**

    - `queue + incident` → tworzy nową kolejkę (nie zmienia `queue`)
    - `queue += incident` → modyfikuje istniejącą kolejkę (zmienia `queue`)

??? - info "6. Porównania"

    `__lt__` i `__gt__` odpowiednio wywoływane przez Pythona gdy `queue1 < queue2` i `queue1 > queue2`.

??? - info "7. Konwersja na bool"

    Gdy piszemy `if queue:`, Python wywołuje `queue.__bool__()`, czyli sprawdza czy nasza kolejka jest pusta.

??? - info "8. Wywołanie jako funkcja"

    `__call__` - dzięki temu możemy wyciągnąć instancję `Incident` o zadanym ID `queue(1)` zamiast implementować specjalnie chociażby `queue.find_by_id(1)`.

??? - info "9. Reprezentacja tekstowa"

    `__str__` i `__repr__`

### A może jednak dziedziczenie?

**Kompozycja (obecne rozwiązanie):**

- `IncidentQueue` ma listę (`self.__queue`)
- Musimy ręcznie zaimplementować wszystkie magiczne metody
- Pełna kontrola nad interfejsem
- Nie dziedziczy metod `list` (np. `append`, `extend`, `sort`)

**Dziedziczenie (alternatywa):**
```python
class IncidentQueue(list):  # Dziedziczy po list
    def __init__(self):
        super().__init__()  # Inicjalizuje pustą listę
    
    def __call__(self, id):
        for incident in self:
            if incident.id == id:
                return incident
        raise ValueError("No incident found")
```

`IncidentQueue` jest listą:

- Automatycznie dziedziczy wszystkie metody `list` (`append`, `extend`, `sort`, itp.)
- Mniej kodu, ale mniej kontroli