---
title: Enlaces de Azure IoT Hub para Azure Functions
description: Descubra cómo utilizar los enlaces de IoT Hub en Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, funciones, procesamiento de eventos, proceso dinámico, arquitectura sin servidor
ms.service: azure-functions
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: d8b749a294da379e99e61072ff7a3415c508d2ac
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097641"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Enlaces de Azure IoT Hub para Azure Functions

En este artículo se explica cómo usar enlaces de Azure Functions para IoT Hub. La compatibilidad de IoT Hub se basa en el [enlace de Azure Event Hubs](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Paquetes: Functions 1.x

En Azure Functions versión 1.x, los enlaces de IoT Hub se proporcionan en el paquete NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus), versión 2.x. El código fuente del paquete se encuentra en el repositorio [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) de GitHub.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Paquetes: Functions 2.x

En Functions 2.x, use el paquete [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs), versión 3.x. El código fuente del paquete se encuentra en el repositorio [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) de GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Aunque los ejemplos de código siguientes usan la API de Event Hubs, la sintaxis proporcionada es aplicable para las funciones de IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre desencadenadores y enlaces de Azure Functions](functions-triggers-bindings.md)
