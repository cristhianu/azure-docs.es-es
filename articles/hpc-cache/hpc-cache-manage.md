---
title: Administración y actualización de Azure HPC Cache
description: Administración y actualización de Azure HPC Cache mediante Azure Portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 62b54bfe120acdde1fd22c4a0d04165ea7243b50
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582199"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Administración de la memoria caché desde Azure Portal

En la página de información general de caché de Azure Portal se muestran los detalles del proyecto, el estado de la memoria caché y las estadísticas básicas de dicha memoria. También tiene controles para eliminar la memoria caché, vaciar los datos para el almacenamiento a largo plazo o actualizar el software.

Para abrir la página de información general, seleccione el recurso de caché en Azure Portal. Por ejemplo, cargue la página **Todos los recursos** y haga clic en el nombre de la memoria caché.

![Captura de pantalla de la página Información general de una instancia de Azure HPC Cache](media/hpc-cache-overview.png) <!-- placeholder is identical to hpc-cache-new-overview.png; replace with better image (showing graphs, full sidebar) when available -->

Los botones que se encuentran en la parte superior de la página pueden ayudarlo a administrar la memoria caché:

* [**Vaciar**](#flush-cached-data): Escribe todos los datos en caché en destinos de almacenamiento
* [**Actualizar**](#upgrade-cache-software): actualiza el software de caché
* **Refrescar**: vuelve a cargar la página de información general
* [**Eliminar**](#delete-the-cache): destruye permanentemente la memoria caché

Lea más información sobre estas opciones a continuación.

## <a name="flush-cached-data"></a>Vaciado de los datos en caché

El botón **Vaciar** de la página de información general indica a la memoria caché que escriba inmediatamente todos los datos modificados que están almacenados en la memoria caché en los destinos de almacenamiento de back-end. La memoria caché guarda regularmente los datos en los destinos de almacenamiento, por lo que no es necesario hacerlo manualmente a menos que desee asegurarse de que el sistema de almacenamiento de back-end esté actualizado. Por ejemplo, puede usar **Vaciar** antes de tomar una instantánea de almacenamiento o comprobar el tamaño del conjunto de datos.

> [!NOTE]
> Durante el proceso de vaciado, la memoria caché no puede atender las solicitudes del cliente. El acceso a la memoria caché se suspende y se reanuda una vez finalizada la operación.

Cuando se inicia la operación de vaciado de la memoria caché, esta deja de aceptar solicitudes de cliente y su estado de en la página de información general cambia a **Vaciado**.

Los datos de la memoria caché se guardan en los destinos de almacenamiento adecuados. El proceso puede tardar unos minutos, una hora o más en función de la cantidad de datos que se hayan escrito en la memoria caché recientemente.

Una vez que todos los datos se guardan en destinos de almacenamiento, la memoria caché empieza de nuevo a tomar las solicitudes de cliente. El estado de la memoria caché vuelve a **Correcto**.

## <a name="upgrade-cache-software"></a>Actualización del software de caché

Si hay disponible una nueva versión de software, se activa el botón **Actualizar**. También puede ver un mensaje en la parte superior de la página sobre la actualización del software.

![Captura de pantalla de la fila superior de botones con el botón Actualizar habilitado](media/hpc-cache-upgrade-button.png)

El acceso de cliente no se interrumpe durante una actualización de software, pero el rendimiento de la memoria caché se ralentiza. Planee la actualización de software durante horas de menor uso o en un período de mantenimiento planeado.

La actualización de software puede tardar varias horas. Las memorias caché configuradas con un mayor rendimiento tardan más tiempo en actualizarse que las que tienen valores de rendimiento de pico más pequeños.

Cuando haya una actualización de software disponible, tendrá varios días para aplicarla manualmente. La fecha de finalización aparece en el mensaje de actualización. Si no actualiza durante ese tiempo, Azure aplica automáticamente la actualización a la memoria caché. El momento de la actualización automática no es configurable. Si le preocupa el impacto en el rendimiento de la memoria caché, debe actualizar el software usted mismo antes de que expire el período de tiempo.

Haga clic en el botón **Actualizar** para comenzar la actualización de software. El estado de la memoria caché cambia a **Actualizando** hasta que se complete la operación.

## <a name="delete-the-cache"></a>Eliminación de la caché

El botón **Eliminar** destruye la memoria caché. Cuando se elimina una memoria caché, todos sus recursos se destruyen y ya no incurren en cargos de cuenta.

Los destinos de almacenamiento no se ven afectados cuando se elimina la memoria caché. Puede agregarlos a una memoria caché futura más adelante, o bien decomisarlos por separado.

La memoria caché vacía automáticamente los datos no guardados en los destinos de almacenamiento como parte de su cierre final.

## <a name="cache-metrics-and-monitoring"></a>Supervisión y métricas de caché

La página de información general muestra gráficos para algunas estadísticas básicas de caché: rendimiento de caché, operaciones por segundo y latencia.

![Captura de pantalla de tres gráficos de líneas que muestran las estadísticas mencionadas anteriormente para una memoria caché de ejemplo](media/hpc-cache-overview-stats.png)

Estos gráficos forman parte de las herramientas de supervisión y análisis integradas de Azure. Existen otras herramientas y alertas disponibles en las páginas del encabezado **Supervisión** de la barra lateral del portal. Obtenga más información en la sección del portal de la [documentación de Supervisión de Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Pasos siguientes

<!-- * Learn more about metrics and statistics for hpc cache -->
* Más información sobre las [herramientas de métricas y estadísticas de Azure](../azure-monitor/index.yml)
* Obtener [ayuda con la instancia de Azure HPC Cache](hpc-cache-support-ticket.md)
