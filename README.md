# PREDICCIÓN DE VENTAS POR IDENTIFICACIÓN DISPERSA DE UN SISTEMA ERP A PARTIR DE DATOS DE UN MÓDULO POS

**Autor:** CESAR DANIEL RINCÓN BRITO

## OBJETIVO

Adaptar un método de identificación de la dinámica de sistemas no lineales dispersos para predecir el comportamiento de las ventas del módulo POS de un sistema ERP.

## INTRODUCCIÓN

La metodología SINDy (Sparse Identification of Nonlinear Dynamics) se ha convertido en una herramienta poderosa para descubrir ecuaciones dinámicas a partir de datos, incluso en contextos donde los sistemas presentan comportamientos complejos y no lineales. En el caso de modelos discretos estocásticos, SINDy permite identificar relaciones entre variables de ventas u otros procesos que evolucionan en el tiempo bajo incertidumbre, seleccionando solo los términos más relevantes del modelo. Esta capacidad de reducción de complejidad es clave para evitar el sobreajuste y capturar la esencia de la dinámica subyacente. Además, su flexibilidad lo hace aplicable en escenarios donde las entradas son ruidosas o están afectadas por factores externos aleatorios. En síntesis, SINDy aporta interpretabilidad y eficiencia en la modelación de sistemas dinámicos discretos estocásticos, facilitando la generación de predicciones y el análisis de la dinámica real de los datos.

En SINDy el sistema dinámico se considera un mapa. En lugar de predecir derivadas, las funciones del lado derecho avanzan el sistema un paso de tiempo.

![Predicción de SINDy](Docs/Images/eq001.png)

La notación xk+1 indica que el sistema evoluciona en pasos de tiempo discretos. Es decir, se observan estados en k=0,1,2,… en lugar de un tiempo continuo.
En el caso de ventas, esto corresponde a registros diarios, semanales o mensuales, donde cada observación depende de la anterior.

En el contexto de ventas discretas representa un mapa dinámico estocástico no lineal, capaz de capturar patrones complejos y de generar pronósticos considerando tanto dependencias pasadas como fluctuaciones aleatorias. Es útil cuando los datos son observaciones discretas (ventas diarias, series temporales, sensores con sampling, etc.) en lugar de mediciones continuas.

[Introducción SINDy](readme.sindy.md)

## Descripción del Proyecto

Este proyecto utiliza el método SINDy (Sparse Identification of Nonlinear Dynamics) para modelar y predecir las ventas. El proceso incluye:

1.  **Carga y pre-procesamiento de datos**: Se leen los datos de ventas desde un archivo CSV y se escalan las características relevantes.
2.  **División de datos**: Los datos se dividen en conjuntos de entrenamiento, validación y prueba.
3.  **Modelo SINDy**: Se define y entrena un modelo SINDy de tiempo discreto.
4.  **Simulación y Evaluación**: El modelo entrenado se utiliza para simular las ventas y los resultados se comparan con los datos reales para evaluar el rendimiento del modelo utilizando métricas como el R².

## Variables de control

| Variable        | Data set       | Columna              | Registros |
|-----------------|----------------|----------------------|-----------|
| Ventas (Pred)   | y_trainDatos   | Total venta neta     | 1093      |
| Pico categórica | r_trainDatos   | Pico B_M_A           | 1093      |
| Unidades        | x_trainDatos   | Unidades Kit         | 1093      |
| Vta CO          | n_trainDatos   | Ventas centro oriente | 1093      |
| Vta OC          | k_trainDatos   | Ventas occidente     | 1093      |
| Vta NT          | l_trainDatos   | Ventas norte         | 1093      |
| Pico alto       | u_trainDatos   | Pico 1/0             | 1093      |

Tabla 1. Variables de control.


## Variables de control

1. \(u_{1r,k}\): Pico categórica (Alto - Medio - Bajo) Promociones, Fechas especiales.  
2. \(u_{2x,k}\): Unidades Kit
3. \(u_{3n,k}\): Ventas centro oriente 
4. \(u_{4k,k}\): Ventas occidente  
5. \(u_{5l,k}\): Ventas norte 
6. \(u_{6u,k}\): Pico 1/0 

![Predicción de SINDy](Docs/Images/eq002.png)

El modelo indica que las ventas futuras dependen no solo del nivel actual de ventas, sino también de múltiples factores externos que cambian con el tiempo.


## SINDy

![Predicción de SINDy](Docs/Images/SINDy_params.png)

*** STLSQ *** (Sequentially Thresholded Least Squares).

Calcula una regresión lineal para aproximar la dinámica con un umbral (threshold=0.05), los coeficientes más pequeños (menos influyentes) se eliminan, quedando solo los términos más importantes.

![Predicción de SINDy](Docs/Images/eq003.png)

de la eq. 3 SINDy busca un modelo sparsity 

*** feature library *** ps.PolynomialLibrary(degree=2)
Construye la matriz Θ(X,U), que contiene todas las funciones candidato (términos polinómicos hasta grado 2).

![Predicción de SINDy](Docs/Images/eq004.png)

donde SINDy busca la combinación de estas funciones que mejor represente la dinámica

*** Discrete time *** (discrete_time=True)

Indica que el sistema no está en forma continua (derivadas), sino en pasos discretos. En lugar de aproximar x˙(t) (derivadas), ajusta directamente la evolución paso a paso.

## Modelo

![Predicción de SINDy](Docs/Images/model.png)


## Coeficientes

![Predicción de SINDy](Docs/Images/FilterCoefSindy.png)

*** Términos lineales más relevantes ***

nVtaCO = +0.30 → Las ventas en la región Centro-Occidente tienen un efecto positivo directo sobre la variable dependiente «Ventas».

Los demás términos lineales (rPicoBMA, xUnd, kVtaOCC, lVtaNT, uPicoBin) aparecen con coeficientes muy bajos o cercanos a cero → poca influencia directa.

*** Interacciones fuertes con Ventas ***

Ventas * xUnd = +0.72 → Las ventas pasadas multiplicadas por «unidades vendidas» son un predictor fuerte positivo: cuando ambas crecen juntas, las ventas futuras crecen.

Ventas * nVtaCO = -0.82 → Relación fuerte pero negativa: un aumento simultáneo de ventas y ventas en Centro-Occidente.

Ventas * uPicoBin = -0.14 → Relación débilmente negativa.

*** Interacciones entre variables de control ***

rPicoBMA * xUnd = +0.23 → Si el pico de BMA aumenta y también suben las unidades vendidas, impulsa positivamente.

rPicoBMA * nVtaCO = -0.52 → Interacción negativa fuerte.

xUnd * nVtaCO = -0.53 → También fuerte y negativa.

nVtaCO * lVtaNT = +0.30 y kVtaOCC * ^2 = +0.27 → Estas aparecen como efectos positivos no lineales.

*** Términos cuadráticos ***

nVtaNT^2 = -0.15 → Incrementos grandes en ventas en Norte tienen un efecto decreciente.

kVtaOCC^2 = +0.27 → Crecimiento acelerado: ventas en Occidente se comportan de manera no lineal expansiva.

## Gráfico de Predicción

![Predicción de SINDy](Docs/Images/sindy_prediction.png)

*Nota: Para generar el gráfico, ejecute el notebook `SINDY-discrete-stocastic-sales/Sindy-discrete-stocastic-sales-erp.ipynb`.*

## Librerías Utilizadas

-   pandas
-   numpy
-   scikit-learn
-   matplotlib
-   pysindy

## Archivos del Repositorio

-   `SINDY-discrete-stocastic-sales/Sindy-discrete-stocastic-sales-erp.ipynb`: Notebook de Jupyter con la implementación del modelo.
-   `ERP-POS-Data/Sales-CSV970-1093.csv`: Datos de ventas utilizados para el entrenamiento y la validación.
-   `Matrix-Correlation/correlation_matrix.ipynb`: Notebook para analizar la correlación de variables.
-   `Test-Model/test_model.ipynb`: Notebook para pruebas adicionales del modelo.
