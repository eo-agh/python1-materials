### Przypisanie tworzy referencję, a nie kopię

W Pythonie przypisanie listy do innej zmiennej nie tworzy nowej kopii, lecz odniesienie do tej samej struktury danych.
```python
L = [1,2,3,4]
M = [1,2,3,L,4]
print(f"Wartość zmiennej M przed zmianą L: {M}")
L[1] = "woooow"
print(f"Wartość zmiennej M po zmianie L: {M}")
```

???- "Dlaczego tak się dzieje?"
    - `M` zawiera referencję do `L`, a nie jej kopię.
    - Gdy zmienimy element w `L`, zmiana jest widoczna również w `M`, ponieważ odwołuje się do tej samej instancji obiektu.

### Powtórzenie dodaje jeden poziom zagłębienia
```python
L = [4,5,6]
X = L * 4
Y = [L] * 4
print(f"X: {X}, Y: {Y}")
L[1] = "wow"
print(f"X: {X}, Y: {Y}")
```

???- "Dlaczego tak się dzieje?"
    - `X = L * 4` tworzy nową listę powielając zawartość `L` jako wartości skalarne
    - `Y = [L] * 4` powiela odniesienie do listy `L`, nie tworząc nowych instancji
    - Po zmianie elementu w `L`, efekt jest widoczny w `Y`, ale nie w `X`, ponieważ `X` zawiera wartości skopiowane

### Głębsza analiza kopiowania list
```python
L = [4,5,6]
Y = [list(L)] * 4
L[1] = "wow"
print(f"Y: {Y}")
Y[0][1] = "wow"
print(f"Y: {Y}")
```

???- "Dlaczego tak się dzieje?"
    - `[list(L)] * 4` tworzy listę zawierającą cztery referencje do **tej samej kopii `list(L)`**
    - Modyfikacja `L` **nie wpłynie** na `Y`, ponieważ `list(L)` tworzy nową instancję
    - Jednak zmiana `Y[0][1]` spowoduje zmianę we wszystkich elementach `Y`, ponieważ wszystkie odwołują się do tej samej listy