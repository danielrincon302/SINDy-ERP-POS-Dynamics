# SINDy: Identificación de Sistemas Dinámicos No Lineales a partir de Datos

Este documento describe el flujo de trabajo del método SINDy (Sparse Identification of Nonlinear Dynamics)

![Flujo de trabajo de SINDy](Docs/Images/rspa20180335f03.jpg)

## Introducción a SINDy

SINDy es un método para descubrir el modelo matemático subyacente de un sistema dinámico a partir de datos de series temporales. El objetivo es encontrar un modelo que sea a la vez preciso y parsimonioso (escaso), lo que significa que utiliza el menor número posible de términos para describir la dinámica del sistema.

## Flujo de Trabajo de SINDy

El proceso, como se ilustra en la imagen, se puede dividir en los siguientes pasos:

1.  **Dinámicas Desconocidas y Recopilación de Datos**:
    *   Se parte de un sistema con dinámicas desconocidas (por ejemplo, el atractor de Lorenz que se muestra en la imagen).
    *   Se recopilan datos de series temporales de las variables del sistema (x, y, z) y, opcionalmente, de una señal de control (u).

2.  **Construcción de una Biblioteca de Funciones Candidatas**:
    *   Se calculan las derivadas numéricas de los datos medidos (ẋ, ẏ, ż).
    *   Se construye una biblioteca de funciones candidatas, Θ(X, U), que pueden incluir términos polinómicos (x, y, z, x², xy, etc.), y otros términos no lineales que podrían formar parte de la dinámica del sistema.

3.  **Resolución de un Problema de Regresión Esparsa**:
    *   El núcleo de SINDy es formular un problema de regresión lineal para encontrar los coeficientes (Ξ) que mejor relacionan las derivadas con la biblioteca de funciones candidatas.
    *   Se utiliza una técnica de regresión esparsa (como la regresión Lasso) para encontrar una solución donde la mayoría de los coeficientes son cero. Esto se logra minimizando una función de costo que penaliza tanto el error del modelo como el número de términos no nulos (norma L1).

4.  **Sistema Identificado**:
    *   Los coeficientes no nulos en la matriz Ξ revelan los términos activos en la dinámica del sistema.
    *   El resultado es un modelo de ecuaciones diferenciales ordinarias, sencillo e interpretable, que describe la evolución del sistema.

## Extensiones Metodológicas

El método SINDy puede extenderse y mejorarse mediante varias técnicas:

*   **Coordenadas/Características**: Utilización de diferentes sistemas de coordenadas, como coordenadas de retardo de tiempo o observables intrínsecos de Koopman.
*   **Restricciones Físicas**: Incorporación de leyes de conservación conocidas (por ejemplo, conservación de la energía) en el proceso de ajuste.
*   **Selección de Modelo**: Uso de criterios de información (como AIC o BIC) para equilibrar la complejidad y el error del modelo, ayudando a seleccionar el mejor modelo.
