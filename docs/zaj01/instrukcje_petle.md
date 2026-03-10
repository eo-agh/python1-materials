# Instrukcje warunkowe i pętle w Pythonie

## Wprowadzenie
W Pythonie istnieje kilka podstawowych mechanizmów sterowania przepływem programu. Najważniejsze z nich to:

- **Instrukcje warunkowe (`if`, `elif`, `else`)** – pozwalają na podejmowanie decyzji w kodzie,
- **Pętle (`for`, `while`)** – umożliwiają wielokrotne wykonanie kodu,
- **Instrukcje sterujące (`break`, `continue`, `pass`)** – dają większą kontrolę nad działaniem pętli.

---

## Instrukcje warunkowe (`if`, `elif`, `else`)
Instrukcje warunkowe pozwalają na wykonanie określonego kodu tylko wtedy, gdy spełniony jest dany warunek.

### Składnia `if`
```python
x = 10
if x > 5:
    print("x jest większe niż 5")
```
Jeśli warunek jest prawdziwy (`True`), wykonywany jest kod znajdujący się wewnątrz bloku `if`.

### Składnia `if-else`
```python
x = 3
if x > 5:
    print("x jest większe niż 5")
else:
    print("x jest mniejsze lub równe 5")
```
Blok `else` wykona się, gdy warunek w `if` nie zostanie spełniony.

### Składnia `if-elif-else`
Jeśli mamy więcej niż dwa przypadki, możemy użyć `elif`:
```python
x = 7
if x > 10:
    print("x jest większe niż 10")
elif x > 5:
    print("x jest większe niż 5, ale nie większe niż 10")
else:
    print("x jest mniejsze lub równe 5")
```

---

## Operatory logiczne w warunkach
Warunki mogą być łączone przy użyciu operatorów logicznych:
- `and` – oba warunki muszą być prawdziwe,
- `or` – przynajmniej jeden warunek musi być prawdziwy,
- `not` – negacja warunku.

Przykład:
```python
x = 8
y = 15
if x > 5 and y < 20:
    print("Oba warunki są spełnione")
```

---

## Pętle w Pythonie
Pętle pozwalają na wielokrotne wykonanie danego fragmentu kodu.

### Pętla `for`
Pętla `for` iteruje po sekwencjach, np. listach, krotkach, ciągach znaków:
```python
imiona = ["Jan", "Maria", "Piotr"]
for imie in imiona:
    print(imie)
```

#### Iterowanie po indeksach
Możemy iterować po indeksach przy użyciu `range()`:
```python
for i in range(5):
    print(f"Iteracja numer {i}")
```

#### Iterowanie po kluczach słownika
```python
osoba = {"imie": "Jan", "wiek": 30}
for klucz in osoba:
    print(f"{klucz}: {osoba[klucz]}")
```

---

### Pętla `while`
Pętla `while` wykonuje kod tak długo, jak długo warunek jest prawdziwy:
```python
x = 0
while x < 5:
    print(f"x = {x}")
    x += 1
```

Pętle `while` mogą prowadzić do nieskończonego wykonania, jeśli warunek nigdy nie stanie się fałszywy:
```python
while True:
    odpowiedz = input("Czy chcesz zakończyć? (tak/nie): ")
    if odpowiedz == "tak":
        break  # Przerwanie pętli
```

---

## Instrukcje sterujące `break`, `continue`, `pass`
**`break` – przerywanie pętli**
```python
for liczba in range(10):
    if liczba == 5:
        break  # Przerywa pętlę, gdy liczba wynosi 5
    print(liczba)
```

**`continue` – pominięcie iteracji**
```python
for liczba in range(5):
    if liczba == 2:
        continue  # Pomija tylko tę iterację
    print(liczba)
```

**`pass` – pusta instrukcja (placeholder)**
Czasem musimy zostawić pusty blok kodu bez błędu składni:
```python
if True:
    pass  # Tymczasowy kod
```

---

## 📝 Zadania

1. Napisz program, który iteruje przez listę imion używając pętli `for` oraz funkcji `enumerate()`, aby wyświetlić indeks każdego imienia wraz z samym imieniem. 
2. Stwórz przykłady dla testów `if`: 
    - Gdzie wystąpią dwa warunki - napisz program sprawdzający czy dana liczba jest dodatnia i parzysta. Jeśli tak, program powinien wydrukować `Liczba jest dodatnia i parzysta`
    - Gdzie wykorzystane zostanie zaprzeczenie `not` lub `=!` - napisz program, który sprawdza, czy wprowadzona przez użytkownika liczba nie jest równa zero. Jeśli nie jest, wydrukuj `Liczba jest różna od zera` 
    - Gdzie wykorzystane będzie słowo `in` - napisz program, który sprawdza, czy wprowadzony przez użytkownika owoc znajduje się na liście dostępnych owoców (np. `['jabłko', 'banan', 'pomarańcza']`). Jeśli tak, program powinien wydrukować `Owoc jest dostępny`
3. Stwórz przykład z pętlą `while` - stwórz program, który będzie ciągle prosił użytkownika o wprowadzenie liczby. Program powinien sumować wprowadzone liczby i kończyć działanie, gdy suma przekroczy `100`. Po zakończeniu pętli, program powinien wydrukować sumę wprowadzonych liczb.