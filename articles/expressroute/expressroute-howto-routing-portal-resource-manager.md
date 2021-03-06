---
title: 'Configuración del emparejamiento de un circuito en ExpressRoute: Azure | Microsoft Docs'
description: En este artículo se documentan los pasos para crear y aprovisionar el emparejamiento privado de ExpressRoute y de Microsoft. Este artículo también muestra cómo comprobar el estado, actualizar o eliminar emparejamientos de un circuito.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 08d8103c4b35148a87d347e31b11c7c8c968598b
ms.sourcegitcommit: dda9fc615db84e6849963b20e1dce74c9fe51821
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67622347"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Creación y modificación del emparejamiento de un circuito ExpressRoute

Este artículo le ayuda a crear y administrar la configuración de enrutamiento de un circuito ExpressRoute en Azure Resource Manager mediante Azure Portal. También puede comprobar el estado de los emparejamientos de un circuito ExpressRoute, así como el modo de actualizarlos o eliminarlos y desaprovisionarlos. Si quiere usar un método diferente para trabajar con el circuito, seleccione un artículo de la lista siguiente:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [CLI de Azure](howto-routing-cli.md)
> * [Vídeo: pares privados](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vídeo: pares públicos](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vídeo: emparejamiento de Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (clásico)](expressroute-howto-routing-classic.md)
> 

Puede configurar el emparejamiento privado de Azure y de Microsoft para un circuito ExpressRoute (el emparejamiento público de Azure está en desuso para los nuevos circuitos). Puede establecer las configuraciones entre pares en cualquier orden. Pero tiene que asegurarse de que completa cada configuración entre pares de una en una. Para más información sobre el enrutamiento de dominios y emparejamientos, consulte el artículo [sobre los circuitos y emparejamientos](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

* Antes de comenzar la configuración, asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md), la página de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md).
* Tiene que tener un circuito ExpressRoute activo. Antes de continuar, siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) y para que el proveedor de conectividad habilite el circuito. Para configurar los emparejamientos, el circuito ExpressRoute debe estar en estado habilitado y aprovisionado. 
* Si tiene pensado usar una clave compartida o un hash MD5, asegúrese de utilizarlos en ambos lados del túnel y limite el número de caracteres alfanuméricos a 25 como máximo. Los caracteres especiales no se admiten. 

Estas instrucciones se aplican solo a los circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de capa 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente VPN IP, como MPLS), el mismo proveedor de conectividad configura y administra el enrutamiento. 

> [!IMPORTANT]
> Actualmente no anunciamos los emparejamientos configurados por proveedores de servicios a través del Portal de administración de servicios. Se está trabajando para habilitar esta funcionalidad pronto. Antes de configurar los emparejamientos BGP, realice las comprobaciones pertinentes con su proveedor de servicios.
> 
> 

## <a name="msft"></a>Emparejamiento de Microsoft

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento de Microsoft para un circuito ExpressRoute.

> [!IMPORTANT]
> Se anunciarán todos los prefijos de servicio para el emparejamiento de Microsoft de los circuitos ExpressRoute que se configuraron antes del 1 de agosto de 2017, incluso si no se definen filtros de ruta. No se anunciará ningún prefijo para el emparejamiento de Microsoft de los circuitos ExpressRoute que se configuraron el 1 de agosto de 2017 o con posterioridad, hasta que se asocie un filtro de ruta al circuito. Para más información, consulte [Configuración de un filtro de ruta para el emparejamiento de Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Creación del emparejamiento de Microsoft

1. Configure el circuito de ExpressRoute. Antes de continuar, compruebe el **estado del proveedor** para asegurarse de que el proveedor de conectividad ha aprovisionado el circuito por completo.

   Si el proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitar a su proveedor de conectividad que habilite para usted la configuración entre pares de Microsoft. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga los pasos siguientes.

   **Estado del circuito y proveedor: No aprovisionado**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Estado de proveedor: No aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Estado del circuito y proveedor: Aprovisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Estado de proveedor: Aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Establezca la configuración del emparejamiento de Microsoft para el circuito. Asegúrese de que tiene la siguiente información antes de empezar:

   * Una subred /30 para el vínculo principal. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR). Desde esta subred asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador.
   * Una subred /30 para el vínculo secundario. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR). Desde esta subred asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN. Para los vínculos principal y secundario, debe usar el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
   * Prefijos anunciados: tiene que proporcionar una lista de todos los prefijos que planea anunciar en la sesión BGP. Se aceptan solo prefijos de direcciones IP públicas. Si tiene pensado enviar un conjunto de prefijos, puede enviar una lista separada por comas. Estos prefijos tienen que estar registrados a su nombre en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
   * **Opcional -** ASN de cliente: si anuncia prefijos que no están registrados en el número de AS de emparejamiento, puede especificar el número de AS en el que están registrados.
   * Nombre del enrutamiento del Registro: puede especificar el RIR o TIR en el que están registrados el número AS y los prefijos.
   * **Opcional:** un hash MD5 si elige usar uno.
3. Puede seleccionar el emparejamiento que desea configurar, como se muestra en el ejemplo siguiente. Seleccione la fila del emparejamiento de Microsoft.

   [![Seleccionar la fila del emparejamiento de Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Select the Microsoft peering row")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Configure el emparejamiento de Microsoft **Guarde** la configuración una vez que haya especificado todos los parámetros. La siguiente imagen muestra un ejemplo de configuración:

   ![Configuración del emparejamiento de Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

   Si el circuito llega a un estado de validación necesaria, debe abrir una incidencia de soporte técnico para mostrar la prueba de propiedad de los prefijos a nuestro equipo de soporte técnico. Puede abrir un vale de soporte técnico directamente desde el portal, tal como se muestra en el ejemplo siguiente:

   ![Validación necesaria: incidencia de soporte técnico](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Después de que la configuración se haya aceptado correctamente, verá algo similar a la imagen siguiente:

   ![Estado de emparejamiento: Configurado](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Peering status: Configured")

### <a name="getmsft"></a>Visualización de detalles del emparejamiento de Microsoft

Para ver las propiedades de un emparejamiento de Microsoft, seleccione la fila del emparejamiento.

[![Ver las propiedades de emparejamiento de Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Ver propiedades")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Actualización de la configuración de emparejamiento de Microsoft

Puede seleccionar la fila del emparejamiento que desee modificar y, después, modificar las propiedades del mismo y guardar las modificaciones.

![Seleccionar fila de emparejamiento](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Eliminación del emparejamiento de Microsoft

Para quitar la configuración de emparejamiento, haga clic en el icono de eliminación, como se muestra a continuación:

![Eliminación de emparejamiento](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Configuración entre pares privados de Azure

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento privado de Azure para un circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Creación de un emparejamiento privado de Azure

1. Configure el circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de conectividad ha aprovisionado el circuito por completo. 

   Si su proveedor de conectividad ofrece servicios administrados de nivel 3, puede solicitarle que habilite la configuración entre pares privados de Azure. En ese caso, no necesita seguir las instrucciones que aparecen en las secciones siguientes. Por otra parte, si su proveedor de conectividad no administra este enrutamiento, una vez que cree el circuito siga los pasos siguientes.

   **Estado del circuito y proveedor: No aprovisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Estado de proveedor: No aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Estado del circuito y proveedor: Aprovisionado**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Estado de proveedor: Aprovisionado")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Establecimiento de la configuración entre pares privados de Azure para el circuito. Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:

   * Una subred /30 para el vínculo principal. La subred no debe ser parte de ningún espacio de direcciones reservado para redes virtuales. Desde esta subred asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador.
   * Una subred /30 para el vínculo secundario. La subred no debe ser parte de ningún espacio de direcciones reservado para redes virtuales. Desde esta subred asignará la primera dirección IP utilizable para el enrutador, ya que Microsoft usa la segunda dirección IP utilizable para su enrutador.
   * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN. Para los vínculos principal y secundario, debe usar el mismo identificador de VLAN.
   * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Puede usar un número de AS privado para este emparejamiento, excepto el comprendido entre 65515 y 65520, ambos inclusive.
   * Debe anunciar las rutas del enrutador perimetral local a Azure a través de BGP al configurar el emparejamiento privado.
   * **Opcional:** un hash MD5 si elige usar uno.
3. Seleccione la fila de emparejamiento privado de Azure, como se muestra en el ejemplo siguiente:

   [![Seleccionar la fila del emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Select the private peering row")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Configure el emparejamiento privado. **Guarde** la configuración una vez que haya especificado todos los parámetros.

   ![configurar el emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Una vez que la configuración se haya aceptado correctamente, verá algo similar al siguiente ejemplo:

   ![Emparejamiento privado guardado](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Visualización de los detalles del emparejamiento privado

Para ver las propiedades del emparejamiento privado de Azure seleccione el emparejamiento.

[![Ver propiedades de emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "View private peering properties")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Actualización del establecimiento de configuración del emparejamiento privado de Azure

Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo. Después de actualizar, guarde los cambios.

![actualizar emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Eliminación del emparejamiento privado de Azure

Para quitar la configuración de emparejamiento, seleccione el icono de eliminación, como se muestra a continuación:

> [!WARNING]
> Debe asegurarse de que todas las conexiones de redes virtuales y de ExpressRoute Global Reach se eliminan antes de ejecutar este ejemplo. 
> 
> 

![eliminar emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Configuración entre pares públicos de Azure

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento público de Azure para un circuito ExpressRoute.

> [!Note]
> El emparejamiento público de Azure está en desuso para circuitos nuevos. Para más información, consulte el artículo sobre [emparejamiento en ExpressRoute](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Visualización de detalles de un emparejamiento público de Azure

Para ver las propiedades de un emparejamiento público de Azure, seleccione el emparejamiento.

### <a name="updatepublic"></a>Actualización del establecimiento de configuración del emparejamiento público de Azure

Seleccione la fila del emparejamiento y modifique las propiedades de este.

### <a name="deletepublic"></a>Eliminación del emparejamiento público de Azure

Para quitar la configuración de emparejamiento, seleccione el icono de eliminación.

## <a name="next-steps"></a>Pasos siguientes

Siguiente paso, [Conexión de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para más información sobre los flujos de trabajo de ExpressRoute, vea [Flujos de trabajo de ExpressRoute](expressroute-workflows.md).
* Para más información sobre el emparejamiento de circuitos, vea [Circuitos y dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md).
* Para más información sobre redes virtuales, vea [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).
