---
title: Ventajas de la arquitectura multimaestro de Azure Cosmos DB
description: Descripción de las ventajas de la arquitectura multimaestro de Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788583"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Descripción de las ventajas de la arquitectura multimaestro de Azure Cosmos DB

Los operadores de cuentas de Cosmos DB deben elegir la configuración adecuada de la distribución global para garantizar los requisitos de latencia, disponibilidad y RTO de sus aplicaciones. Las cuentas de Azure Cosmos DB configuradas con múltiples ubicaciones de escritura ofrecen ventajas significativas con respecto a aquellas cuentas con una sola ubicación, ventajas entre las que se incluyen un Acuerdo de Nivel de Servicio de disponibilidad de escritura del 99,999 %, uno de latencia de escritura <10 ms en el percentil 99 y un RTO = 0 en caso de desastre regional.

## <a name="comparison-of-features"></a>Comparación de características

|Requisito de la aplicación|Múltiples ubicaciones de escritura|Ubicación única de escritura|Nota:|
|---|---|---|---|
|Acuerdo de Nivel de Servicio de latencia de escritura de <10ms en P99|**Sí**|Sin|Las cuentas con una única ubicación de escritura incurren en una latencia adicional de red entre regiones por cada escritura.|
|Acuerdo de Nivel de Servicio de latencia de lectura de <10ms en P99|**Sí**|Sí| |
|Acuerdo de Nivel de Servicio de operaciones de escritura del 99,999 %|**Sí**|Sin|Las cuentas con una única ubicación de escritura garantizan un Acuerdo de Nivel de Servicio del 99,99 %|
|RTO = 0|**Sí**|Sin|Tiempo fuera de servicio igual a cero para las operaciones de escritura en caso de desastres regionales. Las cuentas con una única ubicación de escritura tienen un RTO de 15 min.|

## <a name="next-steps"></a>Pasos siguientes

Si desea deshabilitar EnableMultipleWriteLocations en su cuenta de Azure Cosmos, [abra una incidencia de soporte técnico](https://azure.microsoft.com/support/create-ticket/).
