---
title: Creación de un clúster de Kubernetes habilitado para Azure Dev Spaces mediante Azure Cloud Shell
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 10/04/2018
ms.topic: conceptual
description: Aprenda a crear rápidamente un clúster de Kubernetes habilitado para Azure Dev Spaces directamente desde el explorador sin instalar nada.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenedores, Helm, service mesh, enrutamiento de service mesh, kubectl, k8s
ms.openlocfilehash: cd0c8c3c26feefe3448ada1cf1575706cd17e525
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808696"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Creación de un clúster de Kubernetes mediante Azure Cloud Shell

Puede usar [Azure Cloud Shell](/azure/cloud-shell) para crear un clúster de Azure Kubernetes Service mediante el botón **Probar** de esta página. Si no ha iniciado sesión, siga los avisos para iniciar sesión con una cuenta de Azure y, luego, escriba los comandos en el símbolo del sistema de Azure Cloud Shell cuando aparezca.

## <a name="create-the-cluster"></a>Creación de clústeres

En primer lugar, cree el grupo de recursos en una [región que admita Azure Dev Spaces][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Crear un clúster de Kubernetes con el siguiente comando:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

La operación de creación del clúster tarda unos minutos.  Cuando haya finalizado, la salida se muestra en el formato JSON. Busque `provisioningState` y compruebe que su valor es `Succeeded`.

## <a name="next-steps"></a>Pasos siguientes

Consulte [Azure Dev Spaces](/azure/dev-spaces/) para obtener vínculos a tutoriales completos.

> [!IMPORTANT]
> Muchas de los tutoriales e inicios rápidos de Azure Dev Spaces usan la CLI de Azure Dev Spaces para realizar operaciones. La CLI de Azure Dev Spaces no se puede instalar en Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations