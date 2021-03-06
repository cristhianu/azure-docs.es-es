---
title: 'Ejemplos de Azure PowerShell: Service Fabric | Microsoft Docs'
description: 'Ejemplos de Azure PowerShell: Service Fabric'
services: service-fabric
documentationcenter: service-fabric
author: athinanthny
manager: chackdan
editor: ''
tags: ''
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: service-fabric
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: service-fabric
ms.date: 11/29/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 73e88e692b68d73c90176a6f5b8fce06bdf8b8c7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035788"
---
# <a name="azure-powershell-samples"></a>Ejemplos de Azure PowerShell

En la tabla siguiente se incluyen vínculos a ejemplos de scripts de PowerShell que crean y administran clústeres, aplicaciones y servicios de Service Fabric.

[!INCLUDE [links to azure cli and service fabric cli](../../includes/service-fabric-powershell.md)]

| | |
|-|-|
| **Crear clúster** ||
| [Creación de un clúster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Crea un clúster de Azure Service Fabric. |
| **Administración de clúster, nodos e infraestructura** ||
| [Adición de un certificado de la aplicación](./scripts/service-fabric-powershell-add-application-certificate.md)| Agrega un certificado X.509 de aplicación a todos los nodos de un clúster. |
| [Actualización del intervalo de puertos RDP en máquinas virtuales del clúster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Cambia el intervalo de puertos RDP en máquinas virtuales del nodo de clúster en un clúster implementado.|
| [Actualización del usuario administrador y la contraseña para máquinas virtuales del nodo de clúster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Actualiza el usuario administrador y la contraseña para máquinas virtuales del nodo de clúster. |
| [Apertura de un puerto en el equilibrador de carga](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Abre un puerto de aplicación en el equilibrador de carga de Azure para permitir el tráfico entrante en un puerto específico. |
| [Creación de una regla de grupo de seguridad de red entrante](./scripts/service-fabric-powershell-add-nsg-rule.md) | Cree una regla de grupo de seguridad de red entrante para permitir el tráfico entrante al clúster en un puerto específico. |
| **Administrar aplicaciones** ||
| [Implementar una aplicación](./scripts/service-fabric-powershell-deploy-application.md)| Implementar una aplicación en un clúster.|
| [Upgrade an application](./scripts/service-fabric-powershell-upgrade-application.md) (Actualización de una aplicación)| Actualizar una aplicación.|
| [Eliminar una aplicación](./scripts/service-fabric-powershell-remove-application.md)| Eliminar una aplicación de un clúster.|
