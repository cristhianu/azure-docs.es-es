---
title: Visualización de datos desde Azure Data Explorer mediante Grafana
description: En este procedimiento, aprenderá a configurar Azure Data Explorer como origen de datos de Grafana y a visualizarlos después desde un clúster de ejemplo.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: f1eb9fb0d81d1e9cdf3dd8628a6d7ad1f0ccce92
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581970"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualización de datos desde Azure Data Explorer en Grafana

Grafana es una plataforma de análisis que permite consultar y visualizar datos y crear y compartir paneles basados en las visualizaciones. Grafana proporciona un *complemento* para Azure Data Explorer, que permite conectarse a datos desde Azure Data Explorer y visualizarlos. En este artículo, aprenderá a configurar Azure Data Explorer como origen de datos de Grafana y a visualizarlos después desde un clúster de ejemplo.

Con el siguiente video, puede aprender a usar el complemento Azure Data Explorer de Grafana, configurar Azure Data Explorer como origen de datos para Grafana y, después, visualizar los datos. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Además, puede [configurar el origen de datos](#configure-the-data-source) y [visualizar los datos](#visualize-data), tal como se detalla en el artículo siguiente.

## <a name="prerequisites"></a>Requisitos previos

Necesita lo siguiente para completar este procedimiento:

* [Grafana versión 5.3.0 o una versión posterior](https://docs.grafana.org/installation/) para su sistema operativo

* El [complemento de Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) para Grafana

* Un clúster que incluya los datos de ejemplo de StormEvents. Para más información, consulte [Guía de inicio rápido: Creación de un clúster y de la base de datos de Azure Data Explorer](create-cluster-database-portal.md) e [Ingesta de datos de ejemplo en Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Especificar propiedades y probar la conexión

Con la entidad de servicio asignada al rol *visores*, ahora puede especificar propiedades en la instancia de Grafana y probar la conexión a Azure Data Explorer.

1. En Grafana, en el menú izquierdo, seleccione el icono de engranaje y, a continuación, **Data Sources** (Orígenes de datos).

    ![Orígenes de datos](media/grafana/data-sources.png)

1. Seleccione **Add data source**(Agregar origen de datos).

1. En la página **Data Sources / New** (Orígenes de datos / Nuevo), escriba un nombre para el origen de datos y, a continuación, seleccione el tipo **Azure Data Explorer Datasource** (Origen de datos de Azure Data Explorer).

    ![Nombre y tipo de conexión](media/grafana/connection-name-type.png)

1. Escriba el nombre del clúster en el formulario https://{ClusterName}.{Region}.kusto.windows.net. Especifique los demás valores desde Azure Portal o la CLI. Consulte la tabla debajo de la imagen siguiente para una asignación.

    ![Propiedades de la conexión](media/grafana/connection-properties.png)

    | UI de Grafana | Portal de Azure | CLI de Azure |
    | --- | --- | --- |
    | Id. de suscripción | ID. DE SUSCRIPCIÓN | SubscriptionId |
    | Identificador de inquilino | Identificador de directorio | tenant |
    | Id. de cliente | Identificador de aplicación | appId |
    | Secreto del cliente | Contraseña | password |
    | | | |

1. Seleccione **Guardar y probar**.

    Si la prueba se realiza correctamente, vaya a la sección siguiente. Si tiene algún problema, compruebe los valores especificados en Grafana y revise los pasos anteriores.

## <a name="visualize-data"></a>Visualización de datos

Ahora que ya ha terminado de configurar Azure Data Explorer como origen de datos para Grafana, es el momento de visualizar los datos. Le mostraremos un ejemplo básico aquí, pero puede hacer mucho más. Se recomienda revisar [Escribir consultas para Azure Data Explorer](write-queries.md) para obtener ejemplos de otras consultas ejecutadas en el conjunto de datos de ejemplo.

1. En Grafana, en el menú izquierdo, seleccione el icono del signo más y después **Dashboard** (Panel).

    ![Crear panel](media/grafana/create-dashboard.png)

1. En la pestaña **Add** (Agregar), seleccione **Graph** (Gráfico).

    ![Agregar gráfico](media/grafana/add-graph.png)

1. En el panel de gráfico, seleccione **Panel Title** (Título del panel) y después **Edit** (Editar).

    ![Editar panel](media/grafana/edit-panel.png)

1. En la parte inferior del panel, seleccione **Data Source** (Origen de datos) y después seleccione el origen de datos que ha configurado.

    ![Selección de origen de datos](media/grafana/select-data-source.png)

1. En el panel de consulta, copie la siguiente consulta y después seleccione **Run** (Ejecutar). La consulta desglosa el recuento de eventos por día para el conjunto de datos de ejemplo.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Ejecutar consulta](media/grafana/run-query.png)

1. El gráfico no muestra ningún resultado porque su ámbito predeterminado son los datos de las seis últimas horas. En el menú superior, seleccione **Last 6 hours** (Últimas 6 horas).

    ![Últimas seis horas](media/grafana/last-six-hours.png)

1. Especifique un intervalo personalizado que abarque 2007, el año incluido en el conjunto de datos de ejemplo StormEvents. Seleccione **Aplicar**.

    ![Personalizar intervalo de fecha](media/grafana/custom-date-range.png)

    Ahora el gráfico muestra los datos de 2007, desglosados por días.

    ![Gráfico finalizado](media/grafana/finished-graph.png)

1. En el menú superior, seleccione el icono para guardar: ![Icono Save (Guardar)](media/grafana/save-icon.png).

## <a name="next-steps"></a>Pasos siguientes

* [Write queries for Azure Data Explorer](write-queries.md) (Escritura de consultas del Explorador de datos de Azure)

* [Tutorial: Visualización de datos desde Azure Data Explorer en Power BI](visualize-power-bi.md)
