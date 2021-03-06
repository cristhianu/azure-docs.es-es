---
title: Personalización de las configuraciones de red de una máquina virtual de conmutación por error | Microsoft Docs
description: Se proporciona información general sobre la personalización de configuraciones de red de una máquina virtual de conmutación por error en la replicación de máquinas virtuales de Azure mediante Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 8038f7c909cfeaf15039afa7335dd6b0460a2622
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293467"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalización de las configuraciones de red de la máquina virtual de Azure de destino

En este artículo se proporciona una guía para personalizar las configuraciones de red en la máquina virtual de Azure de destino cuando se replican y recuperan máquinas virtuales de Azure de una región a otra mediante [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de comenzar

Obtenga información sobre cómo Site Recovery proporciona recuperación ante desastres para [este escenario](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Compatibilidad con los recursos de red

Al replicar máquinas virtuales de Azure, se pueden proporcionar las siguientes configuraciones de recursos clave para la máquina virtual de conmutación por error:

- [Equilibrador de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Dirección IP pública](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Grupo de seguridad de red](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) tanto de la subred como de la NIC

 > [!IMPORTANT]
  > Por el momento, esta configuración solo se admite en la operación de conmutación por error, pero no en la de conmutación por error de prueba.

## <a name="prerequisites"></a>Requisitos previos

- Asegúrese de planear las configuraciones de recuperación de antemano.
- Cree los recursos de red por adelantado. Proporciónelos como una entrada para que el servicio de Azure Site Recovery pueda respetar esta configuración y asegúrese de que la máquina virtual de conmutación por error se adhiere a ella.

## <a name="customize-failover-networking-configurations"></a>Personalización de las configuraciones de red de conmutación por error

1. Vaya a **Elementos replicados**. 
2. Seleccione la máquina virtual de Azure que desee.
3. Seleccione **Proceso y red**  y, después,**Editar**. Observe que los valores de la configuración de la NIC incluyen los recursos correspondientes en el origen. 

     ![Personalización de las configuraciones de red de conmutación por error](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Seleccione **Editar** junto a la NIC que desee configurar. En la siguiente hoja que se abre, seleccione en el destino los recursos creados previamente correspondientes.

    ![Edición de la configuración de la NIC](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Seleccione **Aceptar**.

Site Recovery ahora respetará esta configuración y garantizará que la máquina virtual de la conmutación por error esté conectada al recurso seleccionado a través de la NIC correspondiente.

## <a name="troubleshooting"></a>solución de problemas

### <a name="unable-to-view-or-select-a-resource"></a>No se puede ver o seleccionar un recurso

Si no puede seleccionar o ver alguno de los recursos de red, realice las siguientes comprobaciones y condiciones:

- El campo de destino de un recurso de red solo se habilita si la máquina virtual de origen tenía una entrada correspondiente. Esto se basa en el principio de que en un escenario de recuperación ante desastres, se necesita la versión exacta o una versión reducida verticalmente del origen.
- En cada uno de los recursos de red, se aplican algunos filtros de la lista desplegable para asegurarse de que la máquina virtual de conmutación por error se puede asociar automáticamente al recurso seleccionado y que se mantiene la confiabilidad de la conmutación por error. Estos filtros se basan en las mismas condiciones de red que se habrían verificado al configurar la máquina virtual de origen.

Validaciones del equilibrador de carga interno:

- La suscripción y la región del equilibrador de carga y de la máquina virtual de destino deben ser iguales.
- La red virtual asociada con el equilibrador de carga interno y la de la máquina virtual de destino deben coincidir.
- La SKU de la dirección IP pública de la máquina virtual de destino y la del equilibrador de carga interno deben coincidir.
- Si la máquina virtual de destino está configurada para colocarse en una zona de disponibilidad, compruebe si el equilibrador de carga tiene redundancia de zona o forma parte de cualquier zona de disponibilidad. (los equilibradores de carga de la SKU básica no admiten zonas y, en este caso, no se mostrarán en la lista desplegable).
- Asegúrese de que el equilibrador de carga interno tiene un grupo back-end y una configuración de front-end creados previamente.


Dirección IP pública:
    
- La suscripción y la región de la dirección IP pública y de la máquina virtual de destino deben ser las mismas.
- La SKU de la dirección IP pública de la máquina virtual de destino y la del equilibrador de carga interno deben coincidir.

Grupo de seguridad de red:
- La suscripción y la región del grupo de seguridad de red y de la máquina virtual de destino deben ser las mismas.


> [!WARNING]
> Si la máquina virtual de destino está asociada a un conjunto de disponibilidad, debe asociar tanto la dirección IP pública como el equilibrador de carga interno de la misma SKU con la de la dirección IP pública y el equilibrador de carga interno de la otra máquina virtual del conjunto de disponibilidad. Si no lo hace, la conmutación por error podría no realizarse correctamente.
