# Traductor Automático Neuronal Aymara-Español: Un Enfoque para Pares de Bajos Recursos

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-2.5%2B-ee4c2c)
![License](https://img.shields.io/badge/License-MIT-green)

Este repositorio contiene la implementación oficial del código para el proyecto del año 2026 del curso de NLP de la Maestría en Inteligencia Artificial (Universidad Nacional de Ingeniería, Lima - Perú): **"Traductor Automático Neuronal para el Par Aymara-Español: Un Enfoque para Pares de Bajos Recursos"**.

El proyecto implementa dos configuraciones de la arquitectura Transformer (Estándard y 'Deep Encoder / Shallow Decoder') optimizada para realizar traducción automática en un escenario de bajos recursos (*Low-Resource NMT*), utilizando un corpus paralelo construido a partir de textos bíblicos y literatura en PDF.

## 📂 Estructura del Repositorio

El código está organizado según el flujo de trabajo del proyecto:

### 1. Adquisición de Datos
- **`scraping_bible.ipynb`**: Script de *web scraping* utilizando `BeautifulSoup` y `requests` para extraer versículos paralelos (Aymara/Español) de la web Bible.com.

### 2. Preprocesamiento y Tokenización
- **`preprocessing_bible.ipynb`**: Normalización de caracteres unicode, limpieza de puntuación y alineamiento de versículos extraídos.
- **`preprocessing_book.ipynb`**: Script de extracción de texto desde un archivo PDF (`book.pdf`) utilizando `pdfplumber`. Incluye lógica de limpieza, reconstrucción de oraciones y alineamiento de diálogos.
- **`tokenization.ipynb`**: Entrenamiento de un tokenizador BPE (Byte-Pair Encoding) utilizando `SentencePiece`. Generación de vocabulario y archivos tokenizados (`.ids`).

### 3. Modelado y Entrenamiento
- **`training_AymaraToSpanish_Standard.ipynb`, `training_SpanishToAymara_Standard.ipynb`, `training_AymaraToSpanish_DeepEncoder-ShallowDecoder.ipynb`, `training_SpanishToAymara_DeepEncoder-ShallowDecoder.ipynb`**: Implementación en PyTorch del modelo Transformer.
    - Incluye clases para `Dataset`, `DataLoader` y el bucle de entrenamiento/validación.
    - Implementa *Beam Search Decoding* para la inferencia.
    - Calcula métricas BLEU utilizando `sacrebleu`.

## 🚀 Instalación y Requisitos

Para ejecutar los notebooks, asegúrate de instalar las siguientes dependencias:

```bash
pip3 install torch --index-url https://download.pytorch.org/whl/cu121
pip install sentencepiece sacrebleu pandas numpy matplotlib
pip install beautifulsoup4 requests pdfplumber
```

## 🧠 Arquitectura del Modelo

El proyecto implementa y compara dos variantes de la arquitectura Transformer para optimizar el rendimiento en un escenario de bajos recursos:

| Configuración | Encoder Layers | Decoder Layers | D_Model | Dropout | Notas |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Baseline** | 6 | 6 | 512 | 0.1 | Arquitectura estándar (Vaswani et al., 2017). |
| **Deep-Shallow** | 12 | 2 | 512 | 0.3 | **Propuesta**. Utiliza *Pre-Normalization* (`norm_first=True`) y un Encoder profundo para capturar la compleja morfología aglutinante del Aymara, junto con un Decoder superficial para evitar alucinaciones. |

## 📊 Resultados Preliminares

La evaluación del modelo se realizó utilizando la métrica **BLEU** sobre un conjunto de prueba (*Test Set*) reservado (aprox. 8,000 oraciones), aplicando *Beam Search Decoding* con un ancho de 5.

| Dirección de Traducción | BLEU Score | Observaciones |
| :--- | :---: | :--- |
| **Aymara $\rightarrow$ Español** | **28.95** | Resultado competitivo para un escenario de bajos recursos. El modelo logra generalizar bien hacia la gramática del español. |
| **Español $\rightarrow$ Aymara** | **13.37** | El menor puntaje refleja la complejidad de generar una lengua **aglutinante**. El modelo enfrenta el desafío de predecir la combinación exacta de sufijos aymaras a partir del español. |

> ## ⚠️ Disponibilidad de los Datos y Copyright

**Importante:** Este repositorio de código **NO contiene los datasets crudos** (textos bíblicos originales ni el archivo PDF del libro) debido a restricciones de propiedad intelectual (*Copyright*).

### 📥 Obtener el Dataset Limpio
El dataset procesado (alineado, limpio y dividido en *train/valid/test*) está disponible únicamente para **fines de investigación académica** a través de los siguientes repositorios:

- **Hugging Face:** [https://huggingface.co/datasets/wjosielct/aymara-spanish-parallel-corpus]
- **Zenodo:** [https://doi.org/10.5281/zenodo.18193320]

### ⚖️ Descargo de Responsabilidad (Disclaimer)
Los scripts de adquisición de datos (`scraping_bible.ipynb` y `preprocessing_book.ipynb`) se proporcionan con fines de reproducibilidad técnica.
> *The texts processed in this project are derived from copyrighted sources. This repository claims no ownership over the original content. The processed data is distributed solely for **non-commercial academic research** aimed at preserving the Aymara language.*

## 📝 Citación

Si utilizas este código, los modelos entrenados o la metodología descrita en tu investigación, por favor cita el trabajo de la siguiente manera:

```bibtex
@mastersthesis{AymaraNMT2026,
  author  = {Corbera Terrones, Josiel},
  title   = {Traductor Automático Neuronal para el Par Aymara-Español: Un Enfoque para Pares de Bajos Recursos},
  school  = {Universidad Nacional de Ingeniería (UNI)},
  year    = {2026},
  address = {Lima, Perú},
  note    = {Disponible en GitHub}
}
```
## 📄 Licencia

El código fuente de este proyecto se distribuye bajo la licencia **MIT**. Esto permite su uso, modificación y distribución libre para fines académicos y comerciales, siempre que se mantenga el reconocimiento al autor original.

Consulta el archivo [LICENSE](LICENSE) para ver los términos completos.

> **Nota:** Esta licencia aplica únicamente al **software** (scripts, notebooks). Los datos procesados y los modelos derivados están sujetos a las restricciones de copyright y licencias no comerciales (*CC-BY-NC-SA*) mencionadas en la sección de "Disponibilidad de los Datos".
