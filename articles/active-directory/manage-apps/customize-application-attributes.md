---
title: Personalización de asignaciones de atributos de Azure AD | Microsoft Docs
description: Conozca cuáles son las asignaciones de atributos para aplicaciones SaaS en Azure Active Directory y cómo puede modificarlas para satisfacer sus necesidades empresariales.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82c1a536bb86f0b3a4fe6a24af00379686ccc292
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641503"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalización de asignaciones de atributos de aprovisionamiento de usuarios para aplicaciones SaaS en Azure Active Directory

Microsoft Azure AD proporciona soporte técnico para el aprovisionamiento de usuarios en aplicaciones SaaS de terceros como Salesforce, Google Apps y otras. Si habilita el aprovisionamiento de usuarios para una aplicación SaaS de terceros, Azure Portal controla sus valores de atributo en forma de una asignación de atributos.

Hay un conjunto preconfigurado de atributos y asignaciones de atributos entre los objetos de usuario de Azure AD y los objetos de usuario de cada aplicación SaaS. Además de los usuarios, algunas aplicaciones administran otros tipos de objetos, como los grupos.

Puede personalizar las asignaciones de atributos predeterminadas según sus necesidades empresariales. Esto significa que puede cambiar o eliminar asignaciones de atributos existentes o crear nuevas asignaciones de atributos.

## <a name="editing-user-attribute-mappings"></a>Edición de asignaciones de atributos de usuario

Siga estos pasos para acceder a la característica **Asignaciones** del aprovisionamiento de usuarios:

1. Inicie sesión en el [portal de Azure Active Directory](https://aad.portal.azure.com).
1. En el panel izquierdo, seleccione **Aplicaciones empresariales**. Se muestra una lista de las aplicaciones configuradas, incluidas aquellas que se han agregado desde la galería.
1. Seleccione cualquier aplicación para cargar el panel de administración de aplicaciones, donde puede ver los informes y administrar la configuración de la aplicación.
1. Seleccione **Aprovisionamiento** para administrar la configuración de aprovisionamiento de cuentas de usuario de la aplicación seleccionada.
1. Expanda **Asignaciones** para ver y modificar los atributos de usuario que fluyen entre Azure AD y la aplicación de destino. Si la aplicación de destino lo admite, en esta sección se puede configurar opcionalmente el aprovisionamiento de grupos y cuentas de usuario.

   ![Uso de asignaciones para ver y editar atributos de usuario](./media/customize-application-attributes/21.png)

1. Al seleccionar una configuración de **Asignaciones**, se abre la pantalla relacionada **Asignación de atributos**. Hay algunas asignaciones de atributos que una aplicación SaaS necesita para funcionar correctamente. Para los atributos necesarios, la característica **Eliminar** no está disponible.

   ![Uso de la asignación de atributos para configurar asignaciones de atributos para aplicaciones](./media/customize-application-attributes/22.png)

   En esta captura de pantalla, puede ver que el atributo **Username** de un objeto administrado en Salesforce se rellena con el valor de **userPrincipalName** del objeto vinculado de Azure Active Directory.

1. Seleccione una **asignación de atributos** para abrir la pantalla **Editar atributo**. Aquí puede editar los atributos de usuario que fluyen entre Azure AD y la aplicación de destino.

   ![Uso del atributo de edición para editar atributos de usuario](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Información sobre los tipos de asignación de atributos

Con asignaciones de atributos, puede controlar cómo se rellenan los atributos en una aplicación SaaS de terceros.
Se admiten cuatro tipos de asignaciones diferentes:

- **Directa** : el atributo de destino se rellena con el valor de un atributo del objeto vinculado en Azure AD.
- **Constante**: el atributo de destino se rellena con una cadena específica que se ha especificado.
- **Expresión** : el atributo de destino se rellena según el resultado de una expresión similar a un script.
  Para más información, consulte [Escritura de expresiones para la asignación de atributos en Azure Active Directory](functions-for-customizing-application-data.md).
- **Ninguno** : el atributo de destino se deja sin modificar. Sin embargo, si el atributo de destino está vacío, se rellena con el valor predeterminado que especifique.

Además de estos cuatro tipos básicos, las asignaciones de atributos personalizadas admiten el concepto de una asignación de valor **predeterminada** opcional. La asignación de valor predeterminada garantiza que un atributo de destino se rellene con un valor si no hay ningún valor en Azure AD ni en el objeto de destino. La configuración más habitual consiste en dejarlo en blanco.

### <a name="understanding-attribute-mapping-properties"></a>Información sobre las propiedades de asignación de atributos

En la sección anterior, ya ha introducido la propiedad de tipo de asignación de atributos.
Además de esta propiedad, las asignaciones de atributos también admiten los siguientes atributos:

- **Atributo de origen**: especifica el atributo de usuario del sistema de origen (por ejemplo, Azure Active Directory).
- **Atributo de destino**: especifica el atributo de usuario del sistema de destino (por ejemplo, ServiceNow).
- **Hacer coincidir objetos con este atributo**: especifica si se debe usar o no esta asignación para identificar de forma unívoca a los usuarios entre los sistemas de origen y de destino. Normalmente esto se establece en el atributo userPrincipalName o mail en Azure AD, que se suele asignar a un campo de nombre de usuario en una aplicación de destino.
- **Precedencia de coincidencia**: se pueden establecer varios atributos coincidentes. Si hay varios, se evalúan en el orden definido por este campo. En el momento en que se encuentre una coincidencia, no se evaluarán más atributos coincidentes.
- **Aplicar esta asignación**
  - **Siempre**: esta asignación se aplica a las acciones de creación y actualización de usuarios.
  - **Solo durante la creación**: esta asignación se aplica solo a las acciones de creación de usuarios.

## <a name="matching-users-in-the-source-and-target--systems"></a>Emparejamiento de usuarios en los sistemas de origen y destino
El servicio de aprovisionamiento de Azure AD se puede implementar en escenarios Greenfield (los usuarios no existen en el sistema de destino) y Brownfield (los usuarios ya existen en el sistema de destino). Para admitir ambos escenarios, el servicio de aprovisionamiento usa el concepto de atributos coincidentes. Los atributos coincidentes permiten determinar cómo identificar de forma única a un usuario en el origen y emparejarlo en el destino. Como parte de la planeación de la implementación, identifique el atributo que se puede usar para identificar de forma única a un usuario en los sistemas de origen y destino. Cosas que hay que tener en cuenta:

- **Los atributos coincidentes deben ser únicos:** los clientes a menudo usan atributos como userPrincipalName, mail u object ID como atributo coincidente.
- **Se pueden usar varios atributos como atributos coincidentes:** puede definir varios atributos para que se evalúen al emparejar usuarios y el orden en el que se evalúan (definido como precedencia de coincidencias en la interfaz de usuario). Por ejemplo, si define tres atributos como atributos coincidentes y un usuario se empareja de forma única después de evaluar los dos primeros atributos, el servicio no evalúa el tercero. El servicio evalúa los atributos coincidentes en el orden especificado y deja de evaluar cuando se encuentra una coincidencia.  
- **El valor en el origen y el destino no tienen que coincidir exactamente:** el valor en el destino puede ser una función sencilla del valor del origen. Así, uno podría tener un atributo emailAddress en el origen y userPrincipalName en el destino, y emparejar mediante una función del atributo emailAddress que reemplace algunos caracteres por algún valor constante.  
- **No se admite el emparejamiento en función de una combinación de atributos:** la mayoría de las aplicaciones no admiten consultas basadas en dos propiedades y, por lo tanto, no es posible emparejar en función de una combinación de atributos. Es posible evaluar propiedades únicas una después de otra.
- **Todos los usuarios deben tener un valor para al menos un atributo coincidente:** si define un atributo coincidente, todos los usuarios deben tener un valor para ese atributo en el sistema de origen. Si, por ejemplo, define userPrincipalName como atributo coincidente, todos los usuarios deben tener un valor userPrincipalName. Si define varios atributos coincidentes (por ejemplo, extensionAttribute1 y mail), no todos los usuarios deben tener el mismo atributo coincidente. Un usuario podría tener extensionAttribute1 pero no mail mientras que otro usuario podría tener mail pero no extensionAttribute1. 
- **La aplicación de destino debe admitir el filtrado en el atributo coincidente:** los desarrolladores de aplicaciones permiten filtrar por un subconjunto de atributos en su API de usuario o grupo. En el caso de las aplicaciones de la galería, se garantiza que la asignación de atributos predeterminada es para un atributo en el que la API de la aplicación de destino admite el filtrado. Al cambiar el atributo coincidente predeterminado de la aplicación de destino, compruebe la documentación de las API de terceros para asegurarse de que se puede filtrar por el atributo.  

## <a name="editing-group-attribute-mappings"></a>Edición de asignaciones de atributos de grupo

Algunas aplicaciones seleccionadas, como ServiceNow, Box y G Suite, admiten la posibilidad de aprovisionar objetos de grupo además de objetos de usuario. Los objetos de grupo pueden contener propiedades de grupo como nombres para mostrar y alias de correo electrónico, además de miembros de grupo.

![En el ejemplo se muestra ServiceNow con objetos de grupo y usuario aprovisionados](./media/customize-application-attributes/24.png)

El aprovisionamiento de grupos se puede habilitar o deshabilitar de manera opcional; para ello, seleccione la asignación de grupo en **Asignaciones** y establezca **Habilitado** en la opción deseada en la pantalla **Asignación de atributos**.

Los atributos suministrados como parte de los objetos de grupo se pueden personalizar de la misma manera que los objetos de usuario descritos anteriormente. 

> [!TIP]
> El aprovisionamiento de objetos de grupo (propiedades y miembros) es un concepto diferente al de [asignación de grupos](assign-user-or-group-access-portal.md) a una aplicación. Es posible asignar un grupo a una aplicación, pero solo se pueden aprovisionar los objetos de usuario contenidos en el grupo. El aprovisionamiento de objetos de grupo completos no es necesario para usar grupos en asignaciones.

## <a name="editing-the-list-of-supported-attributes"></a>Edición de la lista de atributos admitidos

Los atributos de usuario admitidos en una determinada aplicación están preconfigurados. La mayoría de las API de administración de usuarios de la aplicación de la mayoría no son compatibles con la detección de esquemas. Por lo tanto, el servicio de aprovisionamiento de Azure AD no puede generar dinámicamente la lista de atributos admitidos mediante llamadas a la aplicación.

Sin embargo, algunas aplicaciones admiten atributos personalizados, y el servicio de aprovisionamiento de Azure AD puede leer y escribir en los atributos personalizados. Para especificar sus definiciones en Azure Portal, seleccione la casilla **Mostrar opciones avanzadas** de la parte inferior de la pantalla **Asignación de atributos** y, a continuación, seleccione **Asignación de atributos** en la aplicación.

Las aplicaciones y sistemas que admiten la personalización de la lista de atributos son:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (se admiten los [atributos predeterminados de Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) y las extensiones de directorios personalizadas)
- Aplicaciones que admiten [SCIM 2.0](https://tools.ietf.org/html/rfc7643), donde es necesario agregar los atributos definidos en el [esquema principal](https://tools.ietf.org/html/rfc7643)

> [!NOTE]
> La edición de la lista de atributos admitidos solo se recomienda para administradores que hayan personalizado el esquema de sus aplicaciones y sistemas, y tengan conocimiento de primera mano de cómo se han definido sus atributos personalizados. A veces, es necesario estar familiarizado con las API y las herramientas de los desarrolladores que se proporcionan en una aplicación o un sistema.

Al editar la lista de atributos admitidos, se proporcionan las siguientes propiedades:

- **Nombre**: el nombre del sistema del atributo, tal como se define en el esquema del objeto de destino.
- **Tipo**: el tipo de datos que almacena el atributo, tal como se define en el esquema del objeto de destino, que puede ser uno de los siguientes:
  - *Binario*: el atributo contiene datos binarios.
  - *Booleano*: el atributo contiene un valor True o False.
  - *DateTime*: el atributo contiene una cadena de fecha.
  - *Entero*: al atributo contiene un entero.
  - *Referencia*: el atributo contiene un identificador que hace referencia a un valor almacenado en otra tabla en la aplicación de destino.
  - *Cadena*: el atributo contiene una cadena de texto.
- **¿Clave principal?** : si el atributo se define como un campo de clave principal en el esquema del objeto de destino.
- **¿Necesario?** : si el atributo se debe rellenar en la aplicación o sistema de destino.
- **¿Varios valores?** : si el atributo admite varios valores.
- **¿Coincidir mayúsculas y minúsculas?** : si los valores de atributo se evalúan según el uso de mayúsculas o minúsculas.
- **Expresión de API**: no se usa, salvo que así se indique en la documentación de un conector de aprovisionamiento específico (como Workday).
- **Atributo de objeto con referencia**: si este es un atributo de tipo referencia, este menú le permite seleccionar la tabla y el atributo de la aplicación de destino que contiene el valor asociado al atributo. Por ejemplo, si tiene un atributo llamado "Department" cuyo valor almacenado hace referencia a un objeto de una tabla "Departments" independiente, seleccionaría "Departments.Name". Las tablas de referencia y los campos de identificador principal admitidos en una determinada aplicación están preconfigurados y actualmente no se pueden editar mediante Azure Portal, pero se pueden modificar con la [Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Para agregar un nuevo atributo, desplácese hasta el final de la lista de atributos admitidos, rellene los campos anteriores mediante las entradas proporcionadas y seleccione **Agregar atributo**. Cuando termine de agregar atributos, seleccione **Guardar**. Tendrá que volver a cargar la pestaña **Aprovisionamiento** para que los nuevos atributos estén disponibles en el editor de asignación de atributos.
## <a name="provisioning-a-role-to-a-scim-app"></a>Aprovisionamiento de un rol para una aplicación de SCIM
Use los pasos siguientes para aprovisionar roles para un usuario para la aplicación. Tenga en cuenta que la descripción siguiente es específica de las aplicaciones de SCIM personalizadas. En el caso de las aplicaciones de la galería como Salesforce y ServiceNow, use las asignaciones de roles predefinidas. En las viñetas siguientes se explica cómo transformar el atributo AppRoleAssignments al formato que espera la aplicación.

- La asignación de un elemento appRoleAssignment en Azure AD a un rol de la aplicación requiere que se transforme el atributo mediante una [expresión](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data). El atributo appRoleAssignment **no debe asignarse directamente** a un atributo de rol sin usar una expresión para analizar los detalles del rol. 

- **SingleAppRoleAssignment** 
  - **Cuándo se debe usar:** use la expresión SingleAppRoleAssignment para aprovisionar un rol único para un usuario y para especificar el rol principal. 
  - **Cómo se configura:** use los pasos anteriores para ir a la página de asignaciones de atributos y use la expresión SingleAppRoleAssignment para asignar al atributo de roles. Hay tres atributos de roles entre los que elegir: (roles[primary eq "True"].display, roles[primary eq "True].type y roles[primary eq "True"].value). Puede optar por incluir cualquiera de los atributos de rol o todos en las asignaciones. Si quiere incluir más de uno, simplemente agregue una nueva asignación e inclúyala como atributo de destino.  
  
  ![Adición de SingleAppRoleAssignment](./media/customize-application-attributes/edit-attribute-singleapproleassignment.png)
  - **Cosas que hay que tener en cuenta**
    - Asegúrese de que no se asignen varios roles a un usuario. No se puede garantizar el rol que se va a aprovisionar.
    
  - **Salida del ejemplo** 

   ```json
    {
      "schemas": [
          "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": true,
               "type": "WindowsAzureActiveDirectoryRole",
               "value": "Admin"
         }
      ]
   }
   ```
  
- **AppRoleAssignmentsComplex** 
  - **Cuándo se debe usar:** use la expresión AppRoleAssignmentsComplex para aprovisionar varios roles para un usuario. 
  - **Cómo se configura:** edite la lista de atributos admitidos como se explica arriba para incluir un nuevo atributo para los roles: 
  
    ![Agregar roles](./media/customize-application-attributes/add-roles.png)<br>

    Luego use la expresión AppRoleAssignmentsComplex para asignar al atributo de rol personalizado como se muestra en la imagen siguiente:

    ![Adición de AppRoleAssignmentsComplex](./media/customize-application-attributes/edit-attribute-approleassignmentscomplex.png)<br>
  - **Cosas que hay que tener en cuenta**
    - todos los roles se aprovisionan como primary = false.
    - POST contiene el tipo de rol. La solicitud PATCH no contiene tipo. Se trabaja en el envío del tipo en las solicitudes POST y PATCH.
    
  - **Salida del ejemplo** 
  
   ```json
   {
       "schemas": [
           "urn:ietf:params:scim:schemas:core:2.0:User"
      ],
      "externalId": "alias",
      "userName": "alias@contoso.OnMicrosoft.com",
      "active": true,
      "displayName": "First Name Last Name",
      "meta": {
           "resourceType": "User"
      },
      "roles": [
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "Admin",
               "value": "Admin"
         },
         {
               "primary": false,
               "type": "WindowsAzureActiveDirectoryRole",
               "display": "User",
             "value": "User"
         }
      ]
   }
   ```

  


## <a name="provisioning-a-multi-value-attribute"></a>Aprovisionamiento de un atributo de varios valores
Algunos atributos, como phoneNumbers y emails, son atributos de varios valores en los que puede que necesite especificar diferentes tipos de números de teléfono o correos electrónicos. Use la expresión siguiente para los atributos de varios valores. Permite especificar el tipo de atributo y asignarlo al atributo de usuario de Azure AD correspondiente para el valor. 

* phoneNumbers[type eq "work"].value
* phoneNumbers[type eq "mobile"].value
* phoneNumbers[type eq "fax"].value

   ```json
   "phoneNumbers": [
       {
         "value": "555-555-5555",
         "type": "work"
      },
      {
         "value": "555-555-5555",
         "type": "mobile"
      },
      {
         "value": "555-555-5555",
         "type": "fax"
      }
   ]
   ```

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Restauración de los atributos predeterminados y las asignaciones de atributos

Si necesita comenzar de nuevo y restablecer las asignaciones existentes de nuevo a su estado predeterminado, puede activar la casilla **Restaurar asignaciones predeterminadas** y guardar la configuración. Al hacerlo, todas las asignaciones se restablecen como si la aplicación se acabara de agregar al inquilino de Azure AD desde la galería de aplicaciones.

Al seleccionar esta opción se fuerza la resincronización de todos los usuarios mientras se ejecuta el servicio de aprovisionamiento.

> [!IMPORTANT]
> Se recomienda firmemente establecer el **estado de aprovisionamiento** en **Desactivado** antes de invocar esta opción.

## <a name="what-you-should-know"></a>Qué debería saber

- Microsoft Azure AD proporciona una implementación eficaz de un proceso de sincronización. En un entorno inicializado, sólo los objetos que requieren actualizaciones se procesan durante un ciclo de sincronización.
- Actualizar las asignaciones de atributos repercute en el rendimiento de un ciclo de sincronización. Una actualización de la configuración de la asignación de atributos requiere que se vuelvan a evaluar todos los objetos administrados.
- Es un procedimiento recomendado mantener el número mínimo de cambios consecutivos de las asignaciones de atributos.
- Actualmente no se puede agregar un atributo de foto para su aprovisionamiento en una aplicación, ya que no se permite especificar el formato para sincronizar la foto. Puede solicitar la característica en [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory)
- El atributo IsSoftDeleted suele formar parte de las asignaciones predeterminadas para una aplicación. IsSoftdeleted puede ser true en uno de los cuatro escenarios siguientes: el usuario está fuera del ámbito debido a que se ha desasignado de la aplicación; el usuario está fuera del ámbito debido a que no cumple un filtro de ámbito; el usuario se ha eliminado temporalmente en Azure AD; o la propiedad AccountEnabled está establecida en false en el usuario. 
- El servicio de aprovisionamiento de Azure AD no admite el aprovisionamiento de valores NULL.

## <a name="next-steps"></a>Pasos siguientes

- [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](user-provisioning.md)
- [Escritura de expresiones para la asignación de atributos](functions-for-customizing-application-data.md)
- [Filtros de ámbito para el aprovisionamiento de usuario](define-conditional-rules-for-provisioning-user-accounts.md)
- [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](use-scim-to-provision-users-and-groups.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](../saas-apps/tutorial-list.md)
