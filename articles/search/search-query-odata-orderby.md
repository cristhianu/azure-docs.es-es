---
title: Referencia de order by de OData
titleSuffix: Azure Cognitive Search
description: Referencia del lenguaje OData para la sintaxis de order-by en las consultas de Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: e3ca19b5696b9a7ad9b68b180313753a5c9de912
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793303"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Sintaxis de $orderby de OData en Azure Cognitive Search

 Puede usar el [parámetro **$orderby** de OData ](query-odata-filter-orderby-syntax.md) para aplicar un criterio de ordenación personalizado a los resultados de Azure Cognitive Search. En este artículo se describe con detalle la sintaxis de **$orderby**. Para más información general sobre cómo usar **$orderby** al presentar los resultados de la búsqueda, consulte [Procedimientos para trabajar con los resultados de búsqueda en Azure Cognitive Search](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxis

El parámetro **$orderby** acepta una lista separada por comas de hasta 32 **cláusulas order-by**. La sintaxis de una cláusula order-by se describe mediante la siguiente EBNF ([forma de Backus-Naur extendida](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

También está disponible un diagrama de sintaxis interactivo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxis de OData para Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Consulte [Referencia de sintaxis de expresiones OData para Azure Cognitive Search](search-query-odata-syntax-reference.md) para obtener la EBNF completa.

Cada cláusula tiene criterios de ordenación, seguidos opcionalmente por una dirección de ordenación (`asc` para ascendente o `desc` para descendente). Si no se especifica una dirección, el valor predeterminado es ascendente. Los criterios de ordenación pueden ser la ruta de acceso de un campo `sortable` o una llamada a las funciones [`geo.distance`](search-query-odata-geo-spatial-functions.md) o [`search.score`](search-query-odata-search-score-function.md).

Si varios documentos tienen los mismos criterios de ordenación y no se usa la función `search.score` (por ejemplo, si se ordena por el campo numérico `Rating` y tres documentos tienen una valoración de 4), los empates se resolverán por la puntuación del documento en orden descendente. Cuando las puntuaciones de documento sean las mismas (por ejemplo, cuando no se especifica ninguna consulta de búsqueda de texto completo en la solicitud), entonces el orden relativo de los documentos empatados es indeterminado.

Puede especificar varios criterios de ordenación. El orden de las expresiones determina el criterio de ordenación final. Por ejemplo, para clasificar en orden descendente por puntuación, seguido de la valoración, la sintaxis sería `$orderby=search.score() desc,Rating desc`.

La sintaxis de `geo.distance` en **$orderby** es la misma que la de **$filter**. Cuando se usa `geo.distance` en **$orderby**, el campo al que se aplica debe ser de tipo `Edm.GeographyPoint` y también debe ser `sortable`.

La sintaxis de `search.score` en **$orderby** es `search.score()`. La función `search.score` no toma ningún parámetro.

## <a name="examples"></a>Ejemplos

Clasificar hoteles en orden ascendente por la tarifa base:

    $orderby=BaseRate asc

Clasificar hoteles en orden descendente por valoración y después en orden ascendente por tarifa base (recuerde que el orden ascendente es la opción predeterminada):

    $orderby=Rating desc,BaseRate

Clasificar hoteles en orden descendente por valoración y después en orden ascendente por distancia desde las coordenadas dadas:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Clasificar hoteles en orden descendente por search.score y valoración, y después en orden ascendente por distancia desde las coordenadas dadas. Entre dos hoteles con valoraciones idénticas, el más cercano aparece primero en la lista:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Pasos siguientes  

- [Procedimientos para trabajar con los resultados de búsqueda en Azure Cognitive Search](search-pagination-page-layout.md)
- [Información general sobre el lenguaje de expresiones OData para Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Referencia de sintaxis de expresiones OData para Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Búsqueda de documentos &#40;API REST de Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
