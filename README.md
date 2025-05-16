# Proyecto PLN - Estimación del salario en función de la oferta

**Grupo 3**  
**Componentes**: Sebastia Garcia, Sergio (Lider) / Dionis Ros, Alejandro / Lizzadro Pla, Adrian / Zhigarev, Ilia
**Fuente de datos**: Web Scraping ([GetManfred.com](https://www.getmanfred.com/))  
**Tema**: Ofertas de Trabajo  
**Objetivo principal**: Estimación del salario en función de la oferta  
**Objetivo adicional (opcional)**: Topic modeling

---

## 1. Introducción

Este proyecto se enmarca dentro del curso de Procesamiento de Lenguaje Natural (PLN) y tiene como propósito el análisis de ofertas laborales con el objetivo de **estimar el salario** a partir del contenido textual de dichas ofertas.

---

## 2. Metodología

### 2.1 Extracción de datos (Web Scraping)

- Se utilizó la librería `requests` para acceder al archivo sitemap de ofertas publicado por GetManfred:  
  `https://www.getmanfred.com/sitemap-offers.xml`.

- A partir de este sitemap, se extrajeron todas las URLs que contenían `/ofertas-empleo/`.

- Para cada URL, se realizó una petición HTTP individual y se procesó el HTML con `BeautifulSoup`.

- De cada oferta, se extrajeron los siguientes campos:
    - **Título del puesto** (`<h1>`)
    - **Salario** (extraído de un `<h3>` que contiene la palabra "Salario")
    - **Competencias técnicas** (div con `id="skills"`)
    - **Descripción del puesto** (div con `id="requisitos"`)
    - **Ubicación** (div con `id="donde"`)
    - Además, se verificó si la oferta estaba cerrada mediante una búsqueda textual.

### 2.2 Limpieza y preprocesamiento del texto

- Se aplicaron transformaciones básicas:
    - Conversión a minúsculas (`lower()`)
    - Eliminación de caracteres especiales (`re.sub`)
    - Sustitución de caracteres unicode y normalización (`unicodedata.normalize`)
    - Eliminación de signos de puntuación (`string.punctuation`)
    - Eliminación de stopwords usando `spaCy`

- Los campos limpios incluyen: descripción del puesto, habilidades y localización.

### 2.3 Modelado

---

## 3. Resultados y conclusiones

---

## 5. Recomendaciones futuras
- Incorporar datos de múltiples plataformas (LinkedIn, InfoJobs, etc.).
- Explorar variables adicionales como localización o tamaño de la empresa.