---
title: Indexación de documentos en Azure Data Lake Storage Gen2 (versión preliminar)
titleSuffix: Azure Cognitive Search
description: Obtenga información sobre cómo indexar contenido y metadatos en Azure Data Lake Storage Gen2. Esta característica actualmente está en versión preliminar pública.
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 390f45ce874e4e64d3a7b9a6c3944983e0ba9028
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719924"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexación de documentos en Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> La compatibilidad de Azure Data Lake Storage Gen2 está actualmente en versión preliminar pública. La funcionalidad de versión preliminar se ofrece sin un acuerdo de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Puede solicitar acceso a las versiones preliminares rellenando [este formulario](https://aka.ms/azure-cognitive-search/indexer-preview). En la [API REST, versión 2019-05-06-Preview](search-api-preview.md) se proporciona esta característica. Actualmente no hay compatibilidad con el portal ni con el SDK de .NET.


Al configurar una cuenta de almacenamiento de Azure, tiene la opción de habilitar el [espacio de nombres jerárquico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Esto permite que la colección de contenido de una cuenta se organice en una jerarquía de directorios y subdirectorios anidados. Al habilitar un espacio de nombres jerárquico, habilita a su vez [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

En este artículo se describe cómo empezar a usar la indexación de documentos que se encuentran en Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configuración de un indizador de Azure Data Lake Storage Gen2

Hay algunos pasos que debe completar para indexar contenido de Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Paso 1: Suscríbase a la vista previa

Regístrese para obtener la versión preliminar del indizador Data Lake Storage Gen2 rellenando [este formulario](https://aka.ms/azure-cognitive-search/indexer-preview). Recibirá un correo de confirmación cuando se le haya aceptado en la versión preliminar.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Paso 2: siga los pasos de configuración de la indexación de almacenamiento de blobs de Azure

Cuando haya recibido la confirmación de que el registro de la versión preliminar se ha realizado correctamente, está listo para crear la canalización de indexación.

Se pueden indexar el contenido y los metadatos de Data Lake Storage Gen2 mediante el uso de la [API REST, versión 2019-05-06-Preview](search-api-preview.md). Por el momento, no hay compatibilidad con el portal ni con .NET SDK.

La indexación de contenido en Data Lake Storage Gen2 es idéntica a la indexación de contenido en el almacenamiento de blobs de Azure. De modo que, para entender cómo configurar el origen de datos de Data Lake Storage Gen2, el índice y el indizador, consulte [Cómo indexar documentos en Azure Blob Storage con Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). En el artículo de almacenamiento de blobs también se proporciona información sobre los formatos de documento que se admiten, las propiedades de metadatos de blobs que se extraen, la indexación incremental, etc. Esta información será la misma para Data Lake Storage Gen2.

## <a name="access-control"></a>Control de acceso

Azure Data Lake Storage Gen2 implementa un [modelo de control de acceso](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) compatible con el control de acceso basado en rol (RBAC) de Azure y las listas de control de acceso (ACL) tipo POSIX. Al indexar contenido desde Data Lake Storage Gen2, Azure Cognitive Search no extraerá la información de RBAC y de ACL del contenido. Como resultado, esta información no se incluirá en el índice de Azure Cognitive Search.

Si es importante mantener el control de acceso en cada documento del índice, el hecho de implementar el [recorte de seguridad](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search) dependerá del desarrollador de la aplicación.