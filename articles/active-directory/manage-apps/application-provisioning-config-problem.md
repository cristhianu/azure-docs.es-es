---
title: Problema al configurar el aprovisionamiento de usuarios para una aplicación de la galería de Azure AD | Microsoft Docs
description: Cómo solucionar los problemas habituales que surgen al configurar el aprovisionamiento de usuarios para una aplicación que ya aparece en la galería de aplicaciones de Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a8eaa46b46551f9b6075ec10b38de80f84c22a0
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034142"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema al configurar el aprovisionamiento de usuarios para una aplicación de la galería de Azure AD

La configuración del [aprovisionamiento automático de usuarios](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) para una aplicación (si se admite) requiere que se sigan las instrucciones concretas para preparar la aplicación para el aprovisionamiento automático. A continuación, puede usar Azure Portal para configurar el servicio de aprovisionamiento para sincronizar las cuentas de usuario con la aplicación.

Siempre debería empezar buscando el tutorial de configuración específico para configurar el aprovisionamiento de la aplicación. A continuación, siga esos pasos para configurar tanto la aplicación como Azure AD para crear la conexión de aprovisionamiento. Puede encontrar una lista de tutoriales sobre aplicaciones en [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

## <a name="how-to-see-if-provisioning-is-working"></a>Comprobación de si el aprovisionamiento está funcionando 

Una vez que el servicio está configurado, se puede extraer la mayor parte de la información sobre el funcionamiento del servicio de dos lugares:

-   **Registros de aprovisionamiento (versión preliminar)** : los [registros de aprovisionamiento](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registran todas las operaciones realizadas por el servicio de aprovisionamiento, incluidas las consultas en Azure AD de los usuarios asignados que pertenecen al ámbito de aprovisionamiento. Consulte la existencia de esos usuarios en la aplicación de destino y compare los objetos de usuario entre el sistema. Después, agregue, actualice o deshabilite la cuenta de usuario en el sistema de destino en función de la comparación. Para acceder a los registros de aprovisionamiento en Azure Portal, seleccione **Azure Active Directory** &gt; **Aplicaciones empresariales** &gt; **Registros de aprovisionamiento (versión preliminar)** en la sección **Actividad**.

-   **Estado actual**: se puede ver un resumen de aprovisionamiento de la última ejecución del aprovisionamiento para una aplicación determinada en la sección **Azure Active Directory &gt; Aplicaciones empresariales &gt; \[Nombre de la aplicación\] &gt;Aprovisionamiento**, en la parte inferior de la pantalla bajo la configuración del servicio. En la sección Estado actual se muestra si un ciclo de aprovisionamiento ha iniciado las cuentas de usuario de aprovisionamiento. Puede ver el progreso del ciclo, cuántos usuarios y grupos se han aprovisionado y cuántos roles se crean. Si hay errores, puede consultar los detalles en [Registros de aprovisionamiento (versión preliminar)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas problemáticas generales con el aprovisionamiento que tener en cuenta

A continuación, se muestra una lista de las áreas problemáticas generales en las que puede profundizar si tiene idea de por dónde empezar.

* [No parece que el servicio de aprovisionamiento se inicie](#provisioning-service-does-not-appear-to-start)
* No se puede guardar la configuración porque las credenciales de la aplicación no funcionan
* [Los registros de aprovisionamiento indican que hay usuarios "omitidos" y no aprovisionados, aunque estén asignados](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>No parece que el servicio de aprovisionamiento se inicie

Si establece **Estado de aprovisionamiento** en **Activado** en la sección **Azure Active Directory &gt; Aplicaciones empresariales &gt; \[Nombre de la aplicación\] &gt;Aprovisionamiento** de Azure Portal. No obstante, no se muestran más detalles de estado en esa página tras recargas posteriores. Es probable que el servicio se esté ejecutando pero que no se haya completado aún un ciclo inicial. Compruebe los **registros de aprovisionamiento** descritos antes para determinar qué operaciones está realizando el servicio y si se han producido errores.

>[!NOTE]
>Un ciclo inicial puede tardar desde 20 minutos hasta varias horas, según el tamaño del directorio de Azure AD y el número de usuarios en el ámbito de aprovisionamiento. Los ciclos posteriores al inicial serán más rápidos, ya que el servicio de aprovisionamiento almacena marcas de agua que representan el estado de ambos sistemas tras el ciclo inicial, lo cual mejora el rendimiento de los posteriores.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>No se puede guardar la configuración porque las credenciales de la aplicación no funcionan

Para que funcione el aprovisionamiento, Azure AD necesita credenciales válidas que le permitan conectarse a una API de administración de usuarios proporcionada por esa aplicación. Si estas credenciales no funcionan o las desconoce, revise el tutorial para configurar esta aplicación, tal como se ha descrito antes.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Los registros de aprovisionamiento indican que hay usuarios omitidos y no aprovisionados, aunque estén asignados

Cuando un usuario se muestra como "Omitido" en los registros de aprovisionamiento, es muy importante que lea los detalles ampliados en el mensaje del registro para determinar la razón. Algunas razones y soluciones habituales son:

- **Se ha configurado un filtro de ámbito** **que está filtrando al usuario por un valor de atributo**. Para más información sobre los filtros de ámbito, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters>.

- **El usuario no está autorizado de forma efectiva.** Si ve un mensaje de error de este tipo, se debe a que hay un problema con el registro de asignación de usuarios almacenado en Azure AD. Para corregir este problema, cancele la asignación del usuario (o grupo) de la aplicación y vuelva a repetirla. Para más información sobre la asignación, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal>.

- **Un atributo obligatorio falta o no se ha llenado para un usuario.** Una cuestión importante que tener en cuenta al configurar el aprovisionamiento es revisar y configurar las asignaciones de atributos y los flujos de trabajo que definen qué propiedades de usuario (o de grupo) fluyen de Azure AD a la aplicación. Esto incluye la configuración de la "propiedad de coincidencia" que se usa para identificar de forma exclusiva y emparejar a usuarios y grupos entre ambos sistemas. Para más información acerca de este proceso importante, consulte <https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings>.

  * **Asignaciones de atributos a grupos** Aprovisionamiento del nombre del grupo y los detalles del grupo, además de los miembros, si se admite para algunas aplicaciones. Puede habilitar o deshabilitar esta funcionalidad habilitando o deshabilitando la **Asignación** para los objetos de grupo que se muestran en la pestaña **Aprovisionamiento**. Si los grupos de aprovisionamiento están habilitados, asegúrese de revisar las asignaciones de atributos para asegurarse de que se use un campo apropiado para el "identificador de coincidencia". Esto puede ser el nombre para mostrar o el alias de correo electrónico, ya que el grupo y sus miembros no se habrán aprovisionado si la propiedad de coincidencia está vacía o no se ha rellenado para un grupo en Azure AD.

## <a name="next-steps"></a>Pasos siguientes
[Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](user-provisioning.md)
