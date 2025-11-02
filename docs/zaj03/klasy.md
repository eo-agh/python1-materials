Jest to paradygmat programowania, który opiera się na tworzeniu obiektów – elementów, które łączą dane i logikę w spójną całość. Każdy obiekt reprezentuje pewien byt (np. samochód, użytkownik, document) i posiada własne właściwości oraz zachowania.

## Klasa

**Szablon** lub **przepis**, który definiuje strukturę i zachowanie jej instancji. Klasa określa, jakie **atrybuty** (cechy) i **metody** (działania) będą posiadały instancje należące do tej klasy. Przykładowo, klasa `Samochod` może zawierać takie atrybuty, jak `marka`, `model`, `rok`, a także metody jak `przyspiesz()` czy `hamuj()`.

```python
class Samochod:
    def __init__(self, marka, model, rok):
        self.marka = marka
        self.model = model
        self.rok = rok
```

Gdy mówimy o **obiektach klasy**, mamy na myśli strukturę (samą definicję), z których można tworzyć instancje.

## Instancja

Konkretny egzemplarz klasy, stworzony na podstawie jej definicji. Instancje posiadają swoje własne wartości atrybutów, choć wszystkie należą do tej samej klasy.

```python
moj_samochod = Samochod('Toyota', 'Corolla', 2020)
```

**Obiekty instancji** (lub po prostu instancje) to konkretne przykłady danej klasy, które istnieją w programie i posiadają własne dane.

## Atrybuty

Przechowują stan obiektu. Każdy atrybut jest częścią obiektu i przechowuje informacje specyficzne dla danej instancji klasy.

Atrybuty można definiować w konstruktorze klasy (metodzie `__init__`), co umożliwia każdej instancji posiadanie indywidualnych wartości.

W naszym przykładzie `marka`, `model` i `rok` to atrybuty.

```python
# Każda instancja posiada swoje własne (inne lub nie; unikatowe lub nie) wartości atrybutów
moj_samochod = Samochod('Toyota', 'Corolla', 2020)
kogos_innego_samochod = Samochod('Ford', 'Focus', 2018)
```

```python
# Dostęp do wartości atrybutów danej instancji klasy
print(moj_samochod.model)
```

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
# Zwraca formatted string: Toyota Corolla przyspiesza o 20!
```
