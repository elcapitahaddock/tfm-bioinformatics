# 🧬 Comparación de métodos de selección de biomarcadores y clasificación en datos de expresión génica mediante Python

## 📌 Descripción

Este proyecto forma parte del Trabajo de Fin de Máster (TFM) en Bioinformática y Bioestadística.

El objetivo es comparar de forma sistemática distintas combinaciones de métodos de selección de características (biomarcadores) y algoritmos de clasificación aplicados a datos de expresión génica, evaluando tanto su rendimiento predictivo como la estabilidad de los genes seleccionados.

---

## 🧪 Dataset

Se utiliza el dataset público:

- GSE2034 (GEO)  
- Tipo: Microarray (Affymetrix)  
- Muestras: 286 pacientes  
- Variables: ~22.000 genes  
- Problema: Clasificación binaria (metástasis / no metástasis)  

Fuente:  
https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE2034  

---

## ⚙️ Pipeline

El flujo de trabajo seguido es:

Dataset GEO  
↓  
Exploración inicial  
↓  
Preprocesamiento (log-transformación, filtrado)  
↓  
Validación cruzada  
↓  
Selección de características (solo en train)  
↓  
Entrenamiento del modelo  
↓  
Evaluación en el fold de test  
↓  
Agregación de resultados  
↓  
Comparación de métodos  
↓  
Análisis de estabilidad de biomarcadores  

---

## 🤖 Modelos utilizados

- Random Forest  
- Support Vector Machine (RBF)  
- Logistic Regression  

---

## 🧬 Métodos de selección de características

- ANOVA (SelectKBest)  
- Mutual Information (pendiente)  
- RFE (pendiente)  
- L1 / Lasso (pendiente)  

---

## 📊 Evaluación

Se utiliza validación cruzada estratificada (5-fold) para evitar sesgos.

Métricas evaluadas:

- ROC-AUC (principal)  
- F1-score  
- Accuracy  

---

## 🔁 Reproducibilidad

Pasos para ejecutar el proyecto:

1. Crear el entorno con Conda usando el archivo environment.yml  
2. Activar el entorno tfm_bio  
3. Ejecutar Jupyter Notebook  
4. Abrir el archivo notebooks/dataset.ipynb  

---

## 📁 Estructura del proyecto

tfm/  
├── data/ (Datos descargados, no versionados)  
├── notebooks/ (Notebooks de análisis)  
├── src/ (Código fuente)  
├── results/ (Resultados generados)  
├── environment.yml (Entorno reproducible)  
└── README.md  
