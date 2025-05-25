# Proyecto PLN - Estimación del salario en función de la oferta

**Grupo 3**  

**Componentes**: Sebastia Garcia, Sergio (Lider) / Dionis Ros, Alejandro / Lizzadro Pla, Adrian / Zhigarev, Ilia

**Fuente de datos**: Web Scraping ([GetManfred.com](https://www.getmanfred.com/))  

**Tema**: Ofertas de Trabajo  

**Objetivo principal**: Estimación del salario en función de la oferta  

---

## 1. Introducción

Este proyecto se enmarca dentro del curso de Procesamiento de Lenguaje Natural (PLN) y tiene como propósito el análisis de ofertas laborales con el objetivo de estimar el salario a partir del contenido textual de dichas ofertas.

---

## 2. Metodología

### 2.1 Extracción de datos (Web Scraping)

La recolección de datos se realizó desde la plataforma [GetManfred.com](https://www.getmanfred.com), especializada en ofertas de empleo para perfiles tecnológicos. Este portal permite el scraping según su archivo `robots.txt`.

Se utilizaron las librerías `requests` y `BeautifulSoup` para automatizar la extracción de información. El proceso fue el siguiente:

- **Acceso al sitemap**: se descargó el archivo público `https://www.getmanfred.com/sitemap-offers.xml` que contiene enlaces a todas las ofertas.
- **Filtrado de URLs**: se seleccionaron aquellas que contenían `/ofertas-empleo/`, correspondientes a ofertas laborales.
- **Descarga y análisis HTML**: para cada oferta, se realizó una petición HTTP y se procesó el HTML usando el parser `html.parser`.

De cada página de oferta se extrajeron los siguientes campos:

- `title`: Título del puesto (`<h1>`)
- `salary`: Información salarial (texto dentro de un `<h3>` que contiene la palabra "Salario", luego el `<span>` asociado)
- `skills`: Competencias técnicas (`<div id="skills">`)
- `job_description`: Descripción del puesto (`<div id="requisitos">`)
- `location`: Ubicación del trabajo (`<div id="donde">`)
- `is_offer_closed`: Indicador de si la oferta está cerrada (se detecta mediante la cadena "Oferta cerrada" en el HTML)

Este proceso generó un dataset estructurado con 1.295 observaciones iniciales, que luego se filtraron por idioma (español) para el análisis posterior.

---

### 2.2 Limpieza y preprocesamiento del texto

Se aplicaron múltiples transformaciones al texto con el objetivo de normalizar y reducir la complejidad léxica de las descripciones. Estas operaciones se realizaron principalmente sobre los campos: descripción del puesto, habilidades y localización. Para el título del puesto, solo se aplicó conversión a minúsculas.

Transformaciones aplicadas:

- **Conversión a minúsculas**: para unificar el texto (`.lower()`)
- **Eliminación de signos de puntuación**: usando `string.punctuation` y expresiones regulares
- **Eliminación de acentos**: mediante `str.translate` para sustituir vocales acentuadas por su forma simple
- **Eliminación de caracteres especiales y emojis**: usando la librería `emoji` y funciones de reemplazo específicas
- **Lematización**: con el modelo `es_core_news_lg` de spaCy, obteniendo la forma canónica de cada palabra (`lemma_`)
- **Eliminación de stopwords**: empleando el listado en español incluido en spaCy

Además, se creó una nueva columna `full_description`, que combina: **título + descripción + habilidades + localización**, y se utilizó como texto principal para el modelado.

Se aplicaron dos enfoques principales para la estimación del salario:

## 3. Modelos

#### Modelos propios

Se probaron varias técnicas de vectorización:

- **Bag of Words (BoW)**
- **TF-IDF**
- **Word2Vec**
- **FastText**
- **Doc2Vec**

Estas representaciones se usaron como entrada para modelos de regresión:

- **Ridge**
- **Lasso**
- **Random Forest**
- **XGBoost**

Los modelos se entrenaron sobre la variable `salary_int` (salario máximo en miles de euros) y se evaluaron con las métricas **MSE**, **MAE** y **R²**.

> **Mejores resultados:**  
> Lasso + BoW (R² ≈ 0.57)  
> XGBoost + BoW (R² ≈ 0.50)

#### Modelos preentrenados (transformers)

Se probaron modelos de HuggingFace como:

- `MoritzLaurer/mDeBERTa-v3-base-mnli-xnli`
- `Recognai/bert-base-spanish-wwm-cased-xnli`
- `papluca/xlm-roberta-base-language-detection`
- `Unbabel/xlm-roberta-comet-small`

Para adaptarlos al problema de regresión, se modificó la última capa para producir un único valor.

> **Limitaciones:**  
> Entrenar estos modelos sobre textos largos generó problemas de memoria.  
> Se probaron con 100 y 1000 muestras → resultados pobres (R² negativos).

---

## 4. Resultados y conclusiones

- Los modelos simples con vectorización BoW o TF-IDF superaron a los métodos más complejos (word embeddings y transformers) en este dataset.

- La calidad de los resultados estuvo limitada por el tamaño reducido del conjunto de datos (1.250 muestras).

- Los modelos preentrenados no mostraron ventajas claras debido a restricciones de memoria y tiempo.

- La mayoría de las ofertas ya estaban cerradas, lo que sugiere un dataset con fuerte componente histórico.

---

## 5. Recomendaciones futuras

* Aumento del corpus: recopilación y ampliación de datos estructurados a partir de fuentes como Kaggle.
* Extracción de características: identificación de entidades y palabras clave específicas (tecnologías, skills, experiencia...).
* Modelado: uso de modelos de representación contextual del texto para mejorar la comprensión semántica.
* Estudio de la evolución y tendencias a lo largo del tiempo, dada la disponibilidad de la variable temporal.
