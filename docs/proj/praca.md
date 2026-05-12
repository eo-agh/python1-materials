# Rozszerzanie projektu

## Filozofia szablonu

Szablon jest celowo prosty. Każda technologia ma swoje miejsce i pokazuje **jeden wzorzec**, który możesz powielać i rozszerzać:

| Co rozszerzasz | Gdzie | Jak |
|----------------|-------|-----|
| Własne dane wektorowe | `data/`, `scripts/ingest_data.py` | GeoJSON + nowa tabela PostGIS |
| Własne dane rastrowe | `data/stac_items.json` | Nowy STAC Item wskazujący na COG |
| Nowe endpointy API | `backend/src/app/routers/` | Nowy router FastAPI |
| Nowe warstwy na mapie | `frontend/src/app.py` | Nowe Source + Layer w MapLibre |
| Nowe widoki | `frontend/src/app.py` | Nowa funkcja `render_*_tab()` |

---

## Dodawanie własnych danych wektorowych

### Krok 1 - Przygotuj dane GeoJSON

Dane wektorowe muszą być w formacie **GeoJSON** z układem współrzędnych **WGS84 (EPSG:4326)**. Możesz użyć QGIS, GDAL lub GeoPandas do konwersji.

Przykładowa struktura dla punktów:

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": { "type": "Point", "coordinates": [19.94, 50.06] },
      "properties": { "name": "Moja lokalizacja", "kategoria": "pomiar" }
    }
  ]
}
```

Zapisz plik do katalogu `data/`.

### Krok 2 - Dodaj tabelę PostGIS w skrypcie ładującym dane

Otwórz `scripts/ingest_data.py` i rozszerz metodę `_setup_tables()`:

```python
def _setup_tables(self) -> None:
    with psycopg.connect(self._url) as conn:
        conn.execute("""
            CREATE SCHEMA IF NOT EXISTS app;

            CREATE TABLE IF NOT EXISTS app.moje_dane (
                id       SERIAL PRIMARY KEY,
                name     TEXT NOT NULL,
                kategoria TEXT,
                wartosc  FLOAT,
                geom     GEOMETRY(Point, 4326)
            );

            CREATE INDEX IF NOT EXISTS moje_dane_geom_idx
                ON app.moje_dane USING GIST (geom);
        """)
```

Dodaj metodę ładowania danych:

```python
def _load_moje_dane(self) -> None:
    geojson = json.loads((DATA_DIR / "moje_dane.geojson").read_text())
    features = geojson["features"]

    with psycopg.connect(self._url) as conn:
        conn.execute("TRUNCATE app.moje_dane RESTART IDENTITY")
        for feature in features:
            lon, lat = feature["geometry"]["coordinates"]
            props = feature["properties"]
            conn.execute(
                "INSERT INTO app.moje_dane (name, kategoria, wartosc, geom) "
                "VALUES (%s, %s, %s, ST_SetSRID(ST_MakePoint(%s, %s), 4326))",
                (props["name"], props.get("kategoria"), props.get("wartosc"), lon, lat),
            )
    print(f"Loaded {len(features)} moje_dane.")
```

I wywołaj ją w `run()`:

```python
def run(self) -> None:
    self._setup_tables()
    self._load_vector_data()
    self._load_moje_dane()    # <-- dodaj tutaj
    self._load_stac_catalog()
    print("Ingestion complete.")
```

### Krok 3 - Przeładuj dane

```bash
docker compose run --rm db-init
```

tipg **automatycznie** wykryje nową tabelę `app.moje_dane` i udostępni ją jako MVT i OGC API - bez żadnych zmian konfiguracji.

---

## Dodawanie własnych danych rastrowych (COG)

### Wymagania dla danych rastrowych

TiTiler pracuje najlepiej z plikami **Cloud Optimized GeoTIFF (COG)**. Jeśli masz zwykły GeoTIFF, możesz go przekonwertować za pomocą GDAL:

```bash
gdal_translate -of COG -co COMPRESS=LZW wejscie.tif wyjscie_cog.tif
```

Plik COG musi być dostępny pod publicznym URL (np. AWS S3, Google Cloud Storage, GitHub Releases).

W środowisku deweloperskim możesz serwować lokalne pliki COG przez prosty serwer HTTP i podać URL do kontenera przez nazwę serwisu. Na potrzeby projektu najłatwiej użyć publicznego magazynu (storage).

### Dodaj STAC Item

Otwórz `data/stac_items.json` i dodaj nowy wpis:

```json
{
  "type": "Feature",
  "stac_version": "1.0.0",
  "id": "moje-dane-rastrowe",
  "collection": "sample-imagery",
  "geometry": {
    "type": "Polygon",
    "coordinates": [[
      [minlon, minlat], [maxlon, minlat], [maxlon, maxlat], [minlon, maxlat], [minlon, minlat]
    ]]
  },
  "bbox": [minlon, minlat, maxlon, maxlat],
  "properties": {
    "datetime": "2024-01-01T00:00:00Z",
    "description": "Opis moich danych rastrowych"
  },
  "links": [],
  "assets": {
    "visual": {
      "href": "https://moj-storage.example.com/sciezka/do/pliku.tif",
      "type": "image/tiff; application=geotiff; profile=cloud-optimized",
      "title": "Moje dane (COG)",
      "roles": ["visual", "data"]
    }
  }
}
```

Ponownie załaduj dane:

```bash
docker compose run --rm db-init
```

Mapa automatycznie pobierze nowy item z STAC i wyświetli go jako warstwę.

---

## Dodawanie nowych endpointów FastAPI

### Struktura routera

Każdy router to osobny plik w `backend/src/app/routers/`. Skopiuj wzorzec z `locations.py`:

```python
# backend/src/app/routers/moje_dane.py
from fastapi import APIRouter, Depends
from app.database import Database
from app.dependencies import get_db
from app.models import MojModel   # zdefiniuj w models.py

router = APIRouter(prefix="/moje-dane", tags=["Moje dane"])

@router.get("/", response_model=list[MojModel])
async def list_moje_dane(db: Database = Depends(get_db)) -> list[MojModel]:
    rows = await db.fetch(
        "SELECT id, name, ST_AsGeoJSON(geom) AS geometry FROM app.moje_dane"
    )
    return [MojModel(...) for row in rows]
```

### Zarejestruj router w aplikacji

Dodaj w `backend/src/app/main.py`:

```python
from app.routers import locations, moje_dane   # <-- dodaj import

app.include_router(moje_dane.router)           # <-- dodaj rejestrację
```

Zmiany są widoczne natychmiast (automatyczne przeładowanie kodu). Sprawdź nowe endpointy pod [http://localhost:8000/docs](http://localhost:8000/docs).

---

## Dodawanie nowych warstw na mapie

### Warstwa wektorowa MVT (z tipg)

```python
# frontend/src/app.py - w funkcji build_map()

tipg_tiles_moje = (
    f"{settings.public_tipg_url}"
    "/collections/app.moje_dane/tiles/WebMercatorQuad/{z}/{x}/{y}"
)
m.add_source("moje-dane", VectorTileSource(tiles=[tipg_tiles_moje]))
m.add_layer(Layer(
    type=LayerType.CIRCLE,
    id="moje-dane-layer",
    source="moje-dane",
    source_layer="default",    # tipg zawsze używa "default"
    paint={
        "circle-color": "#2196F3",
        "circle-radius": 6,
        "circle-opacity": 0.85,
    },
))
```

### Warstwa rastrowa COG (z TiTiler)

```python
cog_url = urllib.parse.quote("https://moj-storage.example.com/dane.tif", safe="")
titiler_tiles = (
    f"{settings.public_titiler_url}"
    f"/cog/tiles/WebMercatorQuad/{{z}}/{{x}}/{{y}}"
    f"?url={cog_url}&colormap_name=viridis&rescale=0,100"
)
m.add_source("moj-raster", RasterTileSource(tiles=[titiler_tiles], tile_size=256))
m.add_layer(Layer(
    type=LayerType.RASTER,
    id="moj-raster-layer",
    source="moj-raster",
    paint={"raster-opacity": 0.8},
))
```

Dostępne mapy kolorów TiTiler: `terrain`, `viridis`, `plasma`, `magma`, `inferno`, `blues`, `rdylgn` i [wiele innych](https://developmentseed.org/titiler/endpoints/cog/#available-colormaps).

---

## Dodawanie nowych zakładek w Streamlit

Wzorzec dodawania nowej zakładki:

**1.** Napisz funkcję renderującą zakładkę:

```python
def render_moja_zakladka() -> None:
    st.subheader("Moje dane - analiza")

    # Pobierz dane z API backendu
    resp = httpx.get(f"{settings.backend_url}/moje-dane/", timeout=5)
    dane = resp.json()

    # Wyświetl tabelę
    st.dataframe(dane)

    # Wykres
    import pandas as pd
    df = pd.DataFrame(dane)
    st.bar_chart(df.set_index("name")["wartosc"])
```

**2.** Dodaj zakładkę w funkcji `main()`:

```python
def main() -> None:
    ...
    tabs = st.tabs(["Mapa", "FastAPI", "TiTiler", "STAC", "tipg", "Moje dane"])
    ...
    with tabs[5]:
        render_moja_zakladka()
```

---

## Checklist projektu

Przed oddaniem sprawdź, czy Twój projekt:

- [ ] Zawiera **własne dane** (nie tylko przykładowe z Krakowa)
- [ ] Dane są **sensowne geograficznie** i mają wybrany przez Ciebie kontekst
- [ ] Istnieje co najmniej **jeden endpoint FastAPI** z własną logiką przestrzenną
- [ ] Na mapie są widoczne **warstwy wektorowe** z PostGIS/tipg
- [ ] Na mapie są widoczne **warstwy rastrowe** serwowane przez TiTiler
- [ ] STAC API zawiera **przynajmniej jedną kolekcję** z opisanymi itemami
- [ ] Kod Pythona korzysta z **klas i OOP** (wzorzec ze szablonu)
- [ ] Wszystko działa przez `make build && make init`
- [ ] `README.md` opisuje Twój projekt i użyte dane
