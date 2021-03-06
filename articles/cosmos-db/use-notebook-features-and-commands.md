---
title: Usar los comandos y las características de los cuadernos integrados en Azure Cosmos DB
description: Aprenda a usar los comandos y las características integradas para realizar operaciones comunes con los cuadernos integrados de Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dech
ms.openlocfilehash: 4a9bd554e0858024d656dbf35d6fb00995e6f4bd
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672486"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db"></a>Usar los comandos y las características de los cuadernos integrados en Azure Cosmos DB

Los cuadernos de Jupyter integrados en Azure Cosmos DB permiten analizar y visualizar los datos desde Azure Portal. En este artículo se describe cómo usar los comandos y características integrados del cuaderno para realizar operaciones comunes.

## <a name="install-a-new-package"></a>Instalar un nuevo paquete
Después de habilitar la compatibilidad con el cuaderno en sus cuentas de Azure Cosmos, puede abrir un nuevo cuaderno e instalar un paquete.

En una nueva celda de código, inserte y ejecute el código siguiente, reemplazando ``PackageToBeInstalled`` con el paquete de Python que quiera usar.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled –user
```
Este paquete estará disponible en cualquier cuaderno del área de trabajo de la cuenta de Azure Cosmos. 

> [!TIP]
> Si el cuaderno requiere un paquete personalizado, es recomendable que agregue una celda en el cuaderno para instalar el paquete, ya que los paquetes se eliminan si [restablece el área de trabajo](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Ejecutar una consulta SQL

Puede usar el comando mágico ``%%sql`` para ejecutar una [consulta de SQL](sql-query-getting-started.md) en cualquier contenedor de su cuenta. La sintaxis es la siguiente:

```bash
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Reemplace ``{database_id}`` y ``{container_id}`` por el nombre de la base de datos y el contenedor en la cuenta de Cosmos. Si no se proporcionan los argumentos ``--database`` y ``--container``, la consulta se ejecutará en la [base de datos y en el contenedor predeterminados](#set-default-database-for-queries).
- Puede ejecutar cualquier consulta SQL que sea válida en Azure Cosmos DB. El texto de la consulta debe estar en una línea nueva.

Por ejemplo: 
```bash
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Ejecute ```%%sql?``` en una celda para ver la documentación de ayuda del comando mágico de SQL en el cuaderno.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Ejecutar una consulta SQL y devolver resultados en un DataFrame de Pandas

Puede acceder a los resultados de una consulta ``%%sql`` en [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/getting_started/dsintro.html#dataframe). La sintaxis es la siguiente: 

```bash
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Reemplace ``{database_id}`` y ``{container_id}`` por el nombre de la base de datos y el contenedor en la cuenta de Cosmos. Si no se proporcionan los argumentos ``--database`` y ``--container``, la consulta se ejecutará en la [base de datos y en el contenedor predeterminados](#set-default-database-for-queries).
- Reemplace ``{outputDataFrameVar}`` por el nombre de la variable de DataFrame que contendrá los resultados.
- Puede ejecutar cualquier consulta SQL que sea válida en Azure Cosmos DB. El texto de la consulta debe estar en una línea nueva. 

Por ejemplo:

```bash
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```bash
df_cosmos.head(10)

    Action  ItemRevenue Country Item
0   Viewed  9.00    Tunisia Black Tee
1   Viewed  19.99   Antigua and Barbuda Flannel Shirt
2   Added   3.75    Guinea-Bissau   Socks
3   Viewed  3.75    Guinea-Bissau   Socks
4   Viewed  55.00   Czech Republic  Rainjacket
5   Viewed  350.00  Iceland Cosmos T-shirt
6   Added   19.99   Syrian Arab Republic    Button-Up Shirt
7   Viewed  19.99   Syrian Arab Republic    Button-Up Shirt
8   Viewed  33.00   Tuvalu  Red Top
9   Viewed  14.00   Cape Verde  Flip Flop Shoes
```

## <a name="set-default-database-for-queries"></a>Establecer la base de datos predeterminada para las consultas
Puede establecer los comandos ```%%sql``` de la base de datos predeterminada que se usarán para el cuaderno. Reemplace ```{database_id}``` por el nombre de la base de datos.

```bash
%database {database_id}
```
Ejecute ```%database?``` en una celda para ver la documentación en el cuaderno.

## <a name="set-default-container-for-queries"></a>Establecer el contenedor predeterminado para las consultas
Puede establecer los comandos ```%%sql``` del contenedor predeterminado que se usarán para el cuaderno. Reemplace ```{container_id}``` por el nombre del contenedor.

```bash
%container {container_id}
```
Ejecute ```%container?``` en una celda para ver la documentación en el cuaderno.

## <a name="use-the-built-in-python-sdk"></a>Uso del SDK de Python integrado
La versión 4 del [SDK de Python de Azure Cosmos DB para la API de SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) está instalada e incluida en el entorno del cuaderno para la cuenta de Cosmos.

Use la instancia integrada ``cosmos_client`` para ejecutar cualquier operación de SDK. 

Por ejemplo:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
consulte los [ejemplos de SDK de Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples). 

> [!IMPORTANT]
> El SDK de Python integrado solo se admite para las cuentas de la API de SQL (Core). Para otras API, deberá [instalar el controlador de Python correspondiente](#install-a-new-package) a la API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Creación de una instancia personalizada de ``cosmos_client``
Para lograr una mayor flexibilidad, puede crear una instancia personalizada de ``cosmos_client`` con el fin de:

- Personalizar la [directiva de conexión](https://docs.microsoft.com/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?view=azure-python-preview).
- Ejecutar operaciones en una cuenta de Cosmos diferente de la que se está usando.

Puede obtener acceso a la cadena de conexión y a la clave principal de la cuenta actual a través de las [variables de entorno](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import os
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(os.environ["COSMOS_ENDPOINT"], {'masterKey': os.environ["COSMOS_KEY"]}, connection_policy=custom_connection_policy)
```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Acceder al punto de conexión de la cuenta y a las variables de tipo "env" de la clave principal
```python
import os

endpoint = os.environ["COSMOS_ENDPOINT"]
primary_key = os.environ["COSMOS_KEY"]
```
> [!IMPORTANT]
> Las variables de entorno ``COSMOS_ENDPOINT`` y ``COSMOS_KEY`` solo se pueden aplicar a la API de SQL. En cuanto a otras API, busque el punto de conexión y la clave en la hoja de las **cadenas de conexión** o las **claves** en su cuenta de Cosmos.  

## <a name="reset-notebooks-workspace"></a>Restablecer el área de trabajo de los cuadernos
Para restablecer la configuración predeterminada del área de trabajo de los cuadernos, seleccione **Reset Workspace** (Restablecer el área de trabajo) en la barra de comandos. Se quitarán todos los paquetes instalados personalizados y se reiniciará el servidor de Jupyter. Los cuadernos, los archivos y los recursos de Cosmos no se verán afectados.  

![Restablecer el área de trabajo de los cuadernos](media/use-notebook-features-and-commands/reset-workspace.png)

## <a name="next-steps"></a>Pasos siguientes

- Conozca las ventajas de los [cuadernos de Jupyter para Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
- Obtenga información acerca del [SDK de Python para Azure Cosmos DB para la API de SQL](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)
