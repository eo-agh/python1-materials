Programowanie obiektowe (*OOP - ang. object-oriented programming*) jest często stosowane w celu uporządkowania kodu i ułatwienia zarządzania złożonymi projektami.

## Zalety

- **Modularność i ponowne wykorzystanie kodu**: OOP sprzyja tworzeniu modułowego kodu, gdzie klasy można wykorzystać wielokrotnie w różnych częściach programu, co zmniejsza ilość powtarzanego kodu i ułatwia modyfikacje.
- **Hermetyzacja**: Dzięki niej można ukryć szczegóły implementacyjne klasy przed użytkownikami, którzy korzystają tylko z interfejsu, co zwiększa bezpieczeństwo kodu i minimalizuje ryzyko przypadkowego naruszenia wewnętrznego stanu obiektu.
- **Dziedziczenie**: Pozwala na tworzenie nowych klas na bazie istniejących, co sprzyja hierarchii i ułatwia rozszerzanie funkcjonalności, redukując potrzebę pisania kodu od podstaw.
- **Polimorfizm**: Dzięki polimorfizmowi różne klasy mogą reagować na te same polecenia w odmienny sposób, co upraszcza zarządzanie różnymi obiektami i zwiększa elastyczność kodu.
- **Lepsze zarządzanie złożonymi systemami**: OOP umożliwia tworzenie struktur, które łatwiej utrzymać w dużych projektach, co jest szczególnie przydatne w złożonych aplikacjach.
- **Łatwość utrzymania i modyfikacji**: Kod obiektowy jest często łatwiejszy do utrzymania, ponieważ klasy i obiekty można modyfikować niezależnie, bez wpływu na inne części systemu.

## Wady

- **Wymaga poprzedzającego planowania koncepcyjnego**: Tworzenie kodu obiektowego wymaga wcześniejszego przemyślenia architektury, co może być czasochłonne, zwłaszcza w mniejszych projektach.
- **Złożoność**: Programowanie obiektowe może wydawać się skomplikowane, szczególnie dla początkujących, przez co trudniej nauczyć się i wdrożyć OOP w prostych aplikacjach.
- **Ukrywanie stanu**: Chociaż hermetyzacja jest zaletą, czasem może być wadą - nie zawsze mamy pełny dostęp do informacji o obiekcie, co może ograniczać elastyczność kodu.
- **Wydajność**: Obiektowy kod jest zazwyczaj cięższy i wolniejszy niż podejście proceduralne, co może mieć znaczenie w aplikacjach wymagających wysokiej wydajności.
- **Nadmierna abstrakcja**: Nadmierne stosowanie obiektów i klas może prowadzić do abstrakcji, które nie zawsze są intuicyjne i mogą utrudniać zrozumienie kodu.
- **Nie zawsze najlepsze dopasowanie do przypadków użycia**: W niektórych przypadkach, zwłaszcza przy przetwarzaniu danych, podejście proceduralne jest bardziej efektywne niż OOP, co sprawia, że stosowanie klas i obiektów może być zbędne.

---

## Przykład: podejście proceduralne vs. obiektowe

Poniżej te same dane i logika wyrażone na dwa sposoby. Wersja obiektowa staje się opłacalna gdy rośnie złożoność.

```python
# -- Podejście proceduralne --
def oblicz_pole_prostokata(szerokosc, wysokosc):
    return szerokosc * wysokosc

def skaluj_prostokat(szerokosc, wysokosc, wspolczynnik):
    return szerokosc * wspolczynnik, wysokosc * wspolczynnik

s, h = 4, 3
print(oblicz_pole_prostokata(s, h))   # 12
s, h = skaluj_prostokat(s, h, 2)
print(oblicz_pole_prostokata(s, h))   # 48
```

```python
# -- Podejście obiektowe --
class Prostokat:
    def __init__(self, szerokosc, wysokosc):
        self.szerokosc = szerokosc
        self.wysokosc = wysokosc

    def pole(self):
        return self.szerokosc * self.wysokosc

    def skaluj(self, wspolczynnik):
        self.szerokosc *= wspolczynnik
        self.wysokosc *= wspolczynnik

r = Prostokat(4, 3)
print(r.pole())    # 12
r.skaluj(2)
print(r.pole())    # 48
```

W wersji obiektowej dane (`szerokosc`, `wysokosc`) i zachowania (`pole`, `skaluj`) są zgrupowane w jednym miejscu - łatwiej rozbudować klasę o kolejne metody bez rozsypania stanu po zmiennych globalnych.