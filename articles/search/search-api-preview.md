---
title: API REST versión 2019-05-06-Preview
titleSuffix: Azure Cognitive Search
description: La API REST del servicio Azure Cognitive Search, versión 2019-05-06-Preview, incluye características experimentales como almacén de conocimiento y claves de cifrado administradas por el cliente.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720030"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>API REST del servicio Azure Cognitive Search, versión 2019-05-06-Preview

En este artículo se describe la versión `api-version=2019-05-06-Preview` de la API REST del servicio Search y se proporcionan características experimentales que normalmente no están disponibles.

> [!NOTE]
> Las características en versión preliminar están disponibles para prueba y experimentación con el fin de recabar comentarios y están sujetas a cambios. Le recomendamos encarecidamente que no use una versión preliminar de API en aplicaciones de producción.


## <a name="new-in-2019-05-06-preview"></a>Novedades de 2019-05-06, versión preliminar

+ La [indexación incremental](cognitive-search-incremental-indexing-conceptual.md) es un nuevo modo de indexación que agrega el estado y el almacenamiento en caché a un conjunto de aptitudes, lo que le permite reutilizar la salida existente cuando no se cambian los datos de origen, el indexador y las definiciones del conjunto de aptitudes. Esta característica se aplica únicamente a los enriquecimientos que han definido un conjunto de aptitudes cognitivas.

+ El [indexador de Cosmos DB](search-howto-index-cosmosdb.md) admite MongoDB API, Gremlin API y Cassandra API.

+ El [indexador de Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) puede indexar el contenido y los metadatos de Data Lake Storage Gen2.

+ La [extracción de documentos (versión preliminar)](cognitive-search-skill-document-extraction.md) es una aptitud cognitiva que se usa durante la indexación y que permite extraer el contenido de un archivo de un conjunto de aptitudes. Antes, el descifrado de documentos solo tenía lugar antes de la ejecución del conjunto de aptitudes. Con la incorporación de esta aptitud, también puede realizar esta operación dentro de la ejecución del conjunto de aptitudes.

+ La [traducción de texto (versión preliminar)](cognitive-search-skill-text-translation.md) es una aptitud cognitiva que se usa durante la indexación que evalúa el texto y devuelve en todos los registros el texto traducido al idioma de destino especificado.

+ El [almacén de conocimiento](knowledge-store-concept-intro.md)es un nuevo destino de una canalización de enriquecimiento basada en inteligencia artificial. La estructura de datos física existe en Azure Blob Storage y Azure Table Storage, y se crea y rellena al ejecutar un indexador que tiene un conjunto de aptitudes cognitivas conectado. La definición de un almacén de conocimiento se especifica en la definición de un conjunto de aptitudes. En la definición del almacén de conocimiento, controla las estructuras físicas de los datos a través de los elementos de *proyección* que determinan cómo se forman los datos, si estos se almacenan en Table Storage o Blob Storage, y si hay varias vistas.

+ Las [claves de cifrado administradas por el cliente](search-security-manage-encryption-keys.md) para el cifrado en reposo desde el servicio son también una nueva característica de versión preliminar. Además del cifrado en reposo integrado administrado por Microsoft, puede aplicar una capa adicional de cifrado, donde será el titular exclusivo de las claves de cifrado.

## <a name="earlier-preview-features"></a>Características de las versiones preliminares anteriores

Las características anunciadas en versiones preliminares anteriores aún están en versión preliminar pública. Si está llamando a una API con una versión anterior de la API en versión preliminar, puede continuar usando esa versión o cambiar a `2019-05-06-Preview` sin que se produzcan cambios en el comportamiento esperado.

+ El [parámetro de consulta moreLikeThis](search-more-like-this.md) busca documentos que sean pertinentes para un documento específico. Esta característica ha aparecido en versiones anteriores. 

+ La [indexación de blobs en CSV](search-howto-index-csv-blobs.md) crea un documento por línea, en lugar de un documento por blob de texto.

## <a name="how-to-call-a-preview-api"></a>Cómo llamar a una API en versión preliminar

Las vistas previas anteriores siguen funcionando, pero quedarán obsoletas con el paso del tiempo. Si el código llama a `api-version=2016-09-01-Preview` o `api-version=2017-11-11-Preview`, dichas llamadas siguen siendo válidas. Sin embargo, solo la versión preliminar más reciente se actualiza con mejoras. 

En la sintaxis de ejemplo siguiente se ilustra una llamada a la versión de API de versión preliminar.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

El servicio Azure Cognitive Search está disponible en varias versiones. Para obtener más información, consulte [Versiones de API](search-api-versions.md).

## <a name="next-steps"></a>Pasos siguientes

Revise la documentación de referencia de la API de REST del servicio Search. Si tiene algún problema, puede solicitar ayuda en [StackOverflow](https://stackoverflow.com/) o [ponerse en contacto con el servicio de soporte técnico](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Referencia de la API de REST del servicio Search](https://docs.microsoft.com/rest/api/searchservice/)