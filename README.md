# Predicción de la demanda de electricidad — Proyecto Final

## 1\. Problema

En el Sistema Interconectado Nacional (SIN) del Paraguay, la demanda de energía eléctrica presenta variaciones temporales y picos de consumo asociados, entre otros factores, a condiciones climáticas y al comportamiento de la demanda en días festivos. El proyecto aborda el problema de forecasting de la demanda máxima diaria de electricidad a partir de información histórica. Ante esta situación, surge la necesidad de evaluar diferentes modelos de predicción, basados en técnicas de series temporales, Machine Learning y Deep Learning, que permitan anticipar el comportamiento de la demanda eléctrica a partir de datos históricos y contribuir a una mejor planificación del sector eléctrico.



## 2\. Objetivo

Evaluar diferentes enfoques de predicción de la demanda eléctrica utilizando modelos de series temporales, Machine Learning y Deep Learning, y comparar su desempeño mediante métricas de error.

## 3\. Datos

Dataset utilizado: `complete-dataset.csv`.

Variables principales utilizadas en el pipeline:

* `DATETIME`: fecha y hora del registro.
* `SIN`: demanda/consumo eléctrico en MW.
* `T02M`: temperatura a 2 metros.
* `ISHOLIDAY`: indicador de día festivo.
* Para algunos modelos se construyen variables derivadas de calendario y rezagos (`lag\_1`, `lag\_2`, `lag\_3`, `lag\_7`, `lag\_14`, `dayofweek`, `month`).

### Preprocesamiento

Los registros se ordenan cronológicamente y se agregan a frecuencia diaria:

* `SIN` → máximo diario, denominado `SIN\_max`.
* `T02M` → promedio diario.
* `ISHOLIDAY` → indicador diario.

La evaluación utiliza una separación temporal de aproximadamente **80% para entrenamiento y 20% para prueba**, evitando mezclar aleatoriamente pasado y futuro en los experimentos de series temporales.

## 4\. Modelos

Se evaluaron nueve enfoques:

1. Naïve estacional
2. SARIMAX
3. Prophet
4. Random Forest
5. MLP (Multi-Layer Perceptron)
6. XGBoost
7. SVR (Support Vector Regression)
8. LSTM
9. GRU

### MLP

Configuración utilizada en el notebook:

* `hidden\_layer\_sizes=(64, 32)`
* `max\_iter=1000`
* `random\_state=42`
* `StandardScaler` para las variables de entrada.

### LSTM

Configuración utilizada:

* `input\_chunk\_length=30`
* `output\_chunk\_length=7`
* `hidden\_dim=64`
* `n\_rnn\_layers=2`
* `dropout=0.2`
* `batch\_size=16`
* `n\_epochs=50`
* `learning\_rate=0.001`
* `training\_length=37`

## 5\. Métricas

Se utilizaron:

* **MAE** — Mean Absolute Error: error absoluto medio, expresado en MW.
* **RMSE** — Root Mean Squared Error: raíz del error cuadrático medio; penaliza más los errores grandes.
* **MAPE** — Mean Absolute Percentage Error: error porcentual absoluto medio.

La métrica principal para la comparación es **MAPE**, debido a que facilita la interpretación del error en términos porcentuales.

## 6\. Resultados obtenidos

Los resultados corresponden a la ejecución registrada en `notebook\_final.ipynb`:

|Modelo|MAE (MW)|RMSE (MW)|MAPE|
|-|-:|-:|-:|
|MLP|**153.17**|**192.53**|**5.66%**|
|XGBoost|221.01|275.52|7.82%|
|SVR|181.54|251.36|6.24%|
|LSTM|537.43|623.92|19.11%|
|GRU|219.99|281.03|7.99%|

En esta ejecución, el MLP registró el menor MAPE entre los modelos evaluados, con **5.66%**. Este resultado describe específicamente la configuración, datos y partición utilizados en este proyecto.

## 7\. Aplicación

El proyecto incluye una aplicación desarrollada con **Streamlit** para llevar los modelos a una interfaz interactiva. La aplicación permite seleccionar un modelo, elegir una fecha de referencia, generar un pronóstico para los siguientes siete días y visualizar los resultados mediante gráficos y tablas.



