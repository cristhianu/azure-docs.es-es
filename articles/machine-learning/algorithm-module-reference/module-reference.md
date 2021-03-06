---
title: Referencia de módulos y algoritmos
description: Conozca los módulos disponibles en el diseñador de Azure Machine Learning (versión preliminar).
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 2da567a8f5ebd0161e41bf5a0aeb83b0d3b4ba4c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466071"
---
# <a name="algorithm--module-reference-overview"></a>Información general sobre la referencia de módulos y algoritmos

Este contenido de referencia proporciona el contexto técnico de cada uno de los algoritmos y módulos de aprendizaje automático disponibles en el diseñador de Azure Machine Learning (versión preliminar).

Cada módulo representa un conjunto de código que puede ejecutarse de forma independiente y realizar una tarea de aprendizaje automático, dadas las entradas necesarias. Un módulo puede contener un algoritmo en particular o realizar una tarea que sea importante para el aprendizaje automático, como el reemplazo de un valor que falta o análisis estadísticos.

> [!TIP]
> En cualquier canalización del diseñador, puede obtener información sobre un módulo específico. Seleccione el módulo y luego seleccione el vínculo **más ayuda** en el panel **Ayuda rápida**.

## <a name="modules"></a>Módulos

Los módulos se organizan por funcionalidad:

| Funcionalidad | DESCRIPCIÓN | Módulo |
| --- |--- | ---- |
| Entrada y salida de datos | Mueva los datos de los orígenes en la nube a la canalización. Escriba los resultados o los datos intermedios en Azure Storage, una base de datos SQL o Hive, mientras ejecuta una canalización, o use el almacenamiento en la nube para intercambiar datos entre canalizaciones.  | [Import Data](import-data.md) <br/> [Introducción manual de datos](enter-data-manually.md) <br/>[Export Data](export-data.md) |
| Transformación de datos | Operaciones con los datos que son exclusivas del aprendizaje automático, como la normalización o discretización de datos, la reducción de la dimensionalidad y la conversión de datos entre barios formatos de archivo.| [Adición de columnas](add-columns.md) <br/> [Adición de filas](add-rows.md) <br/> [Clean Missing Data](clean-missing-data.md) (limpiar datos faltantes) <br/> [Conversión a CSV](convert-to-csv.md) <br/> [Conversión en conjunto de datos](convert-to-dataset.md) <br/> [Edición de metadatos](edit-metadata.md) <br/> [Combinación de datos](join-data.md) <br/> [Normalize Data](normalize-data.md) (normalizar datos) <br/> [Partición y ejemplo](partition-and-sample.md) <br/> [Supresión de filas duplicadas](remove-duplicate-rows.md) <br/> [Selección de transformación de columnas](select-columns-transform.md) <br/> [Seleccionar columnas de conjunto de datos](select-columns-in-dataset.md) <br/> [SMOTE](smote.md) <br/> [División de datos](split-data.md) |
| Selección de características | Seleccione un subconjunto de características pertinentes y útiles para la creación de un modelo analítico. | [Selección de características basada en filtros](filter-based-feature-selection.md) <br/> [Importancia de la característica de permutación](permutation-feature-importance.md) |
| Módulos de R y Python | Escriba código e insértelo en un módulo para integrar Python y R con la canalización. | [Creación de modelo Python](create-python-model.md) <br/> [Ejecución de script de Python](execute-python-script.md)   <br/>  [Ejecución script de R](execute-r-script.md)
| Text Analytics | Proporcione herramientas de cálculo especializadas para trabajar con texto estructurado y no estructurado. | [Extracción de características de n-gramas a partir de texto](extract-n-gram-features-from-text.md) <br/> [Hash de características](feature-hashing.md) <br/> [Preprocesamiento de texto](preprocess-text.md) |
|  | **Algoritmos de aprendizaje automático**: | |
| clasificación | Prediga una clase.  Elija en algoritmos binarios (dos clases) o multiclase.| [Bosque de decisión multiclase](multiclass-decision-forest.md) <br/> [Árbol de decisión ampliado multiclase](multiclass-boosted-decision-tree.md) <br/> [Regresión logística multiclase](multiclass-logistic-regression.md)  <br/> [Red neuronal multiclase](multiclass-neural-network.md) <br/> [One vs. All Multiclass](one-vs-all-multiclass.md) (Multiclase uno frente a todos) <br/>  [Regresión logística de dos clases](two-class-logistic-regression.md)  <br/>[Perceptrón promedio de dos clases](two-class-averaged-perceptron.md) <br/> [Two-Class Boosted Decision Tree](two-class-boosted-decision-tree.md) (Árbol de decisión ampliado de dos clases)  <br/> [Bosque de decisión de dos clases](two-class-decision-forest.md)  <br/> [Red neuronal de dos clases](two-class-neural-network.md) <br/> [Two Class Support Vector Machine](two-class-support-vector-machine.md) (Máquina de vectores compatible con dos clases) | 
| Agrupación en clústeres | Agrupe datos.| [Agrupación en clústeres K-Means](k-means-clustering.md)
| Regresión | Prediga un valor. | [Boosted Decision Tree Regression](boosted-decision-tree-regression.md) (Regresión del árbol de decisión ampliado) <br/> [Regresión de bosque de decisión](decision-forest-regression.md) <br/> [Regresión lineal](linear-regression.md)  <br/> [Regresión de red neuronal](neural-network-regression.md)  <br/> |
| Recomendador | Compile modelos de recomendación. | [Evaluate Recommender](evaluate-recommender.md) (Evaluar recomendador) <br/> [Score SVD Recommender](score-svd-recommender.md) (Puntuar recomendador de SVD) <br/> [Train SVD Recommender](train-SVD-recommender.md) (Entrenar recomendador de SVD) |
|  | **Compilar y evaluar modelos**: | |
| Train   | Ejecute datos a través del algoritmo. | [Cross Validate Model](cross-validate-model.md) (Modelo de validación cruzada) <br/> [Train Model](train-model.md) (entrenar modelo)  <br/> [Entrenamiento del modelo de agrupación en clústeres](train-clustering-model.md) <br/>  [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Optimizar hiperparámetros del modelo) |
| Evaluate Model (Evaluar modelo) | Mida la precisión del modelo entrenado. |  [Evaluación de módulo](evaluate-model.md) |
| Score | Obtenga predicciones del modelo que acaba de entrenar. | [Aplicación de la transformación](apply-transformation.md)<br/>[Assign Data to Clusters](assign-data-to-clusters.md) (asignar datos a los clústeres) <br/>[Score Model](score-model.md) (puntuar modelo) |
| Funciones estadísticas | Proporcionan diversos métodos numéricos relacionados con la ciencia de datos. | [Aplicación de operación matemática](apply-math-operation.md) <br/> [Resumen de datos](summarize-data.md)|

## <a name="error-messages"></a>mensajes de error

Conozca los [mensajes de error y códigos de excepción](machine-learning-module-error-codes.md) que pueden surgir al usar los módulos del diseñador de Azure Machine Learning.
