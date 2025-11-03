## Przykład

Wejdź do repozytorium, zapoznaj się z gotowym kodem w pliku `main_zajecia04.py` oraz modułami w folderze `python1course/zajecia04`.

Zwróć uwagę na:

- Tworzenie konstruktorów - `__init__`,
- Dodawanie metod,
- Tekstową reprezentację klas - `__str__` oraz `__repr__` (jako przykład przeciążania operatorów),

## Zadania 📝

Zaimplementuj dwie klasy w module `personnel`: `Employee` oraz `Driver`. Stwórz odpowiednie pliki w katalogu `python1course/zajecia04/personnel/`.

### Klasa `Employee`

Utwórz klasę `Employee` w pliku `employee.py` z następującymi wymaganiami:

1. **Konstruktor (`__init__`)**: Powinien przyjmować następujące parametry:
   - `first_name` (str) - imię pracownika
   - `last_name` (str) - nazwisko pracownika
   - `employee_id` (int) - unikalny identyfikator pracownika
   - `salary` (float) - wynagrodzenie pracownika

2. **Metody**:
   - `display_info()` - powinna wyświetlać informacje o pracowniku w formacie:
     ```
     Employee ID: {employee_id}, Name: {first_name} {last_name}, Salary: {salary} zł
     ```
   - `update_salary(new_salary)` - powinna aktualizować wynagrodzenie pracownika i wyświetlać komunikat: `Updated salary: {new_salary}`

3. **Metody specjalne**:
   - `__str__()` - powinna zwracać czytelną reprezentację tekstową obiektu w formacie: `Employee({first_name} {last_name}, ID: {employee_id})`
   - `__repr__()` - powinna zwracać jednoznaczną reprezentację obiektu w formacie: `Employee('{first_name}', '{last_name}', {employee_id}, {salary})`

### Klasa `Driver`

Utwórz klasę `Driver` w pliku `driver.py`, która dziedziczy po klasie `Employee`. Klasa powinna spełniać następujące wymagania:

1. **Konstruktor (`__init__`)**: Powinien przyjmować wszystkie parametry z klasy `Employee` oraz dodatkowo:
   - `license_number` (str) - numer licencji kierowcy
   - `qualifications` (list) - lista kwalifikacji kierowcy (lista stringów)

2. **Wykorzystanie dziedziczenia**: W konstruktorze wywołaj konstruktor klasy bazowej `Employee`, używając jednej z poniższych metod:
   ```python
   # Metoda 1: bezpośrednie wywołanie
   Employee.__init__(self, first_name, last_name, employee_id, salary)
   
   # Metoda 2: używając super() (zalecane)
   super().__init__(first_name, last_name, employee_id, salary)
   ```

3. **Przeciążenie metody `display_info()`**: Nadpisz metodę `display_info()` z klasy bazowej tak, aby zwracała string (zamiast wyświetlać) w formacie:
   ```
   Driver ID: {employee_id}, Name: {first_name} {last_name}, Salary: {salary}, License Number: {license_number}, Qualifications: {qualifications_joined}
   ```
   gdzie `qualifications_joined` to kwalifikacje połączone przecinkiem (np. `', '.join(self.qualifications)`).

4. **Metody specjalne**:
   - `__str__()` - powinna zwracać czytelną reprezentację tekstową obiektu w formacie: `Driver({first_name} {last_name}, ID: {employee_id}, License: {license_number})`
   - `__repr__()` - powinna zwracać jednoznaczną reprezentację obiektu w formacie: `Driver('{first_name}', '{last_name}', {employee_id}, {salary}, '{license_number}', {qualifications})`

### Plik `__init__.py`

Zaktualizuj plik `__init__.py` w katalogu `personnel/`, aby eksportował obie klasy:
```python
from .employee import Employee
from .driver import Driver

__all__ = ["Employee", "Driver"]
```

### Przykładowe użycie

Po zaimplementowaniu klas i odkomentowaniu kodu w `main_zajecia04.py`, plik ten powinien się uruchamiać bez błędów. Można też dodać swój kod, żeby zaprezentować działanie poszczególnych funkcjonalności.

### Uwagi

- Zwróć uwagę na poprawną inicjalizację klasy bazowej w konstruktorze klasy pochodnej.
- Upewnij się, że metoda `display_info()` w klasie `Employee` wyświetla informacje (`print()`), a w klasie `Driver` zwraca string.
- Zaimplementuj zarówno `__str__()` jak i `__repr__()` dla obu klas zgodnie z podanymi formatami. 