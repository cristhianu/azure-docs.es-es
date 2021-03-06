---
title: Creación y administración de un servidor de Azure Database for MariaDB mediante Azure Portal
description: Este artículo describe cómo crear rápidamente un nuevo servidor de Azure Database for MariaDB y administrarlo mediante Azure Portal.
author: ambhatna
ms.author: ambhatna
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 5aae3eb0582956ccb45cc41d8400f489f1077bad
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143203"
---
# <a name="create-and-manage-azure-database-for-mariadb-server-using-azure-portal"></a>Creación y administración de un servidor de Azure Database for MariaDB mediante Azure Portal
En este tema se describe cómo puede crear rápidamente un servidor de Azure Database for MariaDB. También incluye información sobre cómo administrar el servidor mediante Azure Portal. La administración del servidor incluye ver detalles del mismo y bases de datos, restablecer la contraseña, escalar recursos y eliminar el servidor.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-database-for-mariadb-server"></a>Creación de un servidor de Azure Database for MariaDB
Siga estos pasos para crear un servidor de Azure Database for MariaDB llamado “mydemoserver”.

1. Haga clic en el botón **Crear un recurso** de la esquina superior izquierda de Azure Portal.

2. En la página Nuevo, seleccione **Bases de datos** y, en la página Bases de datos, seleccione **Azure Database for MariaDB**.

    > Se crea un servidor de Azure Database for MariaDB con un conjunto definido de recursos de [proceso y almacenamiento](./concepts-pricing-tiers.md). La base de datos se crea dentro de un grupo de recursos de Azure y en un servidor de Azure Database for MariaDB.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Rellene el formulario de Azure Database for MariaDB utilizando la siguiente información:

    | **Campo del formulario** | **Descripción del campo** |
    |----------------|-----------------------|
    | *Nombre del servidor* | mydemoserver (el nombre del servidor es único globalmente) |
    | *Suscripción* | mysubscription (seleccione en el menú desplegable) |
    | *Grupos de recursos* | myresourcegroup (cree un nuevo grupo de recursos o use uno existente) |
    | *Seleccionar origen* | En blanco (cree un servidor de MariaDB en blanco) |
    | *Inicio de sesión del administrador del servidor* | myadmin (dé un nombre a la cuenta de administrador) |
    | *Contraseña* | indique la contraseña de la cuenta de administrador |
    | *Confirmar contraseña* | confirme la contraseña de la cuenta de administrador |
    | *Ubicación* | Sudeste Asiático (seleccione Europa del Norte u Oeste de EE. UU.) |
    | *Versión* | 10.3 (seleccione la versión de servidor de Azure Database for MariaDB) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Haga clic en **Configurar servidor** para especificar el nivel de rendimiento y el nivel de servicio del nuevo servidor. Seleccione la pestaña **Uso general**. *Gen 5*, *4 núcleos virtuales*, *100 GB* y *7 días* son los valores predeterminados de **Generación de procesos**, **Núcleos virtuales**, **Almacenamiento** y **Período de retención de copia de seguridad**. Puede dejar los controles deslizantes tal y como están. Para habilitar las copias de seguridad del servidor en el almacenamiento con redundancia geográfica, seleccione **Redundancia geográfica** en **Opciones de redundancia de copia de seguridad**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Haga clic en **Revisar y crear** para ir a la pantalla de revisión y compruebe todos los detalles. Haga clic en **Crear** para realizar el aprovisionamiento del servidor. El aprovisionamiento tarda unos minutos.

    > Seleccione la opción **Anclar al panel** para realizar el seguimiento de las implementaciones fácilmente.

## <a name="update-an-azure-database-for-mariadb-server"></a>Actualización de un servidor de Azure Database for MariaDB
Cuando se haya aprovisionado el nuevo servidor, el usuario tiene varias opciones para configurar el servidor existente: restablecer la contraseña de administrador, cambiar el plan de tarifa y cambiar los núcleos virtuales o el almacenamiento para escalar o reducir verticalmente el servidor.

### <a name="change-the-administrator-user-password"></a>Cambio de la contraseña del usuario administrador
1. En **Información general** del servidor, haga clic en **Restablecer contraseña** para mostrar la ventana de restablecimiento de contraseñas.

   ![Información general](./media/howto-create-manage-server-portal/overview.png)

2. Escriba y confirme la contraseña en la ventana tal y como se muestra en:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Haga clic en **Aceptar** para guardar la nueva contraseña.

### <a name="change-the-pricing-tier"></a>Cambio del plan de tarifa
> [!NOTE]
> El escalado solo se admite de los niveles de servicio De uso general a Optimizada para memoria y viceversa. Tenga en cuenta que, una vez creado el servidor, no se permite el cambio a y desde un plan de tarifa Básico en Azure Database for MariaDB.
> 
1. Haga clic en **Plan de tarifa**, que se encuentra en **Configuración**.
2. Seleccione el **plan de tarifa** al que quiera cambiar.

    ![change-pricing-tier](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Haga clic en **Aceptar** para guardar los cambios. 

### <a name="scale-vcores-updown"></a>Escalado y reducción vertical de los núcleos virtuales

1. Haga clic en **Plan de tarifa**, que se encuentra en **Configuración**.

2. Cambie el valor de **vCore** moviendo el control deslizante hasta alcanzar el valor deseado.

    ![escalar proceso](./media/howto-create-manage-server-portal/scale-compute.png)

3. Haga clic en **Aceptar** para guardar los cambios.

### <a name="scale-storage-up"></a>Escalado vertical del almacenamiento

1. Haga clic en **Plan de tarifa**, que se encuentra en **Configuración**.

2. Cambie el valor de **Almacenamiento** moviendo el control deslizante hasta alcanzar el valor deseado.

    ![escalar almacenamiento](./media/howto-create-manage-server-portal/scale-storage.png)

3. Haga clic en **Aceptar** para guardar los cambios.

## <a name="delete-an-azure-database-for-mariadb-server"></a>Eliminación de un servidor de Azure Database for MariaDB

1. En la hoja **Información general** del servidor, haga clic en el botón **Eliminar** para abrir la pregunta de confirmación de eliminación.

    ![delete](./media/howto-create-manage-server-portal/delete.png)

2. Escriba el nombre del servidor en el cuadro de entrada para realizar una doble confirmación.

    ![confirmar eliminación](./media/howto-create-manage-server-portal/confirm.png)

3. Haga clic en el botón **Eliminar** para confirmar la eliminación del servidor. Espere hasta que aparezca el aviso emergente "El servidor MariaDB se eliminó correctamente" en la barra de notificaciones.

## <a name="list-the-azure-database-for-mariadb-databases"></a>Lista de las bases de datos de Azure Database for MariaDB
En **Información general** del servidor, desplácese hacia abajo hasta que vea el icono de la base de datos en la parte inferior. Todas las bases de datos del servidor se muestran en la tabla.

   ![show-databases](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mariadb-server"></a>Presentación de los detalles de un servidor de Azure Database for MariaDB
Haga clic en **Propiedades**, que se encuentra en **Configuración** para ver información detallada acerca del servidor.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Pasos siguientes

[Inicio rápido: Creación de un servidor de Azure Database for MariaDB mediante Azure Portal](./quickstart-create-mariadb-server-database-using-azure-portal.md)