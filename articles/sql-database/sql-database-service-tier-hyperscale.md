---
title: Introducción a Hiperescala
description: En este artículo se describe el nivel de servicio Hiperescala en el modelo de compra basado en núcleo virtual en Azure SQL Database, y se explica la diferencia entre los niveles de servicio Uso general y Crítico para la empresa.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: c71fb8a7e18439817023874146e22c29a5af3b12
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123692"
---
# <a name="hyperscale-service-tier"></a>Nivel de servicio Hiperescala

Azure SQL Database se basa en la arquitectura del motor de base de datos de SQL Server que se ajusta al entorno en la nube, con el fin de garantizar una disponibilidad del 99,99 % incluso en los casos de error de la infraestructura. Hay tres modelos de arquitectura que se usan en Azure SQL Database:
- De uso general/Estándar 
-  Hiperescala
-  Crítico para la empresa/Premium

El nivel de servicio Hiperescala de Azure SQL Database es el nivel de servicio más reciente en el modelo de compra basado en núcleo virtual. Este nivel de servicio es un nivel altamente escalable de almacenamiento y de rendimiento de proceso que aprovecha la arquitectura de Azure para escalar horizontalmente el almacenamiento y los recursos de proceso para una base de datos de Azure SQL considerablemente más allá de los límites disponibles para los niveles de servicio Uso general y Crítico para la empresa.

> 
> [!NOTE]
> Para más información acerca de los niveles de servicios Uso general y Crítico para la empresa en el modelo de compra basado en núcleo virtual, consulte los niveles de servicio [Uso general](sql-database-service-tier-general-purpose.md) y [Crítico para la empresa](sql-database-service-tier-business-critical.md). Para ver una comparación entre el modelo de compra basado en núcleo virtual y el modelo de compra basado en DTU, consulte [Modelos de compra y recursos de Azure SQL Database](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>¿Cuáles son las funcionalidades de Hiperescala?

El nivel de servicio Hiperescala en Azure SQL Database proporciona las siguientes funcionalidades adicionales:

- Compatibilidad con bases de datos con un tamaño de hasta 100 TB
- Copias de seguridad de base de datos casi instantáneas (basadas en las instantáneas almacenadas en Azure Blob Storage) independientemente del tamaño sin efecto de la E/S en recursos de proceso  
- Restauraciones rápidas de base de datos a un momento dado (basadas en instantáneas de archivos) en minutos en lugar de horas o días (no el tamaño de la operación de datos)
- Mayor rendimiento general debido a un mayor rendimiento de los registros y tiempos más rápidos de confirmación de las transacciones, independientemente de los volúmenes de datos
- Rápido escalado horizontal: puede aprovisionar uno o varios de solo lectura nodos para la descarga de la carga de trabajo de lectura y para su uso como esperas activas
- Rápido escalado vertical: en tiempo constante, puede escalar verticalmente los recursos de proceso para dar cabida a cargas de trabajo pesadas como y cuando sea necesario y, a continuación, reducir verticalmente los recursos de proceso cuando no sean necesarios.

El nivel de servicio Hiperescala elimina muchos de los límites prácticos que tradicionalmente se ven en las bases de datos en la nube. Donde la mayoría de las otras bases de datos están limitados por los recursos disponibles en un único nodo, las bases de datos en el nivel de servicio Hiperescala no tienen límites de este tipo. Con su arquitectura de almacenamiento flexible, el almacenamiento crece a medida que sea necesario. De hecho, las bases de datos de hiperescala no se crean con un tamaño máximo definido. Una base de datos de hiperescala aumenta según sea necesario, y se le cobrará solo la capacidad que use. Para cargas de trabajo de lectura intensiva, el nivel de servicio Hiperescala proporciona rápida escalabilidad horizontal mediante el aprovisionamiento de réplicas de lectura adicionales según sea necesario para descargar las cargas de trabajo de lectura.

Además, el tiempo necesario para crear copias de seguridad de bases de datos o para escalar o reducir verticalmente ya no está ligado al volumen de los datos en la base de datos. Pueden crearse copias de seguridad de las bases de datos de hiperescala de manera prácticamente instantánea. También puede escalar o reducir verticalmente una base de datos de decenas de terabytes en cuestión de minutos. Esta funcionalidad le libra de la preocupación de estar atado por las opciones de la configuración inicial.

Para más información sobre los tamaños de proceso para el nivel de servicio Hiperescala, consulte [Características de los niveles de servicios](sql-database-service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quién debe tener en cuenta el nivel de servicio Hiperescala

El nivel de servicio Hiperescala está pensado para la mayoría de las cargas de trabajo empresariales, ya que proporciona una gran flexibilidad y un alto rendimiento con recursos de proceso y almacenamiento escalables de forma independiente. Con la capacidad de almacenamiento con escalado automático hasta 100 TB, es una excelente opción para los clientes que:

- Tienen bases de datos de gran tamaño en el entorno local y desean modernizar sus aplicaciones pasándose a la nube
- Ya están en la nube y están limitados por las restricciones de tamaño máximo de base de datos de otros niveles de servicio (1-4 TB)
- Tienen bases de datos más pequeñas, pero requieren un escalado de proceso vertical y horizontal rápido, alto rendimiento, copia de seguridad instantánea y una rápida restauración de bases de datos.

El nivel de servicio Hiperescala admite una amplia gama de cargas de trabajo de SQL Server, desde OLTP puro hasta análisis puro, pero está optimizado principalmente para cargas de trabajo OLTP y de procesamiento híbrido transaccional y analítico (HTAP).

> [!IMPORTANT]
> Los grupos elásticos no admiten el nivel de servicio Hiperescala.

## <a name="hyperscale-pricing-model"></a>Modelo de precios de Hiperescala

El nivel de servicio Hiperescala solo está disponible en el [modelo de núcleo virtual](sql-database-service-tiers-vcore.md). Para alinearse con la nueva arquitectura, el modelo de precios es ligeramente diferente de los niveles de servicio Se uso general o Crítico para la empresa:

- **Proceso**:

  El precio de la unidad de proceso de Hiperescala es por réplica. El precio de la [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) se aplica automáticamente a las réplicas de escalado de lectura. De manera predeterminada, creamos una réplica principal y una réplica de solo lectura por base de datos Hiperescala.  Los usuarios pueden ajustar el número total de réplicas, incluida la principal, de 1 a 5.

- **Almacenamiento**:

  No es necesario especificar el tamaño máximo de datos al configurar una base de datos Hiperescala. En el nivel Hiperescala, se le cobra por el almacenamiento de su base de datos según el uso real. El almacenamiento se asigna automáticamente entre 10 GB y 100 TB, en incrementos que se ajustan dinámicamente entre 10 GB y 40 GB.  

Para más información sobre los precios de Hiperescala, consulte [Precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitectura de funciones distribuidas

A diferencia de los motores de base de datos tradicionales, que han centralizada todas las funciones de administración de datos en una ubicación o proceso (incluso las llamadas bases de datos distribuidas en producción actualmente tienen varias copias de un motor de datos monolítico), una base de datos de hiperescala separa el motor de procesamiento de consultas, donde la semántica de los diversos motores de datos difieren, de los componentes que proporcionan almacenamiento a largo plazo y durabilidad de los datos. De este modo, la capacidad de almacenamiento se puede escalar horizontalmente fácilmente en cuanto sea necesario (el destino inicial es de 100 TB). Las réplicas de solo lectura comparten los mismos componentes de almacenamiento, por lo que no se requiere ninguna copia de datos para poner en marcha una nueva réplica legible. 

El siguiente diagrama ilustra los diferentes tipos de nodos en una base de datos de hiperescala:

![arquitectura](./media/sql-database-hyperscale/hyperscale-architecture2.png)

Una base de datos de Hiperescala contiene los distintos tipos de componentes siguientes:

### <a name="compute"></a>Proceso

El nodo de ejecución es donde reside el motor relacional, donde ocurren todos los elementos de lenguaje, el procesamiento de consultas, etc. Todas las interacciones de usuario con una base de datos de hiperescala se producen a través de estos nodos de ejecución. Los nodos de ejecución tienen memorias caché basadas en SSD (con la etiqueta RBPEX, extensión del grupo de búferes resistentes, en el diagrama anterior) para minimizar el número de recorridos de ida y vuelta de red necesarios para capturar una página de datos. Hay un nodo de ejecución principal donde se procesan todas las transacciones y las cargas de trabajo de lectura y escritura. Hay uno o varios nodos de ejecución secundarios que actúan como nodos en espera activa para fines de conmutación por error, además de actuar como nodos de ejecución de solo lectura para la descarga de cargas de trabajo de lectura (si se desea esta funcionalidad).

### <a name="page-server"></a>Servidor de páginas

Los servidores de páginas son sistemas que representan un motor de almacenamiento escalado horizontalmente.  Cada servidor de páginas es responsable de un subconjunto de las páginas en la base de datos.  Nominalmente, cada servidor de páginas controla entre 128 GB y 1 TB de datos. No se comparte ningún dato en más de un servidor de páginas (fuera de las réplicas que se mantienen para ofrecer redundancia y disponibilidad). El trabajo de un servidor de páginas es servir las páginas de la base de datos a los nodos de ejecución a petición, y conservar las páginas actualizadas a medida que las transacciones actualizan los datos. Los servidores de páginas se mantienen actualizados mediante la reproducción de entradas del registro del servicio de registro. Los servidores de páginas también mantienen memorias caché basadas en SSD para mejorar el rendimiento. El almacenamiento a largo plazo de las páginas de datos se mantiene en Azure Storage para aumentar la confiabilidad.

### <a name="log-service"></a>Servicio de registros

El servicio de registros acepta entradas de registro desde la réplica de proceso principal, las conserva en una memoria caché duradera y reenvía las entradas del registro al resto de las réplicas de proceso (para que puedan actualizar sus memorias caché), así como los servidores de páginas pertinentes, para que los datos puedan actualizarse allí. De este modo, todos los cambios en los datos desde la réplica de proceso principal se propagan mediante el servicio de registros a todas las réplicas de proceso secundarias y los servidores de página. Por último, las entradas de registro se insertan en el almacenamiento a largo plazo en Azure Storage, que es un repositorio de almacenamiento casi infinito. Este mecanismo elimina la necesidad de truncamiento frecuente del registro. El servicio de registros también tiene memoria caché local para acelerar el acceso a las entradas de registro.

### <a name="azure-storage"></a>Almacenamiento de Azure

Azure Storage contiene todos los archivos de datos de una base de datos. Los servidores de página mantienen los archivos de datos en Azure Storage actualizados. Este almacenamiento se usa con fines de copia de seguridad, así como para la replicación entre regiones de Azure. Las copias de seguridad se implementan mediante instantáneas de almacenamiento de archivos de datos. Las operaciones de restauración mediante instantáneas son rápidas independientemente del tamaño de los datos. Los datos se pueden restaurar a cualquier punto en el tiempo dentro del período de retención de copia de seguridad de la base de datos.

## <a name="backup-and-restore"></a>Copia de seguridad y restauración

Las copias de seguridad están basadas en instantáneas de archivos y, por tanto, se realizan de forma prácticamente instantánea. La separación del almacenamiento y los procesos permite insertar la operación de copia de seguridad y restauración en la capa de almacenamiento para reducir la carga de procesamiento en replica de proceso principal. Como resultado, la copia de seguridad de base de datos no afecta al rendimiento del nodo de proceso principal. del mismo modo, las restauraciones se realizan revirtiendo a instantáneas de archivos y, por tanto, no tienen el tamaño de una operación de datos. La restauración es una operación de tiempo constante, e incluso las bases de datos de varios terabytes se pueden restaurar en minutos en lugar de horas o días. La creación de bases de datos mediante la restauración de una copia de seguridad existente también aprovecha esta característica: la creación de copias de bases de datos en el mismo servidor lógico con fines de desarrollo o para realizar pruebas, incluso cuando se trata de bases de datos con un tamaño de terabytes, se puede hacer en cuestión de minutos.

## <a name="scale-and-performance-advantages"></a>Ventajas de escala y rendimiento

Con la capacidad de aumentar o disminuir rápidamente los nodos de ejecución adicionales de solo lectura, la arquitectura de Hiperescala permite obtener importantes funcionalidades de escalado de lectura y también puede liberar el nodo de ejecución principal para atender más solicitudes de escritura. Además, los nodos de ejecución se pueden escalar o reducir verticalmente rápidamente debido a la arquitectura de almacenamiento compartido de la arquitectura de Hiperescala.

## <a name="create-a-hyperscale-database"></a>¿Qué es una base de datos de Hiperescala?

Puede crearse una base de datos de Hiperescala mediante [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) o la [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Las bases de datos de Hiperescala solo están disponibles con el [modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md).

El siguiente comando de Transact-SQL crea una base de datos de Hiperescala. Debe especificar tanto la edición como el servicio objetivo en la instrucción `CREATE DATABASE`. Consulte los [límites de recursos](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale---provisioned-compute---gen5) para obtener una lista de los objetivos de servicio válidos.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Esto creará una base de datos Hiperescala en el hardware de Gen5 con 4 núcleos.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migración de una base de datos de Azure SQL existente al nivel de servicio Hiperescala

Puede mover las bases de datos de Azure SQL existentes a Hiperescala con [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) o la [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Actualmente, esta migración es unidireccional. No se pueden trasladar bases de datos de Hiperescala a otro nivel de servicio, excepto mediante la exportación e importación de datos. Para las pruebas de concepto (POC), se recomienda que haga una copia de las bases de datos de producción y la migre a Hiperescala. La migración de una base de datos de Azure SQL existente al nivel Hiperescala tiene el tamaño de una operación de datos.

El siguiente comando de T-SQL traslada una base de datos al nivel de servicio Hiperescala. Debe especificar tanto la edición como el servicio objetivo en la instrucción `ALTER DATABASE`.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Conexión a una réplica de escalado de lectura de una base de datos de Hiperescala

En las bases de datos de Hiperescala, el argumento `ApplicationIntent` de la cadena de conexión proporcionado por el cliente dictamina si la conexión se enruta a la réplica de escritura o a una réplica de solo lectura secundaria. Si el argumento `ApplicationIntent` establecido en `READONLY` y la base de datos no tienen una réplica secundaria, la conexión se enrutará a la réplica principal y de forma predeterminada es el comportamiento `ReadWrite`.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Las réplicas secundarias de Hiperescala son todas idénticas, y utilizan el mismo objetivo de nivel de servicio que la réplica principal. Si hay más de una réplica secundaria, la carga de trabajo se distribuye entre todas las replicas secundarias disponibles. Cada réplica secundaria se actualiza de forma independiente, por lo que las distintas réplicas podrían tener una latencia de datos diferente en relación con la réplica principal.

## <a name="database-high-availability-in-hyperscale"></a>Alta disponibilidad de la base de datos en Hiperscala

Como en todos los demás niveles de servicio, Hiperescala garantiza la durabilidad de los datos para las transacciones confirmadas, independientemente de la disponibilidad de la réplica de proceso. El grado de tiempo de inactividad debido a que la réplica principal no esté disponible, depende del tipo de conmutación por error (planeada frente a no planeada) y de la presencia de al menos una réplica secundaria. En una conmutación por error planeada (es decir, un evento de mantenimiento), el sistema crea la nueva réplica principal antes de iniciar la conmutación por error, o bien usa una réplica secundaria existente como destino de la conmutación por error. En una conmutación por error no planeada (es decir, un error de hardware en la réplica principal), el sistema usa una réplica secundaria como destino de la conmutación por error, si existe, o crea una nueva réplica principal a partir del grupo de capacidad de proceso disponible. En el último caso, la duración del tiempo de inactividad es mayor debido a pasos adicionales necesarios para crear la nueva réplica principal.

Para el contrato de nivel de servicio de Hiperescala, consulte [Contrato de nivel de servicio para Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Recuperación ante desastres para bases de datos Hiperescala

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Restaurar una base de datos Hiperescala en una ubicación geográfica diferente
Si necesita restaurar una base de datos Hiperescala de Azure SQL Database en una región distinta a donde se hospeda actualmente —como parte de una operación de recuperación ante desastres, una exploración en profundidad, una reubicación o cualquier otro motivo—, el método principal es realizar una restauración geográfica de la base de datos.  Esto implica exactamente los mismos pasos que seguiría para restaurar cualquier otra base de datos de Azure SQL en una región diferente:
1. Cree un servidor de SQL Database en la región de destino si ahí todavía no tiene un servidor adecuado.  Este servidor debe pertenecer a la misma suscripción que el servidor original (origen).
2. Siga las instrucciones del tema [Restauración geográfica](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) de la página dedicada a la restauración de bases de datos de Azure SQL a partir de copias de seguridad automáticas.

> [!NOTE]
> Dado que el origen y el destino están en regiones distintas, la base de datos no puede compartir el almacenamiento de instantáneas con la base de datos de origen como en las restauraciones no geográficas, que se completan muy rápidamente.  En el caso de una restauración geográfica de una base de datos Hiperescala, será una operación de tamaño de datos, incluso si el destino está en la región emparejada del almacenamiento con replicación geográfica.  Esto significa que la duración de una restauración geográfica será proporcional al tamaño de la base de datos que se está restaurando.  Si el destino se encuentra en la región emparejada, la copia estará dentro de un centro de datos, que será mucho más rápido que una copia de larga distancia a través de Internet, aunque de todos modos se copiarán todos los bits.

## <a name=regions></a>Regiones disponibles

El nivel Hiperescala de Azure SQL Database está disponible actualmente en las regiones siguientes:

- Este de Australia
- Sudeste de Australia
- Sur de Brasil
- Centro de Canadá
- Centro de EE. UU.
- Este de China 2
- Norte de China 2
- Asia oriental
- East US
- Este de EE. UU. 2
- Centro de Francia
- Este de Japón
- Oeste de Japón
- Corea Central
- Corea del Sur
- Centro-Norte de EE. UU
- Europa del Norte
- Norte de Sudáfrica
- Centro-Sur de EE. UU
- Sudeste asiático
- Sur de Reino Unido 2
- Oeste de Reino Unido
- Europa occidental
- Oeste de EE. UU.
- Oeste de EE. UU. 2

Si desea crear una base de datos Hiperescala en una región que no conste como admitida, puede enviar una solicitud de incorporación a través de Azure Portal. Estamos trabajando para ampliar la lista de regiones admitidas, así que consulte la lista de regiones más reciente.

Para solicitar la capacidad de crear bases de datos Hiperescala en regiones que no constan en la lista:

1. Vaya a [Hoja de ayuda y soporte técnico de Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

2. Haga clic en [**Nueva solicitud de soporte técnico**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

    ![Hoja de ayuda y soporte técnico de Azure](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. En **Tipo de problema**, seleccione **Límites de servicio y suscripción (cuotas)** .

4. Elija la suscripción que utilizaría para crear las bases de datos.

5. En **Tipo de cuota**, seleccione **Base de datos SQL**.

6. Haga clic en **Siguiente: Soluciones**

1. Haga clic en **Proporcionar detalles**

    ![Detalles del problema](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. Elija **Tipo de cuota de base de datos SQL**: **otra solicitud de cuota**

9. Rellene la plantilla siguiente:

    ![Detalles de la cuota](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    En la plantilla, proporcione la información siguiente:

    > Solicitud para crear la base de datos de SQL Azure Hiperescala en una región nueva<br/> Región: (escriba la región solicitada)  <br/>
    > Procese el SKU/número total de núcleos, incluidas las réplicas legibles <br/>
    > Número de TB estimado 
    >

10. Elija **Severity C** (Gravedad C)

11. Elija el método de contacto adecuado y rellene los detalles.

12. Haga clic en **Guardar** y **Continuar**.

## <a name="known-limitations"></a>Limitaciones conocidas
Estas son las limitaciones actuales para el nivel de servicio Hiperescala en disponibilidad general.  Estamos trabajando activamente para eliminar tantas limitaciones como sea posible.

| Problema | DESCRIPCIÓN |
| :---- | :--------- |
| El panel Administrar copias de seguridad para un servidor lógico no muestra las bases de datos Hiperescala que se van a filtrar desde SQL Server  | Hiperescala tiene un método independiente para administrar las copias de seguridad y, por tanto, la configuración de la retención de copias de seguridad correspondiente a la retención a largo plazo y a un momento dado no se aplican o se invalidan. En consecuencia, las bases de datos de Hiperescala no aparecen en el panel Administración de copias de seguridad. |
| Restauración a un momento dado | Una vez que se migra una base de datos al nivel de servicio Hiperescala, no se puede restaurar a un momento dado anterior a la migración.|
| Restauración de una base de datos que no sea Hiperescala en una base de datos Hiperescala y viceversa | No se puede restaurar una base de datos Hiperescala en una base de datos que no sea Hiperescala, ni se puede restaurar una base de datos que no sea Hiperescala en una base de datos Hiperescala.|
| Si una base de datos tiene uno o más archivos de datos de más de 1 TB, se produce un error en la migración | En algunos casos, es posible que se pueda solucionar este problema si se reducen los archivos de gran tamaño para que tengan menos de 1 TB. Si va a migrar una base de datos que se utiliza durante el proceso de migración, asegúrese de que ningún archivo tenga un tamaño superior a 1 TB. Utilice la siguiente consulta para determinar el tamaño de los archivos de base de datos. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Instancia administrada | Instancia administrada de Azure SQL Database no es compatible actualmente con las bases de datos Hiperescala. |
| Grupos elásticos |  Los grupos elásticos no admiten actualmente con SQL Database Hiperescala.|
| La migración a Hiperescala actualmente es una operación unidireccional. | Una vez que una base de datos se migra a Hiperescala, no puede migrarse directamente a un nivel de servicio que no sea Hiperescala. En la actualidad, la única manera de migrar una base de datos de Hiperescala a un recursos que no sea de Hiperescala es exportar o importar mediante un archivo BACPAC u otras tecnologías de movimiento de datos (copia masiva, Azure Data Factory, Azure Databricks, SSIS, etc.)|
| Migración de bases de datos con objetos en memoria persistentes | Hiperescala solo admite objetos en memoria no persistentes (tipos de tabla, SP nativos y funciones).  Las tablas en memoria persistentes y otros objetos deben quitarse y volver a crearse como objetos que no sean en memoria antes de migrar una base de datos al nivel de servicio Hiperescala.|
| Seguimiento de cambios | Todavía no puede configurar ni usar Change Tracking con las bases de datos de Hiperescala de Azure SQL. |
| Replicación geográfica  | Todavía no se puede configurar la replicación geográfica activa para Azure SQL Database Hiperescala. |
| Copia de base de datos | Todavía no puede usar la copia de base de datos para crear una base de datos nueva en Hiperescala de Azure SQL. |
| Integración de TDE/AKV | Cifrado de base de datos transparente con Azure Key Vault (conocido comúnmente como Bring-Your-Own-Key o BYOK) todavía no es compatible con Hiperescalado de Azure SQL Database, pero es totalmente compatible con Claves administradas de servicio. |
|Características de bases de datos inteligentes | Con la excepción de la opción "Forzar plan",todas las demás opciones de ajuste automático no se admiten aún en Hiperescala: puede parecer que las opciones están habilitadas, pero no se realizarán recomendaciones ni acciones. |

## <a name="next-steps"></a>Pasos siguientes

- Para consultar preguntas frecuentes sobre Hiperescala, consulte [Preguntas más frecuentes acerca de Hiperescala](sql-database-service-tier-hyperscale-faq.md).
- Para más información sobre los niveles de servicio, consulte [Niveles de servicio](sql-database-service-tiers.md).
- Consulte [Introducción a los límites de recursos de un servidor lógico](sql-database-resource-limits-logical-server.md) para obtener información acerca de los límites en los niveles de servidor y suscripción.
- Para conocer los límites del modelo de compras para una base de datos única, consulte [Límites del modelo de compra basado en núcleo virtual de Azure SQL Database para una base de datos única](sql-database-vcore-resource-limits-single-databases.md).
- Para obtener una lista de características y una comparación, consulte [Características comunes de SQL](sql-database-features.md).
