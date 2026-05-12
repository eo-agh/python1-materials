# Wstęp do projektu

## Czym jest ten projekt?

Ten szablon to punkt startowy do Twojego projektu zaliczeniowego. Zawiera **kompletny, działający stack technologiczny** typowy dla nowoczesnych aplikacji geoinformatycznych w chmurze:

- dane rastrowe jako COG serwowane przez **TiTiler**,
- dane wektorowe w **PostGIS** z dynamicznym kaflowaniem MVT przez **tipg**,
- katalog metadanych przestrzennych zgodny z **STAC**,
- REST API zbudowane w **FastAPI**,
- interaktywna aplikacja webowa w **Streamlit** z mapą **MapLibre GL**.

Wszystko działa w kontenerach **Docker**, środowiskiem Pythona zarządza **UV**.

Twoim zadaniem jest wybrać własne dane i rozwinąć aplikację - nie musisz budować infrastruktury od zera.

---

## Architektura systemu

```
              Przeglądarka
                   |
         +---------+---------+
         |                   |
    :8501 Streamlit      (MapLibre GL)
         |                   |
    :8000 FastAPI         :7800 TiTiler    :8008 tipg
         |                   |                |
         +-------------------+----------------+
                             |
                     :5432 PostGIS/pgSTAC
```

| Serwis | Port | Rola |
|--------|------|------|
| `frontend` | 8501 | Streamlit - interfejs użytkownika |
| `backend` | 8000 | FastAPI - własna logika API |
| `titiler` | 7800 | Serwer kafli rastrowych (COG) |
| `tipg` | 8008 | Serwer kafli wektorowych (MVT) + OGC API |
| `stac-api` | 8080 | Katalog STAC (stac-fastapi + pgSTAC) |
| `db` | 5432 | PostgreSQL + PostGIS + pgSTAC |

---

## Szybki start

### Wymagania

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (włączone Docker Compose)
- [Make](https://www.gnu.org/software/make/) (opcjonalnie, upraszcza komendy)
- Git

### Uruchomienie

```bash
git clone <adres-repo>
cd <nazwa-repo>

cp .env.example .env      # skopiuj konfigurację

make build                # zbuduj obrazy Docker
make init                 # uruchom serwisy i załaduj dane
```

Plik `.env` zawiera hasła i nazwy baz. Domyślne wartości działają od razu - nie musisz nic zmieniać. Jest on na liście `.gitignore`. Nigdy nie wypychaj do repozytorium plików z hasłami.

Pierwsze budowanie zajmuje kilka minut - pobierane są obrazy bazowe i instalowane są zależności Pythona.

Komenda `init` uruchamia wszystkie serwisy w tle, a następnie jednorazowo uruchamia skrypt ładujący dane, który:

- tworzy tabele PostGIS,
- ładuje przykładowe punkty z pliku GeoJSON,
- zapisuje kolekcje STAC i elementy (itemy) do pgSTAC.

Aplikacja jest dostępna pod adresem **[http://localhost:8501](http://localhost:8501)**.

| Serwis | URL |
|--------|-----|
| Streamlit (frontend) | [http://localhost:8501](http://localhost:8501) |
| FastAPI (backend) | [http://localhost:8000/docs](http://localhost:8000/docs) |
| STAC API | [http://localhost:8080](http://localhost:8080) |
| TiTiler | [http://localhost:7800/docs](http://localhost:7800/docs) |
| tipg | [http://localhost:8008](http://localhost:8008) |

### Przydatne komendy Make

```bash
make up        # uruchom wszystkie serwisy w tle
make down      # zatrzymaj serwisy
make logs      # podglądaj logi wszystkich serwisów
make restart s=frontend   # zrestartuj konkretny serwis
make shell s=backend      # terminal w kontenerze
```

---

## Co demonstruje przykładowy zbiór danych?

Przykładowy projekt pokazuje dane z okolic **Krakowa**:

| Dane | Technologia | Format |
|------|-------------|--------|
| Numeryczny Model Terenu (Copernicus DEM 30 m) | TiTiler | COG (4 kafle 1°×1°) |
| Lokalizacje zabytków i atrakcji | PostGIS + tipg | punkty (4326) |
| Metadane rastra | pgSTAC + STAC API | STAC Items/Collections |

Możesz zastąpić je dowolnymi danymi przestrzennymi - zob. [Rozszerzanie projektu](praca.md).

---

## Struktura projektu

```
.
├── backend/                  # FastAPI - własna logika API
│   ├── Dockerfile
│   ├── pyproject.toml
│   └── src/app/
│       ├── main.py           # inicjalizacja aplikacji, CORS, lifespan
│       ├── config.py         # zmienne środowiskowe (pydantic-settings)
│       ├── database.py       # klasa Database (pula połączeń asyncpg)
│       ├── models.py         # modele Pydantic (Location, LocationCollection)
│       ├── dependencies.py   # dependency injection (get_db)
│       └── routers/
│           └── locations.py  # endpointy /locations/
├── frontend/                 # Streamlit - interfejs użytkownika
│   ├── Dockerfile
│   ├── pyproject.toml
│   └── src/
│       ├── app.py            # główna aplikacja Streamlit
│       └── config.py         # adresy URL serwisów
├── stac-api/                 # STAC API (stac-fastapi + pgSTAC)
│   ├── Dockerfile
│   ├── pyproject.toml
│   └── main.py
├── tipg/                     # tipg - OGC API wektorowe
│   ├── Dockerfile
│   └── pyproject.toml
├── scripts/                  # skrypt jednorazowego ładowania danych
│   ├── Dockerfile
│   ├── pyproject.toml
│   └── ingest_data.py
├── data/                     # przykładowe dane
│   ├── sample_features.geojson
│   ├── stac_collection.json
│   └── stac_items.json
├── docker-compose.yml
├── .env.example
└── Makefile
```

---

## Jak działają poszczególne serwisy

### Baza danych (`db`)

Kontener `postgis/postgis` z zainstalowanym pgSTAC. Po starcie automatycznie inicjalizuje schemat pgSTAC.

Dane przechowywane są w wolumenie Dockera `pgdata` - pozostają między restartami kontenera.

Reset bazy:
```bash
docker compose down -v   # -v usuwa wolumeny!
make init
```

### Skrypt ładujący dane (`db-init`)

Jednorazowy kontener Pythona, który ładuje dane do bazy. Klasa `DataIngester` wykonuje trzy kroki:

1. `_setup_tables()` - tworzy schemat `app` i tabele w PostGIS,
2. `_load_vector_data()` - ładuje GeoJSON do tabeli `app.locations` (z TRUNCATE),
3. `_load_stac_catalog()` - usuwa stare itemy i ładuje kolekcje/itemy STAC.

Możesz uruchomić ponownie po zmianie danych:
```bash
docker compose run --rm db-init
```

### Backend FastAPI (`backend`)

Serwis z automatycznym przeładowaniem kodu (plik `src/` jest podmontowany jako wolumen). Zmiany w kodzie są widoczne natychmiast bez przebudowy obrazu.

Klasy:

- `Database` (`database.py`) - zarządza pulą połączeń `asyncpg`,
- `Location`, `LocationCollection` (`models.py`) - modele Pydantic odpowiedzi API.

### Frontend Streamlit (`frontend`)

Również z automatycznym przeładowaniem kodu. Każda zmiana w `src/app.py` jest widoczna po odświeżeniu przeglądarki.

Streamlit buforuje wyniki funkcji oznaczonych `@st.cache_data`. Po zmianie danych w bazie może być konieczne odświeżenie pamięci podręcznej - wystarczy kliknąć `C` lub poczekać na wygaśnięcie TTL (domyślnie 60 sekund).

---

## Debugowanie

### Podgląd logów serwisu

```bash
make logs              # wszystkie serwisy
docker compose logs -f backend    # tylko backend, na żywo
docker compose logs -f frontend   # tylko frontend
```

### Wejście do kontenera

```bash
make shell s=backend   # terminal w kontenerze backend
make shell s=db        # terminal w kontenerze bazy danych
```

### Zapytanie bezpośrednio do bazy

```bash
docker compose exec db psql -U postgres -d geoapp
```

Przykładowe zapytania:
```sql
-- Lista lokalizacji
SELECT id, name, category FROM app.locations;

-- Lokalizacje jako GeoJSON
SELECT name, ST_AsGeoJSON(geom) FROM app.locations LIMIT 3;

-- Lista kolekcji STAC
SELECT id FROM pgstac.collections;

-- Lista itemów STAC
SELECT id, collection FROM pgstac.items;
```
