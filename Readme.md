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

El análisis sobre 114.000 canciones de Spotify permite responder esta pregunta con datos concretos. El metal no es mainstream porque su perfil de atributos es estructuralmente opuesto al perfil de las canciones más populares en casi todas las dimensiones relevantes.

### Respuesta a hipótesis

**H6 — El metal es el opuesto del mainstream**
Las canciones más populares de Spotify tienen alta danceability, alta valence y baja instrumentalness. El metal tiene exactamente lo contrario — baja danceability, baja valence y alta instrumentalness. La distancia euclidiana entre ambos perfiles confirma que no es una diferencia marginal sino estructural. El metal no está cerca del mainstream — está en el extremo opuesto.

**H7 — Alt-rock y alternative son el puente**
Dentro de los subgéneros de metal y rock, alt-rock y alternative son los más cercanos al mainstream. Tienen mayor danceability y valence que el metal o el heavy-metal, y menor instrumentalness. El grunge y el heavy-metal son los más alejados. Esto sugiere que los subgéneros que incorporan elementos más melódicos y emocionales tienen más chances de penetrar al público masivo.

**H8 — El metal popular traiciona al género**
Las canciones de metal que logran mayor popularidad relativa dentro del género tienen un perfil más cercano al mainstream que las menos populares. Más danceability, más valence, menos instrumentalness. El metal que más éxito tiene es el que más se aleja de las características que definen al género. Es una trampa estructural — para ser popular hay que dejar de sonar a metal.

**H9 — Hip-hop y reggaeton encajan perfecto con el algoritmo**
El hip-hop y el reggaeton lideran en popularidad porque maximizan exactamente los atributos que más se asocian con popularidad en Spotify — danceability y valence altas — y minimizan los que se asocian negativamente — instrumentalness y acousticness bajas. No es casualidad. Su dominio del mainstream refleja una alineación casi perfecta con lo que el algoritmo de Spotify premia y recomienda.

### Hallazgo central

El declive del metal en el mainstream no es solo cultural — está codificado en los datos. El algoritmo de Spotify favorece canciones bailables, alegres y con voz prominente. El metal es exactamente lo contrario en las tres dimensiones. Mientras el hip-hop y el reggaeton evolucionaron hacia formatos que el algoritmo premia, el metal mantuvo su identidad sonora. El costo de esa integridad es la invisibilidad algorítmica.

### Limitaciones del análisis

- La popularidad de Spotify no equivale a popularidad cultural total. Un artista puede ser masivo en vivo o en radio y tener baja popularidad en la plataforma.
- El dataset no tiene fechas de lanzamiento confiables, por lo que no fue posible analizar la evolución temporal del metal desde los 90 hasta hoy.
- La popularidad en Spotify está influenciada por factores externos al audio — marketing, playlists editoriales, viralidad en redes sociales — que estos datos no capturan.
- La correlación no implica causalidad. Que danceability correlacione con popularidad no significa que hacer una canción más bailable la vuelva más popular.

---

## Estructura del repositorio

```
tp1-eda-spotify/
│
├── README.md                          # Este archivo
├── dataset.csv                        # Dataset original de Kaggle
└── TP1_EDA_Spotify.ipynb             # Notebook con el análisis completo
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
