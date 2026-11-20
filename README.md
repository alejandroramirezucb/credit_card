# Credit Card

Segmentación de tarjetahabientes con clustering sobre el *Credit Card Dataset for Clustering*. Los
notebooks comparan K-Means y un modelo de mezcla gaussiana (GMM), primero sobre las 17 variables
originales y después sobre 6 componentes principales, y describen los grupos en unidades originales.

## Inicio rápido

Para correrlos sin instalar nada, abre cada notebook en Google Colab y ejecútalos en orden, del 01 al
06. Cada uno guarda en Google Drive los archivos que necesita el siguiente.

| Notebook | Qué hace | Necesita | Tiempo aproximado |
|---|---|---|---|
| [01_formulacion_y_datos](https://colab.research.google.com/github/alejandroramirezucb/credit_card/blob/main/notebooks/01_formulacion_y_datos.ipynb) | Descarga el conjunto, describe las variables y revisa nulos, duplicados y rangos | Nada | menos de 1 min |
| [02_estadistica_descriptiva](https://colab.research.google.com/github/alejandroramirezucb/credit_card/blob/main/notebooks/02_estadistica_descriptiva.ipynb) | Escalas, asimetría, masas puntuales, atípicos y correlaciones | Nada | menos de 1 min |
| [03_preprocesamiento_y_particion](https://colab.research.google.com/github/alejandroramirezucb/credit_card/blob/main/notebooks/03_preprocesamiento_y_particion.ipynb) | Partición 80/20, imputación y transformación por cuantiles ajustadas solo con entrenamiento | Nada | menos de 1 min |
| [04_clustering_espacio_original](https://colab.research.google.com/github/alejandroramirezucb/credit_card/blob/main/notebooks/04_clustering_espacio_original.ipynb) | Etapa 1, ajuste y evaluación de K-Means y GMM sobre las 17 variables | 03 | 5 a 15 min |
| [05_pca_y_espacio_reducido](https://colab.research.google.com/github/alejandroramirezucb/credit_card/blob/main/notebooks/05_pca_y_espacio_reducido.ipynb) | Etapa 2, PCA, reentrenamiento, tabla comparativa, sensibilidad y costo | 03 y 04 | 5 a 15 min |
| [06_interpretacion_y_conclusiones](https://colab.research.google.com/github/alejandroramirezucb/credit_card/blob/main/notebooks/06_interpretacion_y_conclusiones.ipynb) | Perfiles de los grupos, peso en compras, deuda y efectivo, y variables que los definen | 03, 04 y 05 | menos de 1 min |

Los notebooks 04 y 05 son lentos porque prueban 9 valores de K para K-Means y 24 combinaciones de
covarianza y regularización para GMM, y repiten cada configuración en diez submuestras para medir la
estabilidad.

Para solo leerlos, GitHub muestra los notebooks ya ejecutados en la carpeta
[notebooks](notebooks), con todas sus tablas y gráficos.

## Estructura del repositorio

```
credit_card/
├── notebooks/
│   ├── 01_formulacion_y_datos.ipynb
│   ├── 02_estadistica_descriptiva.ipynb
│   ├── 03_preprocesamiento_y_particion.ipynb
│   ├── 04_clustering_espacio_original.ipynb
│   ├── 05_pca_y_espacio_reducido.ipynb
│   └── 06_interpretacion_y_conclusiones.ipynb
├── splits/                  16 figuras PNG, con el número del notebook que las genera como prefijo
├── resultados.json          cifras de los seis notebooks, que usa el informe
├── requirements.txt
├── .gitignore
└── README.md
```

| Ruta | Contenido |
|---|---|
| `notebooks/` | Los seis notebooks ya ejecutados, con sus tablas y gráficos |
| `splits/02_*.png` | Histogramas, atípicos y matriz de correlación |
| `splits/04_*.png` | Selección de K, BIC por covarianza, selección de GMM y certeza de las asignaciones |
| `splits/05_*.png` | Varianza explicada, cargas, proyección PCA y t-SNE, y selección de K y de GMM sobre PCA |
| `splits/06_*.png` | Perfiles de los grupos y peso de cada variable en la separación |
| `resultados.json` | Cifras de cada notebook, redondeadas con la misma regla que el informe |

## Carpeta de trabajo y archivos intermedios

En Colab, la primera celda de código monta Google Drive y usa `MyDrive/credit_card` como carpeta de
trabajo. Colab pide autorizar el acceso la primera vez. Si el montaje falla, la celda se detiene con un
error en lugar de escribir en el disco temporal de Colab, que se borra al cerrar la sesión. Fuera de
Colab la carpeta de trabajo es la raíz del repositorio, se abra el notebook desde la raíz o desde
`notebooks/`.

Al ejecutarse, los notebooks escriben en esa carpeta las figuras, `resultados.json` y cuatro archivos
intermedios que no están en el repositorio porque se regeneran en cada ejecución:

| Archivo | Lo crea | Lo usan | Contenido |
|---|---|---|---|
| `CC_GENERAL.csv` | 01, 02 o 03, el primero que corra | 01, 02 y 03 | Copia local del conjunto, para no descargarlo de nuevo |
| `datos_preparados.joblib` | 03 | 04, 05 y 06 | Particiones en unidades originales y escaladas, el transformador y las listas de columnas |
| `etapa1.joblib` | 04 | 05 y 06 | Hiperparámetros, etiquetas y tiempos de K-Means y GMM en el espacio original |
| `etapa2.joblib` | 05 | 06 | Etiquetas de los modelos sobre PCA |

Por esa cadena de archivos el orden importa la primera vez. Después se puede volver a abrir cualquier
notebook por separado, siempre que los archivos que necesita sigan en la carpeta de trabajo.

## Instalación local

Clona el repositorio y crea un entorno virtual:

```bash
git clone https://github.com/alejandroramirezucb/credit_card.git
cd credit_card
python -m venv .venv
source .venv/bin/activate        # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Abre Jupyter y ejecuta los notebooks en orden:

```bash
jupyter lab notebooks/
```

También se pueden ejecutar los seis desde la terminal, sin abrir Jupyter. Conviene desactivar el límite
de tiempo por celda con `timeout=-1`, porque la búsqueda de GMM del notebook 04 puede pasar de un cuarto de hora
en una máquina lenta:

```bash
for nb in notebooks/0*.ipynb; do jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=-1 "$nb"; done
```

Probado con Python 3.12.10, NumPy 2.2.6, pandas 3.0.3, scikit-learn 1.8.0 y Matplotlib 3.10.9. Cada
ejecución guarda las versiones usadas en `resultados.json`.

## Reproducibilidad

- La semilla 42 fija la partición y todos los modelos, así que dos ejecuciones dan los mismos grupos y
  las mismas métricas. Solo cambian los tiempos, que dependen de la máquina.
- El imputador, la transformación por cuantiles y el PCA se ajustan solo con el conjunto de entrenamiento y después se
  aplican a prueba.
- Las cifras se redondean una sola vez, mitad hacia arriba, con la función `redondear` de la primera
  celda de código, para que coincidan con las del informe.

## Problemas frecuentes

`FileNotFoundError: datos_preparados.joblib` (o `etapa1.joblib`) aparece al abrir un notebook sin haber
ejecutado antes el que crea ese archivo. Revisa la columna "Necesita" de la tabla de inicio rápido.

`ModuleNotFoundError: kagglehub` aparece cuando el entorno no tiene `kagglehub`. Se resuelve con
`pip install -r requirements.txt` en local o con `pip install kagglehub` en Colab.

Si Colab muestra "Drive no quedó montado", vuelve a ejecutar la primera celda de código y acepta el
permiso en la ventana emergente.

## Datos

Bhasin, A. (2018). *Credit Card Dataset for Clustering* [Conjunto de datos]. Kaggle.
https://www.kaggle.com/datasets/arjunbhasin2013/ccdata

Son 8 950 tarjetahabientes y 17 variables numéricas que resumen seis meses de uso de la tarjeta (gasto,
frecuencia de compra, avances en efectivo, pagos y deuda). El conjunto tiene licencia CC0, de dominio
público, y los notebooks lo descargan con `kagglehub` sin pedir credenciales.

El informe técnico y la presentación se publican en el release del repositorio.
