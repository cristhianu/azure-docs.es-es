---
title: 'Tamaños de las máquinas virtuales Windows en Azure: GPU | Microsoft Docs'
description: Enumera los tamaños diferentes de GPU optimizada disponibles para las máquinas virtuales Windows en Azure. Se proporciona información sobre el número de unidades vCPU, discos de datos y NIC, así como sobre el rendimiento de almacenamiento y el ancho de banda de red para los tamaños de esta serie.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 0e809690f0453806402c27773ad0029fc5f64be2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102344"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tamaños de máquinas virtuales optimizadas para GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos y controladores compatibles

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure que ejecutan Windows, deben instalarse controladores de GPU de NVIDIA. La [extensión de controlador de GPU de NVIDIA](../extensions/hpccompute-gpu-windows.md) instala los controladores CUDA de NVIDIA o GRID adecuados en una máquina virtual de la serie N. Instale o administre la extensión mediante Azure Portal o con herramientas como las plantillas de Azure PowerShell o Azure Resource Manager. Consulte la [documentación de la extensión de controlador de GPU de NVIDIA](../extensions/hpccompute-gpu-windows.md) para los sistemas operativos compatibles y los pasos de implementación. Para una información general sobre las extensiones de máquina virtual, consulte [Características y extensiones de las máquinas virtuales de Azure](../extensions/overview.md).

Si decide instalar manualmente los controladores de GPU de NVIDIA, consulte el artículo sobre la [instalación de controladores GPU de la serie N para Windows](n-series-driver-setup.md) para obtener información sobre los sistemas operativos compatibles y los pasos de verificación e instalación.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Otros tamaños
- [Uso general](sizes-general.md)
- [Proceso optimizado](sizes-compute.md)
- [Proceso de alto rendimiento](sizes-hpc.md)
- [Memoria optimizada](sizes-memory.md)
- [Almacenamiento optimizado](sizes-storage.md)
- [Generaciones anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre cómo las [unidades de proceso de Azure (ACU)](acu.md) pueden ayudarlo a comparar el rendimiento en los distintos SKU de Azure.

