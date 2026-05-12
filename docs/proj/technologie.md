# Zastosowane technologie

## Docker i Docker Compose

[Docker](https://docs.docker.com/) to platforma do uruchamiania aplikacji w izolowanych **kontenerach**. Każdy kontener to oddzielny proces z własnym systemem plików, siecią i zależnościami - dzięki temu aplikacja działa identycznie na każdej maszynie.

[Docker Compose](https://docs.docker.com/compose/) pozwala definiować i uruchamiać **wiele kontenerów naraz** za pomocą jednego pliku `docker-compose.yml`. W tym projekcie Compose koordynuje 6 serwisów.

**Kluczowe pojęcia:**

| Pojęcie | Opis |
|---------|------|
| `image` | Szablon kontenera (np. `postgis/postgis`) |
| `container` | Uruchomiona instancja obrazu |
| `volume` | Współdzielony katalog między hostem a kontenerem |
| `network` | Wirtualna sieć łącząca kontenery |
| `healthcheck` | Automatyczne sprawdzanie, czy serwis działa |

**Gdzie to widzisz w projekcie:** `docker-compose.yml` w katalogu głównym.

---

## UV - zarządzanie środowiskiem Python

[UV](https://docs.astral.sh/uv/) to nowoczesny, bardzo szybki menedżer pakietów i wirtualnych środowisk dla Pythona (zamiennik dla `pip` + `venv`).

W projekcie UV jest używany wewnątrz każdego Dockerfile - instaluje zależności z `pyproject.toml`:

```dockerfile
RUN uv sync --frozen --no-cache
```

Zależności projektu definiujesz w `pyproject.toml` (nie w `requirements.txt`):

```toml
[project]
dependencies = [
    "fastapi>=0.110",
    "asyncpg>=0.29",
]
```

**Gdzie to widzisz w projekcie:** `*/Dockerfile` i `*/pyproject.toml` w każdym serwisie.

---

## FastAPI

[FastAPI](https://fastapi.tiangolo.com/) to nowoczesny framework HTTP dla Pythona oparty na adnotacjach typów. Automatycznie generuje dokumentację OpenAPI (Swagger UI).

**Kluczowe cechy używane w projekcie:**

- **dekoratory tras** - `@router.get("/locations/")` definiuje endpoint,
- **modele Pydantic** - automatyczna walidacja wejścia i wyjścia,
- **wstrzykiwanie zależności (dependency injection)** - `Depends()` podstawia połączenie z bazą,
- **async/await** - nieblokujące zapytania do bazy przez `asyncpg`,
- **lifespan** - inicjalizacja puli połączeń przy starcie aplikacji.

```python
@router.get("/locations/", response_model=LocationCollection)
async def list_locations(db: Database = Depends(get_db)) -> LocationCollection:
    rows = await db.fetch("SELECT id, name, category, ST_AsGeoJSON(geom) FROM app.locations")
    ...
```

Dokumentacja API dostępna pod: **[http://localhost:8000/docs](http://localhost:8000/docs)**

**Gdzie to widzisz w projekcie:** `backend/src/app/`

---

## PostGIS

[PostGIS](https://postgis.net/) to rozszerzenie PostgreSQL dodające obsługę **typów geometrycznych** i **funkcji przestrzennych**. To de facto standard baz danych przestrzennych open source.

**Kluczowe funkcje używane w projekcie:**

| Funkcja SQL | Co robi |
|-------------|---------|
| `ST_MakePoint(lon, lat)` | Tworzy punkt ze współrzędnych |
| `ST_SetSRID(geom, 4326)` | Ustawia układ współrzędnych (WGS84) |
| `ST_AsGeoJSON(geom)` | Konwertuje geometrię do GeoJSON |
| `ST_Within(geom, bbox)` | Sprawdza, czy punkt leży w obszarze |
| `ST_MakeEnvelope(minx,miny,maxx,maxy,srid)` | Tworzy prostokąt ze współrzędnych |

Dane są przechowywane w kolumnie typu `GEOMETRY(Point, 4326)`:

```sql
CREATE TABLE app.locations (
    id       SERIAL PRIMARY KEY,
    name     TEXT NOT NULL,
    category TEXT NOT NULL,
    geom     GEOMETRY(Point, 4326)
);
```

**Gdzie to widzisz w projekcie:** `scripts/ingest_data.py`, `backend/src/app/routers/locations.py`

---

## pgSTAC i STAC API

### STAC - SpatioTemporal Asset Catalog

[STAC](https://stacspec.org/) to otwarty standard opisywania danych geoprzestrzennych (zwłaszcza rastrowych). Definiuje strukturę metadanych:

- **Collection** - zbiór danych (np. "Copernicus DEM GLO-30"),
- **Item** - pojedynczy zasób (np. jeden kafel DEM) z bbox, datetime i listą assetów,
- **Asset** - konkretny plik (np. URL do pliku `.tif`).

### pgSTAC

[pgSTAC](https://github.com/stac-utils/pgstac) to implementacja katalogu STAC bezpośrednio w PostgreSQL. Przechowuje Items i Collections w specjalnych tabelach i udostępnia wydajne funkcje wyszukiwania.

### stac-fastapi

[stac-fastapi](https://github.com/stac-utils/stac-fastapi) to serwer STAC API zbudowany na FastAPI, który odpytuje pgSTAC w tle.

Przykładowe zapytanie do STAC API:

```bash
# Wyszukaj itemy w bbox wokół Krakowa
curl -X POST http://localhost:8080/search \
  -H "Content-Type: application/json" \
  -d '{"bbox": [18.0, 49.0, 21.0, 51.5], "datetime": "2020-01-01T00:00:00Z/.."}'
```

**Gdzie to widzisz w projekcie:** `stac-api/`, `data/stac_collection.json`, `data/stac_items.json`, `scripts/ingest_data.py`

---

## TiTiler

[TiTiler](https://developmentseed.org/titiler/) to serwer kafli rastrowych (kafle XYZ) dla plików **Cloud Optimized GeoTIFF (COG)**. Generuje kafle PNG/WebP na żywo, bezpośrednio z pliku w chmurze - bez potrzeby przechowywania ich lokalnie.

**Cloud Optimized GeoTIFF (COG)** to format TIFF zoptymalizowany do strumieniowania: plik jest ułożony tak, że serwer może pobrać tylko fragment odpowiadający żądanemu kaflowi (dzięki żądaniom HTTP Range).

**Przydatne endpointy TiTiler:**

| Endpoint | Opis |
|----------|------|
| `/cog/info?url=<url>` | Metadane pliku COG |
| `/cog/statistics?url=<url>` | Statystyki wartości pikseli |
| `/cog/tiles/{z}/{x}/{y}?url=<url>` | Pojedynczy kafel XYZ |
| `/cog/tilejson.json?url=<url>` | Metadane zgodne z TileJSON |

Parametry wizualizacji można przekazać w URL:

- `colormap_name=terrain` - mapa kolorów (terrain, viridis, plasma, ...),
- `rescale=100,1000` - zakres wartości mapowany na skalę kolorów,
- `bidx=1` - numer pasma.

**Gdzie to widzisz w projekcie:** `docker-compose.yml` (serwis `titiler`), `frontend/src/app.py`

---

## tipg

[tipg](https://github.com/developmentseed/tipg) to serwer implementujący **OGC API Features** i **OGC API Tiles** dla danych wektorowych z PostGIS. Automatycznie wykrywa tabele w schemacie i udostępnia je jako:

- **GeoJSON** (`/collections/{tabela}/items`) - dane jako GeoJSON FeatureCollection,
- **MVT** (`/collections/{tabela}/tiles/{z}/{x}/{y}`) - dane jako Mapbox Vector Tiles.

**Mapbox Vector Tiles (MVT)** to binarny format kafli wektorowych - klient (MapLibre) renderuje geometrię po stronie przeglądarki, co daje wysoką wydajność i elastyczność stylowania.

W kaflach MVT generowanych przez tipg warstwa zawsze nazywa się `"default"`, niezależnie od nazwy tabeli w PostGIS.

**Gdzie to widzisz w projekcie:** `tipg/`, `docker-compose.yml` (serwis `tipg`), `frontend/src/app.py`

---

## Streamlit

[Streamlit](https://streamlit.io/) to framework Pythona do szybkiego tworzenia interaktywnych aplikacji webowych - bez pisania HTML/CSS/JS. Każda zmiana stanu (np. kliknięcie przycisku) wywołuje ponowne wykonanie całego skryptu.

**Kluczowe mechanizmy używane w projekcie:**

- `st.tabs()` - podział na zakładki,
- `st.columns()` - układ kolumnowy,
- `st.checkbox()` - przełącznik warstwy,
- `st.cache_data(ttl=60)` - bufor wyników zapytań HTTP,
- `st.session_state` - stan między kolejnymi renderowaniami.

**Gdzie to widzisz w projekcie:** `frontend/src/app.py`

---

## MapLibre GL

[MapLibre GL](https://maplibre.org/) to biblioteka JavaScript do interaktywnych map wektorowych w przeglądarce. W projekcie używana przez wrapper Pythona [`maplibre`](https://eodagmbh.github.io/py-maplibregl/).

**Kluczowe pojęcia:**

| Pojęcie | Opis |
|---------|------|
| `Map` | Główny obiekt mapy |
| `Source` | Źródło danych (RasterTileSource, VectorTileSource) |
| `Layer` | Warstwa wizualna oparta na Source |
| `Layout` | Właściwości układu warstwy (np. `visibility`) |
| `Paint` | Styl warstwy (kolor, opacity, rozmiar) |

```python
m.add_source("locations", VectorTileSource(tiles=[tipg_url]))
m.add_layer(Layer(
    type=LayerType.CIRCLE,
    source="locations",
    source_layer="default",
    paint={"circle-color": "#e63946", "circle-radius": 8},
))
```

**Gdzie to widzisz w projekcie:** `frontend/src/app.py`
