---
title: 'Ejemplo: Ubicaciones permitidas de emparejamiento de ExpressRoute'
description: Esta definición de directiva de ejemplo requiere que ExpressRoute use ubicaciones de emparejamiento especificadas.
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: 8b991c5b83f5d4ca23963aef089795acd5b96bd6
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076453"
---
# <a name="sample---allowed-peering-location-for-expressroute"></a>Ejemplo: ubicación de emparejamiento permitida para ExpressRoute

Esta directiva requiere que ExpressRoute use ubicaciones de emparejamiento especificadas. Se especifica una matriz de ubicaciones de emparejamiento permitidas.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Plantilla de ejemplo

[!code-json[main](../../../../policy-templates/samples/Network/express-route-peeringLocation/azurepolicy.json "Allowed Peering Location for ExpressRoute")]

Puede implementar esta plantilla mediante [Azure Portal](#deploy-with-the-portal), con [PowerShell](#deploy-with-powershell) o con la [CLI de Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implementación con el portal

[![Implementación del ejemplo de directiva en Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FNetwork%2Fexpress-route-peeringLocation%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "express-route-peeringLocation" -DisplayName "Allowed Peering Location for ExpressRoute" -description "This policy enables you to specify a set of allowed peering location for ExpressRoute" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -listOfLocations <Allowed Peering Locations> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Limpieza de la implementación de PowerShell

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Implementación con la CLI de Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'express-route-peeringLocation' --display-name 'Allowed Peering Location for ExpressRoute' --description 'This policy enables you to specify a set of allowed peering location for ExpressRoute' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Network/express-route-peeringLocation/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "express-route-peeringLocation"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpieza de la implementación de la CLI de Azure

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Pasos siguientes

- Consulte más ejemplos en [Ejemplos de Azure Policy](index.md).