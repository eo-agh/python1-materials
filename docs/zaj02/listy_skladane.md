## Listy składane

Lista składana to wyrażenie, które pozwala na tworzenie nowych list w zwięzły sposób, zwykle z użyciem pętli.

```python
kwadraty = [x ** 2 for x in range(6)]
print(kwadraty)
```

```python
liczby = [1, 2, 3, 4, 5, 6]
parzystosc = ['parzysta' if liczba % 2 == 0 else 'nieparzysta' for liczba in liczby]
print(parzystosc)
```
