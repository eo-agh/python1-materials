Jest to paradygmat programowania, który opiera się na tworzeniu obiektów - elementów, które łączą dane i logikę w spójną całość. Każdy obiekt reprezentuje pewien byt (np. samochód, użytkownik, document) i posiada własne właściwości oraz zachowania.

## Klasa

**Szablon** lub **przepis**, który definiuje strukturę i zachowanie jej instancji. Klasa określa, jakie **atrybuty** (cechy) i **metody** (działania) będą posiadały instancje należące do tej klasy. Przykładowo, klasa `Samochod` może zawierać takie atrybuty, jak `marka`, `model`, `rok`, a także metody jak `przyspiesz()` czy `hamuj()`.

```python
class Samochod:
    def __init__(self, marka, model, rok):
        self.marka = marka
        self.model = model
        self.rok = rok
```

Gdy mówimy o **obiektach klasy**, mamy na myśli strukturę (samą definicję), z której można tworzyć instancje.

## Instancja

Konkretny egzemplarz klasy, stworzony na podstawie jej definicji. Instancje posiadają swoje własne wartości atrybutów, choć wszystkie należą do tej samej klasy.

```python
moj_samochod = Samochod('Toyota', 'Corolla', 2020)
```

**Obiekty instancji** (lub po prostu instancje) to konkretne przykłady danej klasy, które istnieją w programie i posiadają własne dane.

## Atrybuty

Przechowują stan obiektu. W Pythonie wyróżniamy dwa rodzaje atrybutów:

### Atrybuty instancji

Definiowane w konstruktorze (`__init__`) przez `self`. Każda instancja posiada własne, niezależne kopie tych atrybutów.

```python
class Samochod:
    def __init__(self, marka, model, rok):
        self.marka = marka   # atrybut instancji
        self.model = model   # atrybut instancji
        self.rok = rok       # atrybut instancji

moj_samochod = Samochod('Toyota', 'Corolla', 2020)
kogos_innego_samochod = Samochod('Ford', 'Focus', 2018)

print(moj_samochod.marka)         # Toyota
print(kogos_innego_samochod.marka)  # Ford
```

### Atrybuty klasy

Definiowane bezpośrednio w ciele klasy, poza `__init__`. Są **współdzielone** przez wszystkie instancje tej klasy.

```python
class Samochod:
    liczba_kol = 4  # atrybut klasy - wspólny dla wszystkich

    def __init__(self, marka):
        self.marka = marka  # atrybut instancji

auto1 = Samochod('Toyota')
auto2 = Samochod('Ford')

print(auto1.liczba_kol)  # 4
print(auto2.liczba_kol)  # 4

Samochod.liczba_kol = 6  # zmiana przez klasę - dotyczy WSZYSTKICH instancji
print(auto1.liczba_kol)  # 6
print(auto2.liczba_kol)  # 6
```

!!! warning "Pułapka"
    Przypisanie atrybutu przez instancję (`auto1.liczba_kol = 3`) **nie modyfikuje** atrybutu klasy - tworzy nowy atrybut instancji, który przesłania atrybut klasy tylko dla tej konkretnej instancji.

W naszym przykładzie `marka`, `model` i `rok` to atrybuty instancji.

## Metody

Funkcje zdefiniowane wewnątrz klasy, które operują na instancjach tej klasy i mogą zmieniać ich stan.

Zwykle w pierwszym argumencie metody umieszcza się `self`, co pozwala na dostęp do atrybutów i innych metod obiektu.

```python
# Rozszerzenie klasy Samochod o metodę przyspiesz
class Samochod:
    def __init__(self, marka, model, rok):
        self.marka = marka
        self.model = model
        self.rok = rok

    def przyspiesz(self, wartosc: int = 10):
        print(f"{self.marka} {self.model} przyspiesza o {wartosc}!")

moj_samochod.przyspiesz(20)  # wywołanie metody
# Wypisze: Toyota Corolla przyspiesza o 20!
```
