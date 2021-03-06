---
title: Creación de una máquina virtual Windows de SQL Server con Azure PowerShell | Microsoft Docs
description: Este tutorial muestra cómo crear una máquina virtual Windows de SQL Server 2017 con Azure PowerShell.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 69781b7c5187bd9166946a96a8b47233d0f77208
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100687"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Inicio rápido: Creación de una máquina virtual Windows de SQL Server con Azure PowerShell

Esta guía de inicio rápido le ayuda a crear una máquina virtual de SQL Server con Azure PowerShell.

> [!TIP]
> - Esta guía de inicio rápido describe una manera de aprovisionar y conectarse a una máquina virtual de SQL rápidamente. Para más información sobre otras opciones de Azure PowerShell para crear máquinas virtuales de SQL, consulte la [guía de aprovisionamiento de máquinas virtuales de SQL Server con Azure PowerShell](virtual-machines-windows-ps-sql-create.md).
> - Si tiene alguna pregunta sobre las máquinas virtuales de SQL Server, consulte las [Preguntas más frecuentes](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Obtener una suscripción de Azure

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.


## <a id="powershell"></a> Obtener Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Configuración de PowerShell

1. Abra PowerShell y establezca el acceso a su cuenta de Azure mediante la ejecución del comando **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Debería ver una pantalla en la que puede especificar sus credenciales. Use el mismo correo electrónico y la misma contraseña que usa para iniciar sesión en el portal de Azure.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

1. Defina una variable con un nombre único de grupo de recursos. Para simplificar el resto del inicio rápido, los restantes comandos usan este nombre como base de otros nombres de recursos.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Defina la ubicación de una región de Azure de destino para todos los recursos de máquina virtual.

   ```powershell
   $Location = "East US"
   ```

1. Cree el grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Configuración de la red

1. Cree una red virtual, una subred, una dirección IP pública. Estos recursos se utilizan para proporcionar conectividad de red a la máquina virtual y conectarse a Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Cree un grupo de seguridad de red. Configure reglas para permitir conexiones de SQL Server y de escritorio remoto (RDP).

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Cree la interfaz de red.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Creación de la máquina virtual con SQL

1. Defina las credenciales con las que inicia sesión en la máquina virtual. El nombre de usuario es "azureadmin". Asegúrese de que cambia \<contraseña> antes de ejecutar el comando.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Cree un objeto de configuración de máquina virtual y, a continuación, cree la máquina virtual. El siguiente comando crea una máquina virtual de SQL Server 2017 Developer Edition en Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > Se tarda unos minutos en crearla.

## <a name="install-the-sql-iaas-agent"></a>Instalación del Agente de IaaS de SQL

Para obtener las características de máquina virtual SQL e integración del portal, debe instalar la [Extensión del Agente de IaaS de SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Para instalar al agente en la nueva máquina virtual, ejecute el siguiente comando después de crearla.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Escritorio remoto en la máquina virtual

1. Use el siguiente comando para recuperar la dirección IP pública para la nueva máquina virtual.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Use la dirección IP devuelta como parámetro de línea de comandos a **mstsc** para iniciar una sesión de Escritorio remoto en la nueva máquina virtual.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Cuando se le pidan credenciales, elija introducir las de una cuenta diferente. Escriba el nombre de usuario con una barra diagonal inversa (por ejemplo, `\azureadmin`) y la contraseña que ha definido anteriormente en esta guía de inicio rápido.

## <a name="connect-to-sql-server"></a>Conexión con SQL Server

1. Después de iniciar sesión en la sesión de Escritorio remoto, inicie **SQL Server Management Studio 2017** desde el menú Inicio.

1. En el cuadro de diálogo **Conectar con el servidor**, mantenga los valores predeterminados. El nombre de servidor es el de la máquina virtual. La autenticación se establece en **Autenticación de Windows**. Seleccione **Conectar**.

Ya está conectado a SQL Server localmente. Si desea conectarse de forma remota, deberá [configurar la conectividad](virtual-machines-windows-sql-connect.md) desde el portal o manualmente.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no necesita que la máquina virtual se ejecute continuamente, puede detenerla cuando no se esté usando y así evitar cargos innecesarios. El siguiente comando detiene la VM, pero la deja disponible para usarla en el futuro.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

También puede eliminar de forma definitiva todos los recursos asociados a la máquina virtual con el comando **Remove-AzResourceGroup**. Si lo hace, también se elimina la máquina virtual de forma permanente, así que use este comando con cuidado.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una máquina virtual de SQL Server 2017 con Azure PowerShell. Para más información sobre la migración de los datos a la nueva instancia de SQL Server, consulte el artículo siguiente.

> [!div class="nextstepaction"]
> [Migración de una base de datos a una máquina virtual SQL](virtual-machines-windows-migrate-sql.md)
