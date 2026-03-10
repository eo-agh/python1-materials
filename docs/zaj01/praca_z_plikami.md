# Praca z plikami w Pythonie

## Wprowadzenie
Praca z plikami jest kluczową częścią programowania. Python oferuje wbudowane funkcje do odczytu i zapisu plików, które pozwalają na manipulowanie danymi zapisanymi na dysku. 

Podczas pracy z plikami warto stosować **menedżery kontekstu** (`with`), które dbają o poprawne otwarcie i zamknięcie zasobów, zapobiegając wyciekom pamięci i błędom dostępu.

### Podstawowe operacje na plikach

#### Otwieranie i czytanie pliku tekstowego
```python
with open('example.txt', 'r', encoding='utf-8') as file:
    content = file.read()
    print(content)  # Wyświetla zawartość pliku
```

- `'r'` – tryb odczytu (`read`),
- `encoding='utf-8'` – obsługa znaków specjalnych, np. polskich liter.

#### Odczyt linii po linii
Aby nie wczytywać całego pliku do pamięci, można czytać go linia po linii:
```python
with open('example.txt', 'r', encoding='utf-8') as file:
    for line in file:
        print(line.strip())  # Usuwanie znaków nowej linii
```

#### Zapisywanie do pliku
```python
with open('output.txt', 'w', encoding='utf-8') as file:
    file.write("To jest przykładowy tekst.\n")
    file.write("Druga linia tekstu.\n")
```

- `'w'` – tryb zapisu (`write`), nadpisuje plik,
- `'a'` – tryb dopisania (`append`), dodaje dane do pliku zamiast go nadpisywać.

#### Wczytywanie tylko części pliku
Jeśli plik jest duży, warto odczytywać go fragmentami:
```python
with open('example.txt', 'r', encoding='utf-8') as file:
    chunk = file.read(1024)  # Czyta 1024 bajty (1 KB) na raz
    while chunk:
        print(chunk)
        chunk = file.read(1024)
```

### Praca z plikami binarnymi
Pliki binarne (np. obrazy, pliki audio) odczytuje się w trybie `'rb'`.
```python
with open('image.png', 'rb') as file:
    data = file.read()
    print(data[:20])  # Wyświetla pierwsze 20 bajtów
```
Zapisywanie plików binarnych:
```python
with open('copy.png', 'wb') as new_file:
    new_file.write(data)
```

### Automatyczne zamykanie plików
Menedżery kontekstu (`with`) automatycznie zamykają plik, ale można to zrobić ręcznie:
```python
file = open('example.txt', 'r', encoding='utf-8')
content = file.read()
file.close()
```
Jednak lepiej zawsze używać `with open()`, ponieważ w przypadku błędów plik może pozostać otwarty.

---

## Podsumowanie
1. **Menedżery kontekstu** (`with open()`) ułatwiają zarządzanie plikami.
2. **Kodowanie `utf-8`** jest domyślnym standardem, ale istnieją również inne.
3. **Czytanie dużych plików** można optymalizować, odczytując je fragmentami (`read(1024)`).
4. **Pliki binarne** wymagają trybów `'rb'` i `'wb'`.