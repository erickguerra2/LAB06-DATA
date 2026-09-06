# Laboratorio 6. Análisis de redes sociales en YouTube

CC3084 Data Science, Universidad del Valle de Guatemala, Semestre II 2026.

Análisis de la estructura de participación de usuarios de YouTube a partir de dos conjuntos de
datos: 293 videos con información de sus canales y 406 comentarios principales publicados en una
selección de esos videos. El trabajo cubre la carga e integración de los datos, el diagnóstico de
calidad y la limpieza de texto, el análisis exploratorio, la construcción de una red bipartita
autor-video con sus dos proyecciones, el análisis de topología, comunidades y centralidad, el
análisis de sentimiento en español y la interpretación final con limitaciones y conclusiones.

## Contenido del repositorio

| Ruta | Descripción |
| --- | --- |
| `Laboratorio6.ipynb` | Notebook con todo el análisis, ejercicios 1 al 10, ejecutado de principio a fin |
| `data/youtube_videos.csv` | Conjunto de videos, 293 filas y 20 variables |
| `data/youtube_comments.csv` | Conjunto de comentarios, 406 filas y 17 variables |
| `salidas/tabla_nodos_bipartita.csv` | Tabla de nodos de la red bipartita, con tipo y atributos |
| `salidas/tabla_aristas_bipartita.csv` | Tabla de aristas de la red bipartita, con peso y etiquetas |
| `requirements.txt` | Dependencias de Python |

Las carpetas `salidas/` y `.nltk_data/` se generan al ejecutar el notebook.

## Requisitos

- Python 3.11 o superior
- Alrededor de 1 GB de espacio libre, principalmente por PyTorch y el modelo de sentimiento
- Conexión a internet la primera vez, para descargar el modelo de spaCy, las stopwords de NLTK y el
  modelo de sentimiento desde Hugging Face. Después de la primera ejecución todo queda en caché.

## Instalación

```bash
git clone <url-del-repositorio>
cd Lab6

python -m venv .venv
# Windows
.venv\Scripts\activate
# Linux o macOS
source .venv/bin/activate

pip install -r requirements.txt
python -m spacy download es_core_news_sm
```

El paso de `spacy download` es opcional: la primera celda del notebook lo ejecuta sola si el modelo
no está instalado, igual que la descarga de las stopwords de NLTK.

## Ejecución

```bash
jupyter lab Laboratorio6.ipynb
```

Después use la opción de reiniciar el kernel y ejecutar todas las celdas. El notebook corre de
arriba hacia abajo sin ningún paso manual y tarda alrededor de un minuto en una máquina con CPU.
También se puede ejecutar sin abrir la interfaz:

```bash
jupyter nbconvert --to notebook --execute --inplace Laboratorio6.ipynb
```

Ejecute siempre desde la raíz del repositorio, porque las rutas a los datos son relativas.

## Dependencias principales

| Paquete | Uso |
| --- | --- |
| pandas, numpy | Manipulación de datos y cálculos |
| networkx | Construcción de la red bipartita, proyecciones, métricas de topología, centralidad y detección de comunidades con Louvain |
| spacy con `es_core_news_sm` | Lematización en español para el texto limpio |
| nltk | Lista de stopwords en español |
| emoji | Extracción y eliminación de emojis del texto |
| pysentimiento | Análisis de sentimiento en español, modelo RoBERTuito afinado sobre el corpus TASS |
| matplotlib | Todas las visualizaciones |
| wordcloud | Nube de palabras del punto 3.4 |

## Reproducibilidad

- Todas las semillas aleatorias están fijadas en la constante `SEMILLA`, que vale 42. Esto afecta
  el layout de las redes, la partición de Louvain y la nube de palabras.
- La estabilidad de la detección de comunidades se verifica dentro del notebook repitiendo Louvain
  con cinco semillas distintas y comparando la partición ponderada contra la no ponderada.
- Los corpus de NLTK se descargan a `.nltk_data/` dentro del proyecto en lugar de la carpeta por
  defecto del sistema. En Windows la carpeta por defecto puede quedar redirigida por la Microsoft
  Store y el lector de corpus la rechaza.
- Ninguna celda modifica los archivos de `data/`.

## Notas metodológicas

- Los comentarios se conservan en dos versiones. `texto_original` queda intacto para auditoría y
  para el análisis de sentimiento, que aprovecha mayúsculas, puntuación y emojis. `texto_limpio`
  pasa por minúsculas, eliminación de URL, separación de hashtags y menciones, eliminación de
  puntuación, números y emojis, filtrado de stopwords y lematización con spaCy.
- Los emojis se extraen a su propia columna y se analizan aparte. No se convierten a texto, porque
  eso inyecta palabras que el usuario nunca escribió y contamina las frecuencias.
- `reply_count` nunca se usa como arista. Indica cuántas respuestas recibió un comentario pero no
  identifica a sus autores, así que no permite construir relaciones entre usuarios.
- Los identificadores `video_id`, `channel_id`, `comment_id` y `author_channel_id` se usan como
  llaves en todo el análisis. Los nombres visibles y los handles solo se usan como etiquetas.
- Solo 19 de los 293 videos tienen comentarios recolectados. Todas las conclusiones sobre red,
  comunidades, centralidad y sentimiento se refieren a esos 19 videos y no al conjunto completo.
