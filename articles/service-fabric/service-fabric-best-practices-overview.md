---
title: Procedimientos recomendados para aplicaciones y clústeres de Azure Service Fabric | Microsoft Docs
description: Procedimientos recomendados para la administración de aplicaciones y clústeres de Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206798"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Procedimientos recomendados para aplicaciones y clústeres de Azure Service Fabric

En este artículo se proporcionan vínculos a procedimientos recomendados para administrar los clústeres y las aplicaciones de Azure Service Fabric. Se recomienda encarecidamente implementar estos procedimientos para optimizar la confiabilidad del entorno de producción. Use una de las [plantillas del clúster de Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) para comenzar a diseñar la solución de producción, o actualizar la plantilla existente para incorporar estas prácticas.

## <a name="security"></a>Seguridad

* [Procedimientos recomendados para la seguridad](service-fabric-best-practices-security.md)

## <a name="networking"></a>Redes

* [Procedimientos recomendados para las redes](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Planeación y escalado de procesos

* [Procedimientos recomendados para el escalado de procesos](service-fabric-best-practices-capacity-scaling.md)
* [Planeamiento de la capacidad de proceso](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infraestructura como código

* [Procedimientos recomendados para la implementación de infraestructura como código](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Supervisión y diagnóstico

* [Procedimientos recomendados para la supervisión y diagnóstico de clústeres](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Diseño de aplicación

* [Procedimientos recomendados para el diseño de aplicaciones](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Lista de comprobación

Después de implementar las prácticas sugeridas en las secciones anteriores, asegúrese de haber integrado todos los procedimientos recomendados de la lista de comprobación sobre la preparación de producción:
* [Lista de comprobación sobre la preparación de producción de Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Pasos siguientes

* Creación de un clúster en máquinas virtuales o equipos que ejecutan Windows Server: [Creación de un clúster independiente con Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creación de un clúster en máquinas virtuales o equipos que ejecutan Linux: [Crear un clúster Linux](service-fabric-cluster-creation-via-portal.md)
* Solución de problemas de Service Fabric: [Guías de solución de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)