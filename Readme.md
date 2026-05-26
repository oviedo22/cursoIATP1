# TP1 — Análisis Exploratorio de Datos (EDA)
## Dataset: Spotify Tracks

---

## Objetivo del trabajo

El objetivo de este trabajo es realizar un análisis exploratorio de datos (EDA) sobre un dataset de canciones de Spotify, con el fin de responder hipótesis concretas sobre qué diferencia musicalmente al metal y el rock de los géneros mainstream, y por qué estos últimos dominan la popularidad en la plataforma.

A través del tratamiento, limpieza y visualización de los datos, se busca identificar patrones estructurales en los atributos de audio que expliquen el posicionamiento de cada género en el ecosistema algorítmico de Spotify.

---

## Contexto del dataset

**Fuente:** [Spotify Tracks Dataset — Kaggle](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset)

El dataset contiene información de más de 114.000 canciones extraídas de la API de Spotify, distribuidas en 114 géneros musicales. Cada canción está descripta por un conjunto de atributos de audio calculados algorítmicamente por Spotify, junto con métricas de popularidad actualizadas en tiempo real por la plataforma.

**Dimensiones:** 114.000 filas × 20 columnas

**Tipos de datos presentes:**
- Numéricos continuos (danceability, energy, valence, tempo, etc.)
- Numéricos enteros (popularity, key, time_signature)
- Booleanos (explicit)
- Texto / categóricos (track_name, artists, album_name, track_genre)

---

## Diccionario de datos

| Columna | Tipo | Descripción |
|---|---|---|
| `track_id` | string | Identificador único de la canción en Spotify |
| `artists` | string | Nombre del artista o artistas |
| `album_name` | string | Nombre del álbum |
| `track_name` | string | Nombre de la canción |
| `popularity` | int (0-100) | Popularidad calculada por Spotify en base a streams recientes |
| `duration_ms` | int | Duración de la canción en milisegundos |
| `explicit` | bool | Si la canción contiene contenido explícito |
| `danceability` | float (0-1) | Qué tan adecuada es la canción para bailar |
| `energy` | float (0-1) | Intensidad y actividad perceptual de la canción |
| `key` | int (0-11) | Tonalidad musical (0 = Do, 1 = Do#, etc.) |
| `loudness` | float (dB) | Volumen promedio en decibeles (valores negativos) |
| `mode` | int (0-1) | Modalidad: 1 = mayor, 0 = menor |
| `speechiness` | float (0-1) | Presencia de palabras habladas en la canción |
| `acousticness` | float (0-1) | Probabilidad de que la canción sea acústica |
| `instrumentalness` | float (0-1) | Probabilidad de que la canción no tenga voz |
| `liveness` | float (0-1) | Probabilidad de que la canción esté grabada en vivo |
| `valence` | float (0-1) | Positividad emocional (0 = triste/oscura, 1 = alegre/positiva) |
| `tempo` | float (BPM) | Velocidad de la canción en beats por minuto |
| `time_signature` | int | Compás de la canción (3, 4, 5, etc.) |
| `track_genre` | string | Género musical asignado por Spotify |

**Columnas derivadas creadas durante el análisis:**

| Columna | Descripción |
|---|---|
| `duration_min` | Duración en minutos (duration_ms / 60000) |
| `grupo` | Agrupación en Metal/Rock, Mainstream o Referencia |
| `pop_quartile` | Cuartil de popularidad (Q1 a Q4) |
| `metal_pop_quartile` | Cuartil de popularidad calculado dentro del metal |

---

## Metodología aplicada

### 1. Carga y exploración inicial
Se cargó el dataset desde el archivo CSV usando pandas, verificando dimensiones, tipos de datos y estadísticas descriptivas generales (`.info()`, `.describe()`).

### 2. Limpieza de datos
Se aplicaron los siguientes pasos en orden:

- **Valores nulos:** se identificaron y eliminaron las filas con valores nulos mediante `dropna()`. La cantidad fue mínima (menos del 0.1% del dataset).
- **Duplicados:** se verificaron duplicados considerando el par `track_id + track_genre`, respetando que una misma canción puede aparecer en múltiples géneros de forma legítima.
- **Tipos de datos:** se corrigió el tipo de `explicit` a booleano y se creó la columna `duration_min` a partir de `duration_ms`.
- **Filtro de popularidad 0:** se eliminaron las canciones con popularidad igual a 0, ya que este valor no representa canciones impopulares sino canciones sin datos suficientes en el algoritmo de Spotify. Este filtro se documentó visualmente comparando la distribución antes y después.

### 3. Selección de géneros y atributos
Se seleccionaron 12 géneros representativos agrupados en tres categorías:

- **Metal/Rock:** `metal`, `heavy-metal`, `alt-rock`, `alternative`, `grunge`
- **Mainstream:** `pop`, `hip-hop`, `reggaeton`, `latin`, `dance`
- **Referencia:** `jazz`, `classical`

Los atributos de análisis fueron: `popularity`, `danceability`, `energy`, `valence`, `instrumentalness`, `speechiness`, `tempo`, `loudness`, `acousticness`, `explicit`.

### 4. Construcción del perfil mainstream
Se dividió el dataset completo en cuartiles de popularidad usando `pd.qcut()`. El perfil del Q4 (25% de canciones más populares) se usó como referencia para todas las comparaciones. Para comparaciones multidimensionales se aplicó normalización MinMaxScaler y distancia euclidiana.

### 5. Visualizaciones
Se generaron las siguientes visualizaciones:

- 3 histogramas con KDE (danceability, energy y valence por género)
- 3 boxplots con línea de referencia mainstream (danceability, valence, instrumentalness)
- 2 scatterplots con líneas de tendencia (danceability vs popularidad, instrumentalness vs popularidad)
- Radar charts para comparación de perfiles multidimensionales
- Heatmaps de atributos normalizados
- Gráficos de barras horizontales para rankings
- Gráfico de torta para proporción de contenido explícito
- Gráfico de líneas para perfil comparativo por grupo

### 6. Análisis estadístico
- Correlación de Pearson entre atributos de audio y popularidad
- Distancia euclidiana sobre perfiles normalizados para medir similitud entre géneros
- Estadísticas descriptivas (media, mediana, desviación estándar) por género y cuartil

---

## Conclusiones y hallazgos relevantes

### ¿Por qué el metal no es mainstream hoy en día?

El análisis sobre 114.000 canciones de Spotify permite responder esta pregunta con datos concretos.

### Respuesta a hipótesis

**H6 — El metal es opuesto al mainstream en acousticness y danceability, pero no en todo**
La mayor diferencia entre el mainstream y el metal está en `acousticness` (+0.249 a favor del mainstream) y `danceability` (+0.119). Sorprendentemente, el metal supera al mainstream en `energy` (-0.196, es decir el metal tiene más) y en `loudness` y `tempo`. La distancia euclidiana confirma que metal (2.53) y heavy-metal (2.37) están estructuralmente alejados del mainstream, siendo el heavy-metal levemente más cercano.

**H7 — Alternative es el subgénero más cercano al mainstream**
El ranking de distancia euclidiana al mainstream es claro: `alternative` (1.51) y `alt-rock` (1.73) son los más cercanos, mientras que `grunge` (2.35) es el más lejano. Esto confirma que los subgéneros que incorporan elementos más melódicos y menos agresivos tienen mayor afinidad con el perfil de canciones populares.

**H8 — CONFIRMADA: el metal popular traiciona al género**
Las canciones de metal con mayor popularidad relativa (Q4) tienen una distancia de 1.99 al mainstream, mientras que las menos populares (Q1) tienen 2.01. La diferencia es pequeña (+0.023) pero consistente — el metal que logra popularidad tiene más danceability (0.468 vs 0.434), más valence (0.425 vs 0.379) y menos instrumentalness (0.052 vs 0.106). Para tener éxito dentro del género, hay que alejarse de sus características más extremas.

**H9 — PARCIALMENTE CONFIRMADA con un hallazgo inesperado**
Las correlaciones con popularidad son sorprendentemente bajas en todo el dataset: el atributo más correlacionado es `danceability` con apenas 0.06. Esto indica que los atributos de audio por sí solos explican muy poco de la popularidad en Spotify. Sin embargo, el ranking de géneros en los atributos premiados confirma que reggaeton (0.758), latin (0.738) y hip-hop (0.715) lideran en danceability, mientras que metal (0.468) y heavy-metal (0.428) están al final. Curiosamente, en popularidad promedio `pop` lidera (58.4) seguido de `metal` (55.0) y `hip-hop` (53.3), lo que refuerza que la popularidad depende de factores más allá del audio.

### Hallazgo central

El metal no es mainstream principalmente porque tiene baja `acousticness` y baja `danceability` respecto al perfil de canciones populares — no por su alta energía, que no penaliza la popularidad tanto como se esperaba. Las correlaciones bajas entre atributos de audio y popularidad revelan que el algoritmo de Spotify está influenciado por factores externos al sonido: marketing, playlists editoriales y viralidad en redes sociales pesan más que cualquier característica musical. El metal mantiene su identidad sonora, pero esa identidad lo deja fuera de los circuitos de recomendación masiva.

### Limitaciones del análisis

- Las correlaciones entre atributos de audio y popularidad son muy bajas (máximo 0.06), lo que indica que el audio explica poco de la popularidad real en Spotify.
- La popularidad de Spotify no equivale a popularidad cultural total. Un artista puede ser masivo en vivo o en radio y tener baja popularidad en la plataforma.
- El dataset no tiene fechas de lanzamiento confiables, por lo que no fue posible analizar la evolución temporal del metal desde los 90 hasta hoy.
- La correlación no implica causalidad. Que danceability correlacione con popularidad no significa que hacer una canción más bailable la vuelva más popular.

---

## Estructura del repositorio

```
tp1-eda-spotify/
│
├── README.md                              # Este archivo
├── dataset.csv                            # Dataset original de Kaggle (no incluido en el repo)
└── TP1_EDA_Spotify_Oviedo.ipynb          # Notebook con el análisis completo
```

---

## Requisitos

```
pandas
numpy
matplotlib
seaborn
scikit-learn
```

Instalación:
```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

---

*Trabajo Práctico N°1 — Análisis Exploratorio de Datos*
*Curso de IA con Machine Learning*