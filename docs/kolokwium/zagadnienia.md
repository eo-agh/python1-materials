# Zagadnienia do kolokwium - Python 1

## 1. Typy wbudowane i podstawy

1.1. Typy danych

- Typy liczbowe: `int`, `float`, `complex`
- Łańcuchy znaków (`str`) - niezmienność, indeksowanie, slicing
- Listy (`list`) - mutowalne, dynamiczne kolekcje
- Krotki (`tuple`) - niemutowalne kolekcje
- Słowniki (`dict`) - pary klucz-wartość
- Zbiory (`set`) - unikalne elementy, operacje matematyczne

1.2. Mutowalne vs niemutowalne obiekty

- Obiekty mutowalne: `list`, `dict`, `set`
- Obiekty niemutowalne: `int`, `float`, `str`, `tuple`
- Konsekwencje przy przypisaniach i przekazywaniu do funkcji
- Współdzielone referencje

1.3. Przypisania i referencje

- Przypisania tworzą referencje do obiektów
- Rozpakowywanie (unpacking) z operatorem `*`
- Płytka kopia (`copy.copy()`) vs głęboka kopia (`copy.deepcopy()`)
- Przypisania rozszerzone (`+=`, `-=`, `*=`)

1.4. Slicing (wycinki)

- Składnia: `lista[start:stop:step]`
- Indeksowanie ujemne
- Odwracanie sekwencji: `lista[::-1]`

---

## 2. Funkcje

2.1. Definiowanie funkcji

- Słowo kluczowe `def`
- Argumenty i parametry
- Instrukcja `return`

2.2. Argumenty funkcji

- Argumenty pozycyjne vs nazwane
- Wartości domyślne parametrów
- `*args` - zmienna liczba argumentów pozycyjnych (krotka)
- `**kwargs` - zmienna liczba argumentów nazwanych (słownik)
- Kolejność parametrów: pozycyjne → domyślne → `*args` → `**kwargs`

2.3. Adnotacje typów (type hints)

- Składnia: `def funkcja(x: int) -> str:`
- Moduł `typing` dla zaawansowanych typów
- Adnotacje nie wymuszają typów - są podpowiedzią

2.4. Lambda

- Funkcje anonimowe: `lambda x, y: x + y`
- Użycie z `sorted()`, `filter()`, `map()`, `max()`, `min()`
- Kiedy używać, a kiedy nie

2.5. Listy składane (list comprehension)

- Składnia: `[wyrażenie for x in iterable if warunek]`
- Zagnieżdżone pętle
- Wyrażenie warunkowe (ternary): `a if warunek else b`

---

## 3. Programowanie obiektowe (OOP)

3.1. Klasy i instancje

- Definicja klasy (`class`)
- Konstruktor `__init__`
- `self` - referencja do instancji
- Atrybuty instancji vs atrybuty klasy

3.2. Metody

- Metody instancji (pierwszy argument: `self`)
- Metody klasy (`@classmethod`, pierwszy argument: `cls`)
- Metody statyczne (`@staticmethod`, brak specjalnego argumentu)

3.3. Dziedziczenie

- Klasa bazowa (nadrzędna) i pochodna
- `super()` - wywołanie metody klasy nadrzędnej
- MRO (Method Resolution Order) - kolejność przeszukiwania
- Dziedziczenie wielokrotne

3.4. Nadpisywanie metod (overriding)

- Redefiniowanie metod w klasie pochodnej
- Rozszerzanie vs zastępowanie zachowania

3.5. Przeciążanie operatorów (dunder methods)

- `__init__`, `__str__`, `__repr__`
- `__add__`, `__sub__`, `__mul__`, `__lt__`, `__eq__`
- `__len__`, `__getitem__`, `__setitem__`
- `__iter__`, `__next__`
- `__enter__`, `__exit__` (menedżery kontekstu)

3.6. Klasy abstrakcyjne

- Moduł `abc` (Abstract Base Classes)
- Dekorator `@abstractmethod`
- Wymuszanie implementacji metod w klasach pochodnych

3.7. Sloty (`__slots__`)

- Optymalizacja pamięci
- Ograniczenie dynamicznego dodawania atrybutów
- Brak `__dict__` w instancji

---

## 4. Iteratory i generatory

4.1. Iteratory

- Protokół iteratora: `__iter__()` i `__next__()`
- Funkcje `iter()` i `next()`
- Wyjątek `StopIteration`
- Każda pętla `for` używa iteratorów

4.2. Generatory

- Słowo kluczowe `yield`
- Lazy evaluation - obliczanie na żądanie
- Wyrażenia generatorów: `(x for x in range(10))`
- Różnice między iteratorami a generatorami

---

## 5. Menedżery kontekstu

5.1. Instrukcja `with`

- Automatyczne zarządzanie zasobami
- Gwarancja zamknięcia/zwolnienia zasobów

5.2. Tworzenie własnych menedżerów

- Metody `__enter__` i `__exit__`
- Obsługa wyjątków w `__exit__`
- Argumenty `exc_type`, `exc_value`, `traceback`

---

## 6. Dekoratory

6.1. Podstawy dekoratorów

- Funkcja przyjmująca funkcję i zwracająca funkcję
- Składnia `@dekorator`
- Funkcja `wrapper` z `*args, **kwargs`

6.2. Dekoratory z argumentami

6.3. Typowe zastosowania

- Logowanie
- Mierzenie czasu wykonania
- Uwierzytelnianie
- Dekorowanie klas

---

## 7. Wyjątki

7.1. Obsługa wyjątków

- `try`, `except`, `else`, `finally`
- Przechwytywanie konkretnych wyjątków
- Dostęp do informacji o wyjątku: `except Exception as e`

7.2. Wywoływanie wyjątków

- `raise` - ręczne wywołanie wyjątku
- Re-raising: `raise` bez argumentów

7.3. Tworzenie własnych wyjątków

- Dziedziczenie po `Exception`
- Hierarchia wyjątków
- Dodawanie atrybutów do wyjątków

7.4. Popularne wyjątki

- `ValueError`, `TypeError`, `KeyError`, `IndexError`
- `AttributeError`, `FileNotFoundError`
- `ZeroDivisionError`, `RuntimeError`

---

## 8. Testowanie (pytest)

8.1. Podstawy pytest

- Funkcje testowe: `def test_nazwa():`
- Asercje: `assert wynik == oczekiwane`
- Uruchamianie testów: `pytest`, `pytest -v`, `pytest -x`

8.2. Testowanie wyjątków

- `with pytest.raises(TypWyjatku):`
- Weryfikacja komunikatu: `match="..."`

8.3. Parametryzacja

- `@pytest.mark.parametrize("args", [...])`
- Testowanie wielu przypadków jedną funkcją

8.4. Fixtures

- `@pytest.fixture`
- Setup i teardown z `yield`
- Scope: `function`, `module`, `session`
- Plik `conftest.py`

8.5. Mockowanie

- `MagicMock` i `patch` z `unittest.mock`
- Zastępowanie zależności

---

## 9. Logowanie

9.1. Moduł `logging`

- Dlaczego nie `print()`
- Poziomy: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`

9.2. Konfiguracja

- `logging.basicConfig()`
- Format komunikatów
- Logowanie do pliku i konsoli

9.3. Loggery i Handlery

- `logging.getLogger(__name__)`
- `StreamHandler`, `FileHandler`
- `RotatingFileHandler`

9.4. Dobre praktyki

- Jeden logger per moduł
- `logger.exception()` w blokach `except`
- Nie logować wrażliwych danych

---

## 10. Środowiska wirtualne

10.1. Po co środowiska wirtualne?

- Izolacja zależności między projektami
- Reprodukowalność
- Ochrona instalacji systemowej Pythona

10.2. Narzędzia

- `venv` - wbudowane, podstawowe
- `pipenv` - `Pipfile` + lock
- `poetry` - `pyproject.toml` + lock
- `conda`/`mamba` - pakiety spoza PyPI
- `pixi` - nowoczesne, szybkie

10.3. Pliki konfiguracyjne

- `requirements.txt`
- `environment.yml`
- `pyproject.toml`
- Lock files (reproducibility)

---

## 11. Formatowanie i analiza kodu

11.1. PEP 8

- Styl kodowania Python
- Wcięcia (4 spacje)
- Nazewnictwo: `snake_case` vs `CamelCase`

11.2. Ruff

- Linting i formatowanie w jednym
- `ruff check .`, `ruff format .`
- Konfiguracja w `ruff.toml` lub `pyproject.toml`
- Automatyczne naprawy: `--fix`

11.3. Pre-commit hooks

- Automatyczne sprawdzanie przed commitem
- Plik `.pre-commit-config.yaml`
- `pre-commit install`, `pre-commit run --all-files`

---

## 12. Pakiety i budowanie

12.1. Struktura pakietu

- Folder z plikiem `__init__.py`
- Import bezwzględny vs względny
- `__all__` - kontrola eksportów

12.2. pyproject.toml

- Centralna konfiguracja projektu
- Sekcje: `[build-system]`, `[project]`, `[tool.xxx]`
- Zależności runtime vs dev

12.3. Budowanie pakietu

- `pip install -e .` - tryb edytowalny
- `python -m build` - budowanie dystrybucji
- Pliki `.whl` i `.tar.gz`

12.4. Wersjonowanie (SemVer)

- `MAJOR.MINOR.PATCH`
- Kiedy zwiększać którą część

---

## 13. Praca z plikami

13.1. Otwieranie i czytanie

- `open()` z menedżerem kontekstu
- Tryby: `'r'`, `'w'`, `'a'`, `'rb'`, `'wb'`
- Encoding: `encoding='utf-8'`

13.2. Metody

- `read()`, `readline()`, `readlines()`
- `write()`, `writelines()`

---

## 14. Moduły i przestrzenie nazw

14.1. Importowanie

- `import moduł`
- `from moduł import funkcja`
- `import moduł as alias`

14.2. Przestrzenie nazw

- Lokalna, globalna, wbudowana
- `sys.path` - ścieżki wyszukiwania modułów

14.3. Idiomy Pythona

- `if __name__ == "__main__":` - uruchamianie vs importowanie
- `__all__` - kontrola eksportów przy `import *`

---

## Wskazówki do kolokwium

1. **Zrozum mechanizmy** - Python to język praktyczny, ważniejsze jest zrozumienie mechanizmów niż zapamiętywanie składni.

7. **Czytaj kod** - pytania mogą zawierać fragmenty kodu do analizy.

---

## Przykładowe typy pytań na kolokwium

Poniżej znajdują się przykłady głównych typów pytań, które mogą pojawić się na kolokwium.

### Typ 1: Analiza kodu

Pytania sprawdzające umiejętność prześledzenia wykonania kodu i przewidzenia wyniku.

**Przykład:**

```python
def zmien(lista):
    lista = [10, 20, 30]
    return lista

moja_lista = [1, 2, 3]
zmien(moja_lista)
print(moja_lista)
```

Co zostanie wypisane?

- A) `[10, 20, 30]`
- B) ✅ `[1, 2, 3]`
- C) `[]`
- D) Błąd

**Wyjaśnienie:** Przypisanie `lista = [10, 20, 30]` tworzy nową referencję lokalną, nie modyfikuje oryginalnej listy.

---

### Typ 2: Pytania teoretyczne i koncepcyjne

Pytania sprawdzające znajomość mechanizmów Pythona, różnic między koncepcjami oraz wybór odpowiednich rozwiązań.

**Przykład:**

Które stwierdzenie o generatorach w Pythonie jest **prawdziwe**?

- A) Generator musi być zdefiniowany jako klasa z metodami `__iter__` i `__next__`
- B) ✅ Generator używa słowa kluczowego `yield` i oblicza wartości leniwie (lazy evaluation)
- C) Generator zawsze zwraca listę wszystkich wartości od razu
- D) Generator nie może być użyty w pętli `for`

**Wyjaśnienie:** Generatory używają `yield` i obliczają wartości na żądanie, co oszczędza pamięć.
