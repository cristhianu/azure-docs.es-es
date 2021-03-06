---
title: Introducción a Azure FarmBeats
description: Proporciona información general sobre Azure FarmBeats.
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12882860b8a9beadd4e56cbb151d670fac4da43b
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797161"
---
# <a name="overview-of-azure-farmbeats"></a>Introducción a Azure FarmBeats

Azure FarmBeats es una colección de servicios y funcionalidades de Azure, diseñada para ayudarle a crear rápidamente soluciones inteligentes controladas por datos en agricultura. Azure FarmBeats es una oferta de Azure Marketplace que le permite adquirir, agregar y procesar datos relacionados con la agricultura desde diversos orígenes, como sensores, drones, cámaras, satélites, sin invertir en recursos de ingeniería de datos profundos.

> [!NOTE]
> Azure FarmBeats actualmente está en versión preliminar pública. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats se proporciona sin un acuerdo de nivel de servicio. Use el [foro de Azure FarmBeats](https://aka.ms/FarmBeatsMSDN ) para soporte técnico.

Con Azure FarmBeats, podrá obtener datos de diversos orígenes, como sensores, satélites, drones, todo ello en el contexto de una granja de servidores (un área geográfica de interés).

Con esto, podrá:

- Agregar conjuntos de datos agrícolas de varios proveedores
- Crear rápidamente modelos de inteligencia artificial/lenguaje máquina (AI/ML) mediante la fusión de distintos conjuntos de valores.

Azure FarmBeats le proporciona una forma sólida y sencilla de hacer lo siguiente:

- Cree un mapa de la granja con un archivo GeoJSON.
- Evalúe el estado de la granja con el índice de vegetación y el índice de agua basado en imágenes satelitales.
- Obtenga recomendaciones sobre cuántos sensores se deben usar y dónde colocarlos.
- Realice un seguimiento de la condición de granja mediante la visualización de datos del suelo recopilados por sensores de varios proveedores de sensores.
- Obtenga un mapa de la humedad del suelo, en función de la fusión de datos de satélites y sensores.
- Obtenga información útil al crear modelos de AI/ML sobre conjuntos de datos agregados.
- Cree o aumente su solución de agricultura digital proporcionando avisos de estado de la granja.

Los componentes de Azure FarmBeats se describen en las siguientes secciones de este artículo.

## <a name="data-hub"></a>Centro de datos

Una capa de API, que permite la agregación, la normalización y la contextualización de varios conjuntos de datos agrícolas entre proveedores. A partir de esta versión preliminar, puede aprovechar dos proveedores de sensores, [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) y [Teralytic](https://teralytic.com/), un proveedor de imágenes satelitales, [Sentinel-2](https://sentinel.esa.int/web/sentinel/home), y dos proveedores de imágenes de drones, [senseFly](https://www.sensefly.com/) y [SlantRange](https://slantrange.com/). El centro de datos está diseñado como plataforma de API, y estamos trabajando con muchos más proveedores para que se integren en Azure FarmBeats, para que tenga más opciones al crear su solución.

## <a name="accelerator"></a>Acelerador

Una solución de ejemplo, creada a partir del centro de datos, que acelera el desarrollo de la interfaz de usuario y de modelos. Esta aplicación web aprovecha las API para mostrar la visualización de datos ingeridos de sensores, como gráficos, y la visualización de la salida del modelo como mapas. Por ejemplo, puede usar el acelerador para crear rápidamente una granja y obtener fácilmente un mapa de índice de vegetación o un mapa de ubicación de sensor para esa granja.

## <a name="resources"></a>Recursos

Visite le [blog](https://aka.ms/AzureFarmBeats) y los [foros](https://aka.ms/FarmBeatsMSDN) de FarmBeats.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con Azure FarmBeats, visite [Azure Marketplace](https://aka.ms/FarmBeatsMarketplace) para implementarlo.
