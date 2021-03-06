---
title: Aptitudes integradas para la extracción de datos, el lenguaje natural y el procesamiento de imágenes durante la indexación
titleSuffix: Azure Cognitive Search
description: Las aptitudes cognitivas de extracción de datos, lenguaje natural y procesamiento de imágenes agregan semántica y estructura al contenido sin procesar de una canalización de Búsqueda cognitiva de Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2f03752c5fda366fc16b681d71ba68486527a30f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466800"
---
# <a name="built-in-cognitive-skills-for-content-enrichment-azure-cognitive-search"></a>Aptitudes cognitivas integradas para el enriquecimiento de contenido (Búsqueda cognitiva de Azure)

En este artículo se obtiene información sobre las aptitudes cognitivas que proporciona Búsqueda cognitiva de Azure. Una *habilidad cognitiva* es una operación que transforma el contenido de alguna manera. Normalmente es un componente que extrae datos o deduce la estructura y, por tanto, aumenta la comprensión de los datos de entrada. Casi siempre la salida se basa en texto. Un *conjunto de habilidades* es una colección de habilidades que definen la canalización de enriquecimiento. 

> [!NOTE]
> A medida que expanda el ámbito aumentando la frecuencia de procesamiento, agregando más documentos o agregando más algoritmos de IA, tendrá que [asociar un recurso facturable de Cognitive Services](cognitive-search-attach-cognitive-services.md). Los cargos se acumulan cuando se llama a las API de Cognitive Services y por la extracción de imágenes como parte de la fase de descifrado de documentos de Azure Cognitive Search. No hay ningún cargo por la extracción de texto de documentos.
>
> La ejecución de aptitudes integradas se cobra según los [precios de pago por uso de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. Los precios de la extracción de imágenes se describen en la [página de precios de Búsqueda cognitiva de Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="built-in-skills"></a>Aptitudes integradas

Hay varias habilidades que son flexibles con respecto a lo que usan o producen. En general, la mayoría de las habilidades se basan en modelos aprendidos previamente, lo que significa que no es posible aprender el modelo con sus propios datos de aprendizaje. En la tabla siguiente se enumeran y se describen las habilidades proporcionadas por Microsoft. 

| Habilidad | DESCRIPCIÓN |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Esta habilidad usa un modelo aprendido previamente para detectar frases importantes en función de la colocación de términos, las reglas lingüísticas, la proximidad a otros términos y cómo de inusual es el término en los datos de origen. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Esta habilidad usa un modelo aprendido previamente para detectar qué idioma se usa (un identificador de idioma por documento). Si se usan varios idiomas en los mismos segmentos de texto, la salida es el LCID del idioma más usado.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolida el texto de una colección de campos en un solo campo.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Esta habilidad usa un modelo aprendido previamente para establecer las entidades de un conjunto fijo de categorías: personas, ubicación, organización, correos electrónicos, direcciones URL, campos de fecha y hora. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Esta habilidad usa un modelo aprendido previamente para puntuar opiniones positivas o negativas registro a registro. La puntuación va de 0 a 1. Se otorgan puntuaciones neutras para el caso nulo cuando no se puede detectar la opinión y para el texto que se considera neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Divide el texto en páginas para que se pueda enriquecer o aumentar el contenido de forma incremental. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Esta aptitud usa un modelo entrenado previamente para traducir el texto de entrada a una variedad de idiomas para los casos de uso de normalización o localización. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Esta habilidad usa un algoritmo de detección de imágenes para identificar el contenido de una imagen y generar una descripción de texto. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconocimiento óptico de caracteres. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Permite el filtrado, la asignación de un valor predeterminado y la combinación de datos según una condición.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extrae contenido de un archivo dentro de la canalización de enriquecimiento. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Asigna la salida a un tipo complejo (un tipo de datos de varias partes que se podría usar para un nombre completo, una dirección de varias líneas o una combinación de apellido e identificador personal). |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permite la extensibilidad de la canalización de enriquecimiento con inteligencia artificial mediante la realización de una llamada HTTP a una API web personalizada |


Para ver una guía acerca de cómo crear una [aptitud personalizada](cognitive-search-custom-skill-web-api.md), consulte [Cómo definir una interfaz personalizada](cognitive-search-custom-skill-interface.md) y [Ejemplo: Creación de una aptitud personalizada para el enriquecimiento con inteligencia artificial](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Otras referencias

+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Custom Skills interface definition](cognitive-search-custom-skill-interface.md) (Definición de interfaz de habilidades personalizadas)
+ [Tutorial: Enriched indexing with AI](cognitive-search-tutorial-blob.md) (Tutorial: Indexación enriquecida con inteligencia artificial)
