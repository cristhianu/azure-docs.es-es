---
title: Configuración de un grupo de conmutación por error
description: Aprenda a configurar un grupo de conmutación por error automática para una base de datos única Azure SQL Database, un grupo elástico y una instancia administrada mediante Azure Portal, la CLI de Azure y PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: fb9ee2378679c420a7675856ec95e60f6ae1d14f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827152"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configuración de un grupo de conmutación por error para Azure SQL Database

Este tema le enseña a configurar un [grupo de conmutación por error automática](sql-database-auto-failover-group.md) para una base de datos única Azure SQL Database, un grupo elástico y una instancia administrada mediante Azure Portal o PowerShell. 

## <a name="single-database"></a>Base de datos única
Cree el grupo de conmutación por error y agregue una base de datos única mediante Azure Portal o PowerShell.

### <a name="prerequisites"></a>Requisitos previos

Tenga en cuenta los siguientes requisitos previos:

- La configuración del firewall y de inicio de sesión del servidor para el servidor secundario debe coincidir con la del servidor principal. 

### <a name="create-failover-group"></a>Creación de un grupo de conmutación por error

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Cree el grupo de conmutación por error y agregue la base de datos única mediante Azure Portal.

1. Seleccione **Azure SQL** en el menú izquierdo de [Azure Portal](https://portal.azure.com). Si **Azure SQL** no está en la lista, seleccione **Todos los servicios** y escriba Azure SQL en el cuadro de búsqueda. (Opcional) Seleccione la estrella junto a **Azure SQL** para marcarlo como favorito y agréguelo como un elemento en el panel de navegación izquierdo. 
1. Seleccione la base de datos única que desea agregar al grupo de conmutación por error. 
1. Seleccione el nombre del servidor en **Nombre del servidor** para abrir la configuración del servidor.

   ![Abrir servidor para una base de datos única](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Seleccione **Grupos de conmutación por error** en el panel **Configuración** y, después, seleccione **Agregar grupo** para crear un nuevo grupo de conmutación por error. 

    ![Adición de un grupo de conmutación por error](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. En la página **Grupo de conmutación por error**, escriba o seleccione los valores necesarios y, después, seleccione **Crear**.

   - **Bases de datos en el grupo** Elija la base de datos que desea agregar a su grupo de conmutación por error. Al agregar la base de datos al grupo de conmutación por error, se iniciará automáticamente el proceso de replicación geográfica. 
        
    ![Adición de la base de datos SQL a un grupo de conmutación por error](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Cree el grupo de conmutación por error y agregue la base de datos única mediante PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>Conmutación por error de prueba 

Pruebe la conmutación por error de su grupo de conmutación por error mediante Azure Portal o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Pruebe la conmutación por error de su grupo de conmutación por error mediante Azure Portal. 

1. Seleccione **Azure SQL** en el menú izquierdo de [Azure Portal](https://portal.azure.com). Si **Azure SQL** no está en la lista, seleccione **Todos los servicios** y escriba Azure SQL en el cuadro de búsqueda. (Opcional) Seleccione la estrella junto a **Azure SQL** para marcarlo como favorito y agréguelo como un elemento en el panel de navegación izquierdo. 
1. Seleccione la base de datos única que desea agregar al grupo de conmutación por error. 

   ![Abrir servidor para una base de datos única](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Seleccione **Grupos de conmutación por error** en el panel **Configuración** y, a continuación, elija el grupo de conmutación por error que acaba de crear. 
  
   ![Seleccione el grupo de conmutación por error en el portal.](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Revise cuál es el servidor principal y cuál el secundario. 
1. Seleccione **Conmutación por error** en el panel de tareas para conmutar por error el grupo de conmutación por error que contiene la base de datos única. 
1. Seleccione **Sí** en la advertencia que le notifica que las sesiones de TDS se desconectarán. 

   ![Conmutación por error del grupo de conmutación por error que contiene la base de datos SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Revise qué servidor es ahora el principal y cuál el secundario. Si la conmutación por error se realiza correctamente, los dos servidores deben tener los roles intercambiados. 
1. Vuelva a seleccionar **Conmutación por error** para devolver los servidores a sus roles originales. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pruebe la conmutación por error de su grupo de conmutación por error mediante PowerShell.  

Compruebe el rol de la réplica secundaria: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Conmute por error el servidor secundario: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Revierta el grupo de conmutación por error al servidor principal:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

## <a name="elastic-pool"></a>Grupo elástico
Cree el grupo de conmutación por error y agregue un grupo elástico mediante Azure Portal o PowerShell.  

### <a name="prerequisites"></a>Requisitos previos

Tenga en cuenta los siguientes requisitos previos:

- La configuración del firewall y de inicio de sesión del servidor para el servidor secundario debe coincidir con la del servidor principal. 

### <a name="create-the-failover-group"></a>Creación del grupo de conmutación por error 

Creación del grupo de conmutación por error para su grupo elástico mediante Azure Portal o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Cree el grupo de conmutación por error y agregue el grupo elástico mediante Azure Portal.

1. Seleccione **Azure SQL** en el menú izquierdo de [Azure Portal](https://portal.azure.com). Si **Azure SQL** no está en la lista, seleccione **Todos los servicios** y escriba Azure SQL en el cuadro de búsqueda. (Opcional) Seleccione la estrella junto a **Azure SQL** para marcarlo como favorito y agréguelo como un elemento en el panel de navegación izquierdo. 
1. Seleccione el grupo elástico que desea agregar al grupo de conmutación por error. 
1. En el panel **Información general**, seleccione el nombre del servidor en **Nombre del servidor** para abrir la configuración del servidor.
  
    ![Abra el servidor para el grupo elástico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Seleccione **Grupos de conmutación por error** en el panel **Configuración** y, después, seleccione **Agregar grupo** para crear un nuevo grupo de conmutación por error. 

    ![Adición de un grupo de conmutación por error](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. En la página **Grupo de conmutación por error**, escriba o seleccione los valores necesarios y, después, seleccione **Crear**. Cree un nuevo servidor secundario o seleccione un servidor secundario existente. 

1. Seleccione **Bases de datos en el grupo** y, a continuación, elija el grupo elástico que desea agregar al grupo de conmutación por error. Si aún no existe un grupo elástico en el servidor secundario, aparecerá una advertencia en la que se le solicita que cree un grupo elástico en el servidor secundario. Seleccione la advertencia y, a continuación, elija **Aceptar** para crear el grupo elástico en el servidor secundario. 
        
    ![Agregar un grupo elástico a un grupo de conmutación por error](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Elija **Seleccionar** para aplicar la configuración del grupo elástico al grupo de conmutación por error y, después, seleccione **Crear** para crear el grupo de conmutación por error. Al agregar el grupo elástico al grupo de conmutación por error, se iniciará automáticamente el proceso de replicación geográfica. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Cree el grupo de conmutación por error y agregue el grupo elástico mediante PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>Conmutación por error de prueba

Pruebe la conmutación por error de su grupo elástico mediante Azure Portal o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Se producirá un error en el grupo de conmutación por error en el servidor secundario y, a continuación, se realizará la conmutación por recuperación mediante Azure Portal. 

1. Seleccione **Azure SQL** en el menú izquierdo de [Azure Portal](https://portal.azure.com). Si **Azure SQL** no está en la lista, seleccione **Todos los servicios** y escriba Azure SQL en el cuadro de búsqueda. (Opcional) Seleccione la estrella junto a **Azure SQL** para marcarlo como favorito y agréguelo como un elemento en el panel de navegación izquierdo. 
1. Seleccione el grupo elástico que desea agregar al grupo de conmutación por error. 
1. En el panel **Información general**, seleccione el nombre del servidor en **Nombre del servidor** para abrir la configuración del servidor.
  
    ![Abra el servidor para el grupo elástico](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. Seleccione **Grupos de conmutación por error** en el panel **Configuración** y, a continuación, elija el grupo de conmutación por error que ha creado en la sección 2. 
  
   ![Seleccione el grupo de conmutación por error en el portal.](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Revise cuál es el servidor principal y cuál es el secundario. 
1. Seleccione **Conmutación por error** en el panel de tareas para conmutar por error el grupo de conmutación por error que contiene el grupo elástico. 
1. Seleccione **Sí** en la advertencia que le notifica que las sesiones de TDS se desconectarán. 

   ![Conmutación por error del grupo de conmutación por error que contiene la base de datos SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Revise cuál es el servidor principal y cuál es el secundario. Si la conmutación por error se realiza correctamente, los dos servidores deben tener los roles intercambiados. 
1. Seleccionar de nuevo **Conmutación por error** para que el grupo de conmutación por error vuelva a la configuración original. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pruebe la conmutación por error de su grupo de conmutación por error mediante PowerShell.

Compruebe el rol de la réplica secundaria: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Conmute por error el servidor secundario: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

## <a name="managed-instance"></a>Instancia administrada

Cree un grupo de conmutación por error entre dos instancias administradas mediante Azure Portal o PowerShell. 

Tendrá que crear una puerta de enlace para la red virtual de cada instancia administrada, conecte las dos puertas de enlace y, a continuación, cree el grupo de conmutación por error.

### <a name="prerequisites"></a>Requisitos previos
Tenga en cuenta los siguientes requisitos previos:

- La instancia administrada secundaria debe estar vacía.
- El intervalo de subred para la red virtual secundaria no debe superponerse al intervalo de subred de la red virtual principal. 
- La intercalación y zona horaria de la instancia secundaria deben coincidir con las de la instancia principal. 
- Al conectar las dos puertas de enlace, la **clave compartida** debe ser la misma para ambas conexiones. 

### <a name="create-primary-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual principal 

Cree la puerta de enlace de red virtual principal con Azure Portal o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Cree la puerta de enlace de red virtual principal mediante Azure Portal. 

1. En [Azure Portal](https://portal.azure.com), navegue al grupo de recursos y seleccione el recurso **Red virtual** para la instancia administrada principal. 
1. Seleccione **Subredes** en **Configuración** y, a continuación, seleccione para agregar una nueva **Subred de puerta de enlace**. Deje los valores predeterminados. 

   ![Adición de una puerta de enlace para la instancia administrada principal](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Una vez creada la puerta de enlace de subred, seleccione **Crear un recurso** en el panel de navegación izquierdo y, a continuación, escriba `Virtual network gateway` en el cuadro de búsqueda. Seleccione el recurso **Puerta de enlace de red virtual** publicado por **Microsoft**. 

   ![Creación de una nueva puerta de enlace de red virtual](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Rellene los campos obligatorios para configurar la puerta de enlace de la instancia administrada principal. 

   En la tabla siguiente se muestran los valores necesarios para la puerta de enlace de la instancia administrada principal:
 
    | **Campo** | Valor |
    | --- | --- |
    | **Suscripción** |  Suscripción en la que reside la instancia administrada principal. |
    | **Nombre** | Nombre de la puerta de enlace de red virtual. | 
    | **Región** | Región donde se encuentra la instancia administrada secundaria. |
    | **Tipo de puerta de enlace** | Seleccione **VPN**. |
    | **Tipo de VPN** | Seleccione **Basada en rutas**. |
    | **SKU**| Deje el valor predeterminado de `VpnGw1`. |
    | **Ubicación**| Ubicación donde se encuentran la instancia administrada secundaria y la red virtual secundaria.   |
    | **Red virtual**| Seleccione la red virtual para su instancia administrada secundaria. |
    | **Dirección IP pública**| Seleccione **Crear nuevo**. |
    | **Nombre de la dirección IP pública**| Escriba un nombre para la dirección IP. |
    | &nbsp; | &nbsp; |

1. Deje los demás valores predeterminados y, a continuación, seleccione **Revisar y crear** para revisar la configuración de la puerta de enlace de red virtual.

   ![Configuración de la puerta de enlace principal](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Seleccione **Crear** para crear la nueva puerta de enlace de red virtual. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Cree la puerta de enlace de red virtual principal mediante PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Creación de una puerta de enlace de red virtual secundaria

Cree la puerta de enlace de red virtual secundaria mediante Azure Portal o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Repita los pasos de la sección anterior para crear la subred y la puerta de enlace de la red virtual para la instancia administrada secundaria. Rellene los campos obligatorios para configurar la puerta de enlace para la instancia administrada secundaria. 

   En la tabla siguiente se muestran los valores necesarios para la puerta de enlace de la instancia administrada secundaria:

   | **Campo** | Valor |
   | --- | --- |
   | **Suscripción** |  Suscripción en la que reside la instancia administrada secundaria. |
   | **Nombre** | Nombre de la puerta de enlace de red virtual, como `secondary-mi-gateway`. | 
   | **Región** | Región donde se encuentra la instancia administrada secundaria. |
   | **Tipo de puerta de enlace** | Seleccione **VPN**. |
   | **Tipo de VPN** | Seleccione **Basada en rutas**. |
   | **SKU**| Deje el valor predeterminado de `VpnGw1`. |
   | **Ubicación**| Ubicación donde se encuentran la instancia administrada secundaria y la red virtual secundaria.   |
   | **Red virtual**| Seleccione la red virtual que se creó en la sección 2, como `vnet-sql-mi-secondary`. |
   | **Dirección IP pública**| Seleccione **Crear nuevo**. |
   | **Nombre de la dirección IP pública**| Escriba un nombre para la dirección IP, como `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Configuración de la puerta de enlace secundaria](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Cree la puerta de enlace de red virtual secundaria mediante PowerShell. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>Conexión de las puertas de enlace 
Cree conexiones entre las dos puertas de enlace mediante Azure Portal o PowerShell. 

Deben crearse dos conexiones: la conexión de la puerta de enlace principal a la puerta de enlace secundaria y, a continuación, la conexión de la puerta de enlace secundaria a la puerta de enlace principal. 

La clave compartida usada para ambas conexiones debe ser la misma para cada conexión. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Cree conexiones entre las dos puertas de enlace mediante Azure Portal. 

1. Seleccione **Crear un recurso** en [Azure Portal](https://portal.azure.com).
1. Escriba `connection` en el cuadro de búsqueda y, después, presione Entrar para buscar. Esto lo llevará al recurso **Conexión**, publicado por Microsoft.
1. Seleccione **Crear** para crear su conexión. 
1. En la pestaña **Aspectos básicos**, seleccione los siguientes valores y luego seleccione **Aceptar**. 
    1. Seleccione `VNet-to-VNet` para el **tipo de conexión**. 
    1. Seleccione la suscripción en la lista desplegable. 
    1. Seleccione el grupo de recursos de la instancia administrada en la lista desplegable. 
    1. Seleccione la ubicación de la instancia administrada principal en la lista desplegable. 
1. En la pestaña **Configuración**, seleccione o escriba los valores siguientes y, después, seleccione **Aceptar**:
    1. Elija la puerta de enlace de red principal para la **Primera puerta enlace de red virtual**, como `Primary-Gateway`.  
    1. Elija la puerta de enlace de red secundaria para la **Segunda puerta enlace de red virtual**, como `Secondary-Gateway`. 
    1. Active la casilla situada junto a **Establecer conectividad bidireccional**. 
    1. Deje el nombre de la conexión principal predeterminado o cambie su nombre por un valor de su elección. 
    1. Proporcione una **clave compartida (PSK)** para la conexión, como `mi1m2psk`. 

   ![Crear una conexión de la puerta de enlace](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. En la pestaña **Resumen**, revise la configuración de la conexión bidireccional y, después, seleccione **Aceptar** para crear la conexión. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Cree conexiones entre las dos puertas de enlace mediante PowerShell. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Creación del grupo de conmutación por error 
Cree el grupo de conmutación por error para sus instancias administradas mediante Azure Portal o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Cree el grupo de conmutación por error para sus instancias administradas mediante Azure Portal. 

1. Seleccione **Azure SQL** en el menú izquierdo de [Azure Portal](https://portal.azure.com). Si **Azure SQL** no está en la lista, seleccione **Todos los servicios** y escriba Azure SQL en el cuadro de búsqueda. (Opcional) Seleccione la estrella junto a **Azure SQL** para marcarlo como favorito y agréguelo como un elemento en el panel de navegación izquierdo. 
1. Seleccione la instancia administrada principal que desea agregar al grupo de conmutación por error.  
1. En **Configuración**, navegue a **Grupos de conmutación por error de instancias** y, después, elija **Agregar grupo** para abrir la página **Grupo de conmutación por error de instancias**. 

   ![Adición de un grupo de conmutación por error](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. En la página **Grupo de conmutación por error de instancias**, escriba el nombre del grupo de conmutación por error y después elija la instancia administrada secundaria en la lista desplegable. Seleccione **Crear** para crear el grupo de conmutación por error. 

   ![Creación de un grupo de conmutación por error](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Una vez completada la implementación del grupo de conmutación por error, volverá a mostrarse la página **Grupo de conmutación por error**. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Cree el grupo de conmutación por error para sus instancias administradas mediante PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>Conmutación por error de prueba

Pruebe la conmutación por error de su grupo de conmutación por error mediante Azure Portal o PowerShell. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Pruebe la conmutación por error de su grupo de conmutación por error mediante Azure Portal. 

1. Navegue a la instancia administrada en [Azure Portal](https://portal.azure.com) y seleccione **Grupos de conmutación por error de instancias** en las opciones de configuración. 
1. Revise cuál instancia administrada es la principal y cuál es la secundaria. 
1. Seleccione **Conmutación por error** y, a continuación, seleccione **Sí** en la advertencia acerca de la desconexión de las sesiones TDS. 

   ![Conmutación por error del grupo de conmutación por error](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Revise cuál instancia administrada es la principal y cuál la secundaria. Si la conmutación por error se realiza correctamente, las dos instancias deben tener los roles cambiados. 

   ![Las instancias administradas cambian roles después de la conmutación por error](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Vuelva a seleccionar **Conmutación por error** para que la instancia principal vuelva a tener el rol principal. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pruebe la conmutación por error de su grupo de conmutación por error mediante PowerShell. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>Búsqueda del punto de conexión del cliente de escucha

Una vez que se configure su grupo de conmutación por error, actualice la cadena de conexión para su aplicación al punto de conexión del cliente de escucha. Esto mantendrá su aplicación conectada al cliente de escucha de grupo de conmutación por error, en lugar de la base de datos principal, el grupo elástico o la instancia administrada. De esa forma, no tendrá que actualizar manualmente la cadena de conexión cada vez que su entidad de base de datos de Azure SQL se conmute por error y el tráfico se enrute a la entidad que actualmente sea la principal. 

El punto de conexión del cliente de escucha tiene el formato `fog-name.database.windows.net` y es visible en Azure Portal, al ver el grupo de conmutación por error:

![Cadena de conexión del grupo de conmutación por error](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Comentarios

- Quitar un grupo de conmutación por error para una base de datos única o agrupada no detiene la replicación ni elimina la base de datos replicada. Tendrá que detener manualmente la replicación geográfica y eliminar la base de datos del servidor secundario si desea volver a agregar una base de datos única o agrupada a un grupo de conmutación por error una vez que se haya quitado. Si no hace nada de esto, es posible que se produzca un error similar a `The operation cannot be performed due to multiple errors` al intentar agregar la base de datos al grupo de conmutación por error. 


## <a name="next-steps"></a>Pasos siguientes

Para ver pasos detallados que configuren un grupo de conmutación por error, consulte los siguientes tutoriales:
- [Adición de una base de datos única a un grupo de conmutación por error](sql-database-single-database-failover-group-tutorial.md)
- [Adición de un grupo elástico a un grupo de conmutación por error](sql-database-elastic-pool-failover-group-tutorial.md)
- [Adición de instancias administradas a un grupo de conmutación por error](sql-database-managed-instance-failover-group-tutorial.md)
 
Para ver información general de las opciones de alta disponibilidad de Azure SQL Database, consulte la [replicación geográfica](sql-database-active-geo-replication.md) y [grupos de conmutación por error automática](sql-database-auto-failover-group.md). 
