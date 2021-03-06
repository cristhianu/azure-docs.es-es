---
title: 'Administración de versiones: LUIS'
titleSuffix: Azure Cognitive Services
description: Las versiones permiten compilar y publicar modelos diferentes. Una buena práctica consiste en clonar el modelo activo actual a una versión diferente de la aplicación antes de realizar cambios en el modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: aa1cbd08bdf8d92653a8f30ae67ecd813e563999
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467518"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Uso de versiones para realizar ediciones y pruebas sin afectar a las aplicaciones de almacenamiento provisional o producción

Las versiones permiten compilar y publicar modelos diferentes. Una buena práctica consiste en clonar el modelo activo actual a una [versión](luis-concept-version.md) diferente de la aplicación antes de realizar cambios en el modelo. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

Para trabajar con versiones, abra la aplicación, seleccione su nombre en la página **Mis aplicaciones** y, a continuación, seleccione **Administrar**, en la barra superior, y **Versiones**, en el panel de navegación de la izquierda. 

La lista de versiones muestra las versiones que se publican, dónde se publican y qué versión está activa actualmente. 

[![Sección Administrar, página de versiones](./media/luis-how-to-manage-versions/versions-import.png "Sección Administrar, página de versiones")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Clonar una versión

1. Seleccione la versión que quiera clonar y, a continuación, seleccione **Clonar** en la barra de herramientas. 

2. En el cuadro de diálogo **Clone version** (Versión de clon), escriba un nombre para la nueva versión, como "0.2".

   ![Cuadro de diálogo de versión de clon](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > La id. de versión puede constar únicamente de caracteres, dígitos o "." y no puede tener más de 10 caracteres.
 
   Se crea una nueva versión con el nombre especificado y se establece como la versión activa.

## <a name="set-active-version"></a>Establecer la versión activa

Seleccione una versión de la lista y, a continuación, seleccione **Activar** en la barra de herramientas. 

[![Sección Administrar, página de versiones, creación de una acción de versión](./media/luis-how-to-manage-versions/versions-other.png "Sección Administrar, página de versiones, creación de una acción de versión")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importar versión

1. Seleccione **Importar versión** en la barra de herramientas. 

2. En la ventana emergente **Importar nueva versión**, escriba el nuevo nombre de la versión, de diez caracteres. Basta con establecer una id. de versión si la versión en el archivo JSON ya existe en la aplicación.

    ![Sección Administrar, página de versiones, importar nueva versión](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Después de importar una versión, la nueva versión se convierte en la versión activa.

### <a name="import-errors"></a>Errores de importación

* Errores del tokenizador: Si se produce un **error del tokenizador** al importar, está intentando importar una versión que utiliza un [tokenizador](luis-language-support.md#custom-tokenizer-versions) diferente al que la aplicación usa actualmente. Para corregir este error, consulte [Migración entre versiones de tokenizador](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Otras acciones

* Para **eliminar** una versión, seleccione una versión de la lista y, a continuación, seleccione **Eliminar** desde la barra de herramientas. Seleccione **Aceptar**. 
* Para **cambiar el nombre** de una versión, seleccione una versión de la lista y, a continuación, seleccione **Cambiar nombre** desde la barra de herramientas. Escriba un nombre nuevo y seleccione **Listo**. 
* Para **exportar** una versión, seleccione una versión de la lista y, a continuación, seleccione **Exportar aplicación** desde la barra de herramientas. Elija JSON para exportar para copia de seguridad, elija **Exportar para contenedor** para [usar esta aplicación en un contenedor de LUIS](luis-container-howto.md).  

