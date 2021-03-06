---
title: Completar una implementación de autenticación sin contraseña con Azure AD
description: Completar una implementación de autenticación sin contraseña de Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6f07e1dd8e9252d2b6e00b85a47ba2e19f8bd8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603450"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Completar una implementación de autenticación sin contraseña

La mayoría de los ciberataques comienza con un nombre de usuario y una contraseña robados a alguien de una organización. 

Las organizaciones intentan contrarrestar la amenaza solicitando a los usuarios que usen:

- Contraseñas largas
- Contraseñas complejas
- Cambios frecuentes de contraseñas
- Multi-Factor Authentication (MFA)

Las [investigaciones de Microsoft muestran](https://aka.ms/passwordguidance) que estos esfuerzos molestan a los usuarios y aumentan los costos de soporte técnico. Para más información, consulte [Your PA$$word doesn't matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) (Su contraseña no importa).

La implementación de autenticación sin contraseña proporciona las siguientes ventajas:

- Mayor seguridad. Para reducir el riesgo de ataques de suplantación de identidad y de phishing, quite las contraseñas porque suponen una superficie expuesta a ataques.
- Mejor experiencia del usuario. Proporcione a los usuarios una manera cómoda de acceder a los datos desde cualquier lugar y ofrezca un acceso móvil sencillo a Outlook, OneDrive, Office, etc.
- Información sólida. Obtenga información sobre la actividad sin contraseña de los usuarios con un registro y una auditoría sólidos.

La contraseña se sustituye por algo que tenga más algo que usted es o algo que conoce. Por ejemplo, en Windows Hello para empresas, un gesto biométrico, como la cara o la huella digital, o un PIN específico del dispositivo que no se transmite a través de la red.

Microsoft ofrece tres opciones de autenticación sin contraseña que abarcan muchos escenarios. Estos métodos se pueden utilizar conjuntamente. 

- [Windows Hello para empresas](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) está más indicado para los usuarios que trabajan en sus equipos Windows dedicados.
- El inicio de sesión con [llaves de seguridad FIDO2](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) es especialmente útil para los usuarios que inician sesión en máquinas compartidas, tales como quioscos, situaciones en las que se restringe el uso de teléfonos y para identidades con privilegios elevados.
- El inicio de sesión con teléfono con la aplicación [Microsoft Authenticator](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) resulta útil para ofrecer una opción sin contraseña a los usuarios con dispositivos móviles. Convierte cualquier teléfono iOS o Android en una credencial segura sin contraseña, ya que permite a los usuarios iniciar sesión en cualquier plataforma o explorador. Para iniciar sesión, los usuarios reciben una notificación en su teléfono, comprueban que el número mostrado en la pantalla coincide con el de su teléfono y, a continuación, usan datos biométricos o el PIN para confirmarlo.

## <a name="compare-passwordless-authentication-methods"></a>Comparación de los métodos de autenticación sin contraseña

Los métodos de autenticación sin contraseña de Microsoft habilitan distintos escenarios. Tenga en cuenta las necesidades de su organización, los requisitos previos y las funcionalidades de cada método de autenticación para seleccionar su estrategia de autenticación sin contraseña. Se recomienda que todas las organizaciones que usen dispositivos Windows 10 utilicen Windows Hello para empresas. A continuación, agregue inicio de sesión con teléfono (con la aplicación Microsoft Authenticator) o llaves de seguridad para otros escenarios.

### <a name="passwordless-authentication-scenarios"></a>Escenarios de autenticación sin contraseña

| Escenario | Autenticación con teléfono | Llaves de seguridad | Windows Hello para empresas |
| --- | --- | --- | --- |
| **Inicio de sesión en un equipo**: <br> desde un dispositivo Windows 10 asignado | **No** | **Sí** <br> Con reconocimiento biométrico, PIN | **Sí**<br>Con reconocimiento biométrico o PIN |
| **Inicio de sesión en un equipo**: <br> desde un dispositivo Windows 10 compartido | **No** | **Sí** <br> Con reconocimiento biométrico, PIN  | **No** |
| **Inicio de sesión en aplicación web**: <br>desde un equipo dedicado del usuario | **Sí** | **Sí** <br> El inicio de sesión único en las aplicaciones está habilitado por el inicio de sesión en el equipo | **Sí**<br> El inicio de sesión único en las aplicaciones está habilitado por el inicio de sesión en el equipo |
| **Inicio de sesión en aplicación web**: <br> desde un dispositivo móvil o no Windows | **Sí** | **No** | **No** |
| **Inicio de sesión en un equipo**: <br> equipo que no es Windows | **No** | **No** | **No** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Consideraciones técnicas de la aplicación Microsoft Authenticator

**Integración de AD FS**: cuando un usuario ha habilitado la credencial sin contraseña de Microsoft Authenticator, la autenticación predeterminada para ese usuario siempre será enviar una notificación para su aprobación. Se impide que los usuarios de un inquilino híbrido se dirijan a AD FS para iniciar sesión a menos que seleccionen "Use su contraseña en su lugar". Este proceso también omite las directivas de acceso condicional locales y los flujos de autenticación de paso a través. Sin embargo, cuando se especifica un login_hint, un usuario será reenviado de manera automática a AD FS y se omite la opción de usar la credencial sin contraseña.

**Servidor de Azure MFA**: los usuarios finales que están habilitados para MFA a través de un servidor de Azure MFA local de la organización pueden seguir creando y usando una credencial de inicio de sesión único telefónica sin contraseña. Si el usuario intenta actualizar varias instalaciones (más de 5) de Microsoft Authenticator con la credencial, este cambio puede dar lugar a un error.

**Registro de dispositivos**: para usar la aplicación Authenticator para la autenticación sin contraseña, el dispositivo debe estar registrado en el inquilino de Azure AD y no puede ser un dispositivo compartido. Un dispositivo solo se puede registrar en un único inquilino. Este límite significa que solo se admite una cuenta profesional o educativa para el inicio de sesión con teléfono con la aplicación Authenticator.

## <a name="prerequisites"></a>Requisitos previos

Las organizaciones deben cumplir los siguientes requisitos previos antes de comenzar una implementación sin contraseña.

| Requisito previo | Aplicación de autenticación | Llaves de seguridad FIDO2 |
| --- | --- | --- |
| [Se ha habilitado el registro combinado de Azure MFA y el autoservicio de restablecimiento de contraseña (SSPR)](howto-registration-mfa-sspr-combined.md) (característica en versión preliminar) | √ | √ |
| [Los usuarios pueden utilizar Azure MFA](howto-mfa-getstarted.md) | √ | √ |
| [Los usuarios se han registrado para Azure MFA y SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Los usuarios han registrado sus dispositivos móviles en Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 versión 1809 o superior con un explorador compatible, como Microsoft Edge o Mozilla Firefox <br> (versión 67 o posterior). <br> *Microsoft recomienda la versión 1903 o posteriores para tener compatibilidad nativa*. |   | √ |
| Llaves de seguridad FIDO2 compatibles. Asegúrese de que está usando un dispositivo de seguridad FIDO2 [verificado y probado por Microsoft](howto-authentication-passwordless-enable.md) u otro dispositivo de seguridad FIDO2 compatible. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Requisitos previos de Windows Hello para empresas

Los requisitos previos de Windows Hello dependen en gran medida de si se está implementando en una configuración local, híbrida o solo en la nube. Para más información, consulte la [lista completa de requisitos previos de Windows Hello para empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Los usuarios registran su método sin contraseña como parte del flujo de registro de Azure MFA. La autenticación multifactor con un nombre de usuario y una contraseña, además de otro método registrado, puede usarse como reserva en caso de que no puedan usar su llave de seguridad o el teléfono en algunos escenarios.

### <a name="security-key-lifecycle"></a>Ciclo de vida de las llaves de seguridad

Las llaves de seguridad permiten el acceso a los recursos y debe planear la administración de esos dispositivos físicos.

1. Distribución de las llaves: planee cómo aprovisionará las llaves en su organización. Puede tener un proceso de aprovisionamiento centralizado o permitir que los usuarios finales compren llaves compatibles con FIDO 2.0.
1. Activación de la llave: los usuarios finales deben activar automáticamente la llave de seguridad. Los usuarios finales registran sus llaves de seguridad en [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) y habilitan el segundo factor (PIN o biométrico) al usarlas por primera vez.
1. Deshabilitar una llave: aunque la funcionalidad de llave de seguridad se encuentra en la fase de versión preliminar, no hay forma de que un administrador retire una llave de una cuenta de usuario. El usuario debe hacerlo. Si se pierde o roban una llave:
   1. Quite el usuario de todos los grupos habilitados para la autenticación sin contraseña.
   1. Compruebe que ha quitado la llave como método de autenticación.
   1. Emita una nueva llave.
1. Reemplazo de llaves: los usuarios pueden habilitar dos llaves de seguridad al mismo tiempo. Al reemplazar una llave de seguridad, asegúrese de que el usuario también ha quitado la llave que se va a reemplazar.

### <a name="enable-windows-10-support"></a>Habilitación de la compatibilidad con Windows 10

Para habilitar el inicio de sesión de Windows 10 con llaves de seguridad FIDO2 es necesario habilitar la funcionalidad del proveedor de credenciales en Windows 10. Hágalo de una de estas dos maneras:

- [Habilitar proveedor de credenciales con Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - La implementación de Intune es la opción recomendada.
- [Habilitar un proveedor de credenciales con un paquete de aprovisionamiento](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Si no es posible la implementación de Intune, los administradores deben implementar un paquete en cada equipo para habilitar la funcionalidad del proveedor de credenciales. La instalación del paquete se puede llevar a cabo con una de las siguientes opciones:
      - Directiva de grupo o System Center Configuration Manager (SCCM)
      - Instalación local en una máquina Windows 10

### <a name="register-security-keys"></a>Registro de las llaves de seguridad

Los usuarios deben registrar su llave de seguridad en cada una de las máquinas Windows 10 unidas a Azure Active Directory.

Para más información, consulte [Registro de usuarios y administración de llaves de seguridad FIDO2](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).

### <a name="licensing-for-passwordless-authentication"></a>Licencias para la autenticación sin contraseña

No hay ningún costo adicional para la autenticación sin contraseña, aunque algunos requisitos previos pueden requerir una suscripción prémium. Consulte información detallada sobre las características y licencias en la página de licencias de [Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Desarrollo de un plan

Tenga en cuenta sus necesidades empresariales y los casos de uso de cada método de autenticación. Después, seleccione el método que mejor se adapte a sus necesidades.

### <a name="use-cases"></a>Casos de uso

En la tabla siguiente se describen los casos de uso que se van a implementar durante este proyecto.

| Ámbito | DESCRIPCIÓN |
| --- | --- |
| **Acceder** | El inicio de sesión sin contraseña está disponible desde un dispositivo personal o corporativo, dentro o fuera de la red corporativa. |
| **Auditoría** | Los datos de uso están disponibles para que los administradores auditen casi en tiempo real. <br> Los datos de uso se descargan en sistemas corporativos al menos cada 29 días o se usa la herramienta SIEM. |
| **Gobernanza** | Se ha definido y supervisado el ciclo de vida de las asignaciones de usuario al método de autenticación adecuado y a los grupos asociados. |
| **Seguridad** | El acceso al método de autenticación adecuado se controla mediante asignaciones de usuarios y grupos. <br> Solo los usuarios autorizados pueden utilizar el inicio de sesión sin contraseña. |
| **Rendimiento** | Las escalas de tiempo de propagación de asignación de acceso se documentan y supervisan. <br> Los tiempos de inicio de sesión se miden para facilitar su uso. |
| **Experiencia del usuario** | Los usuarios conocen la compatibilidad con los dispositivos móviles. <br> Los usuarios pueden configurar el inicio de sesión sin contraseñas de la aplicación Authenticator. |
| **Soporte técnico** | Los usuarios saben cómo encontrar soporte técnico para los problemas de inicio de sesión sin contraseña. |

### <a name="engage-the-right-stakeholders"></a>Interactuar con las partes interesadas adecuadas

Cuando fracasan los proyectos tecnológicos, normalmente se debe a expectativas incorrectas relacionadas con el impacto, los resultados y las responsabilidades. Para evitar estos problemas, [asegúrese de que involucra a las partes interesadas adecuadas](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) y que estas conocen bien sus roles.

### <a name="organization-communications"></a>Comunicaciones de la organización

La comunicación es fundamental para el éxito de cualquier servicio nuevo. Comunique activamente a los usuarios cómo y cuándo va a cambiar su experiencia, y cómo obtener soporte técnico si tienen problemas.

Las comunicaciones con los usuarios finales deberán incluir:

- [Habilitación de la experiencia de registro de seguridad combinado](howto-authentication-passwordless-phone.md)
- [Descarga de la aplicación Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md)
- [Registro en la aplicación Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Inicio de sesión con el teléfono](../user-help/user-help-auth-app-sign-in.md)

Microsoft proporciona [plantillas de comunicación](https://aka.ms/mfatemplates) de MFA, [plantillas de comunicación](https://www.microsoft.com/download/details.aspx?id=56768) del autoservicio de restablecimiento de contraseña (SSPR) y [documentación para el usuario final](../user-help/security-info-setup-signin.md) para ayudarle a elaborar sus comunicaciones. Puede enviar a los usuarios a [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) para que se registren directamente mediante los vínculos Información de seguridad de esa página.

### <a name="testing-passwordless"></a>Prueba del acceso sin contraseña

En cada fase de la implementación, asegúrese de que está probando que los resultados son los esperados.

#### <a name="testing-the-microsoft-authenticator-app"></a>Prueba de la aplicación Microsoft Authenticator

Los siguientes son casos de prueba de ejemplo para la autenticación sin contraseña con la aplicación Microsoft Authenticator.

| Escenario | Resultados esperados |
| --- | --- |
| El usuario puede registrar la aplicación Microsoft Authenticator | El usuario puede registrar una aplicación desde aka.ms/mysecurityinfo |
| El usuario puede habilitar el inicio de sesión con teléfono | Inicio de sesión con teléfono configurado para una cuenta profesional |
| El usuario puede acceder a una aplicación con el inicio de sesión con teléfono | El usuario pasa por el flujo de inicio de sesión con teléfono y llega a la aplicación designada. |
| Probar la reversión del registro de inicio de sesión con teléfono desactivando el inicio de sesión sin contraseña de Microsoft Authenticator en la pantalla Métodos de autenticación del portal de Azure Active Directory | Los usuarios habilitados anteriormente no pueden usar el inicio de sesión sin contraseña de Microsoft Authenticator. |
| Quitar el inicio de sesión con teléfono de la aplicación Microsoft Authenticator | La cuenta profesional ya no está disponible en Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Prueba de las llaves de seguridad

Los siguientes son casos de prueba de ejemplo para la autenticación sin contraseña con llaves de seguridad.

**Inicio de sesión con FIDO sin contraseña en dispositivos Windows 10 unidos a Azure Active Directory**

| Escenario | Resultados esperados |
| --- | --- |
| El usuario puede registrar el dispositivo FIDO2 (1809) | El usuario puede registrar el dispositivo FIDO2 en Configuración > Cuentas > Opciones de inicio de sesión > Llave de seguridad |
| El usuario puede restablecer el dispositivo FIDO2 (1809) | El usuario puede restablecer el dispositivo FIDO2 mediante el software del fabricante |
| El usuario puede iniciar sesión con el dispositivo FIDO2 (1809) | El usuario puede seleccionar la llave de seguridad en la ventana de inicio de sesión e iniciar sesión correctamente |
| El usuario puede registrar el dispositivo FIDO2 (1903) | El usuario puede registrar el dispositivo FIDO2 en Configuración > Cuentas > Opciones de inicio de sesión > Llave de seguridad |
| El usuario puede restablecer el dispositivo FIDO2 (1903) | El usuario puede restablecer el dispositivo FIDO2 en Configuración > Cuentas > Opciones de inicio de sesión > Llave de seguridad |
| El usuario puede iniciar sesión con el dispositivo FIDO2 (1903) | El usuario puede seleccionar la llave de seguridad en la ventana de inicio de sesión e iniciar sesión correctamente |

**Inicio de sesión FIDO sin contraseña en aplicaciones web de Azure AD**

| Escenario | Resultados esperados |
| --- | --- |
| El usuario puede registrar el dispositivo FIDO2 en aka.ms/mysecurityinfo con Microsoft Edge | El registro se realizará correctamente |
| El usuario puede registrar el dispositivo FIDO2 en aka.ms/mysecurityinfo con Firefox | El registro se realizará correctamente |
| El usuario puede iniciar sesión en OneDrive en línea mediante el dispositivo FIDO2 con Microsoft Edge | El inicio de sesión se realizará correctamente |
| El usuario puede iniciar sesión en OneDrive en línea mediante el dispositivo FIDO2 con Firefox | El inicio de sesión se realizará correctamente |
| Prueba de reversión del registro de dispositivos FIDO2 desactivando las llaves de seguridad FIDO2 en la hoja Métodos de autenticación del portal de Azure Active Directory | Se solicitará a los usuarios que inicien sesión con su llave de seguridad, se registrarán correctamente y se mostrará un error: "La directiva de la empresa requiere que use otro método para iniciar sesión". Los usuarios podrán seleccionar un método diferente e iniciar sesión correctamente. Cierre la ventana e inicie sesión de nuevo para comprobar que no ven el mismo mensaje de error. |

### <a name="auditing-passwordless"></a>Auditoría de contraseñas

Azure AD tiene informes que proporcionan información técnica y empresarial. Haga que los propietarios de las aplicaciones empresariales y técnicas asuman la propiedad de estos informes y los consuman en función de los requisitos de su organización.

La sección Métodos de autenticación del portal de Azure Active Directory es el lugar donde los administradores pueden habilitar y administrar la configuración de las credenciales sin contraseña.

Azure AD agrega entradas a los registros de auditoría cuando:

- Un administrador realiza cambios en la sección Métodos de autenticación.
- Un usuario realiza cualquier tipo de cambio en sus credenciales dentro de Azure Active Directory.

En la tabla siguiente se proporcionan algunos ejemplos de escenarios de informes típicos.

|   | Administración de los riesgos. | Aumento de la productividad | Gobernanza y cumplimiento |
| --- | --- | --- | --- |
| **Tipos de informes** | Métodos de autenticación: usuarios registrados para el registro de seguridad combinado | Métodos de autenticación: usuarios registrados para la notificación de aplicaciones | Inicios de sesión: revisar quién tiene acceso al inquilino y cómo. |
| **Acciones posibles** | Dirigirse a los usuarios no registrados todavía | Impulsar la adopción de la aplicación Microsoft Authenticator o llaves de seguridad | Revocar el acceso o aplicar directivas de seguridad adicionales para administradores |

**Azure AD conserva la mayoría de los datos de auditoría durante 30 días** y hace que estén disponibles en el portal de administración de Azure o una API para que pueda descargarlos a sus sistemas de análisis. Si su organización requiere una retención más prolongada, los registros se deben exportar y consumir en una herramienta SIEM, como [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk o Sumo Logic. [Obtenga más información sobre cómo ver los informes de uso y acceso](../reports-monitoring/overview-reports.md).

Los usuarios pueden registrar y administrar sus credenciales en [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Este vínculo dirige a los usuarios a la experiencia de administración de credenciales de usuario final que se ha habilitado combinando la experiencia de registro de SSPR y MFA. Cualquier registro de dispositivos de seguridad FIDO2 o cambios en los métodos de autenticación por parte de un usuario se registra en los registros de auditoría de Azure Active Directory.

Cuando los usuarios habilitan o deshabilitan la cuenta en una llave de seguridad o restablecen el segundo factor de la llave de seguridad en sus máquinas Windows 10, se agrega una entrada al registro de seguridad, que se encuentra bajo los siguientes identificadores de eventos: 4670, 5382.

### <a name="plan-for-rollback"></a>Planeación para la reversión

Aunque la autenticación sin contraseña es una característica ligera con un impacto mínimo en los usuarios finales, puede que sea necesario revertirla.

Para ello, es necesario que el administrador inicie sesión en el portal de Azure Active Directory, seleccione los métodos de autenticación seguros que desee y cambie la opción Habilitar a "No". Este proceso desactivará la funcionalidad sin contraseña para todos los usuarios.

A los usuarios que ya han registrado dispositivos de seguridad FIDO2 se les pedirá que utilicen el dispositivo de seguridad en el siguiente inicio de sesión y, a continuación, verán el siguiente error:

![elija otra manera de iniciar sesión](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Planeación de pruebas piloto

Al implementar la autenticación sin contraseña, debe habilitar primero uno o varios grupos piloto. Puede [crear grupos](../fundamentals/active-directory-groups-create-azure-portal.md) específicamente para este propósito. Agregue a los grupos los usuarios que van a participar en la prueba piloto. A continuación, habilite los nuevos métodos de autenticación sin contraseña para los grupos seleccionados.

Los grupos se pueden sincronizar desde un directorio local o desde Azure AD. Cuando esté satisfecho con los resultados de la prueba piloto, puede activar la autenticación sin contraseña para todos los usuarios.

Consulte [Procedimiento recomendado para una prueba piloto](https://aka.ms/deploymentplans) en la página de planes de implementación.

## <a name="deploy-passwordless-authentication"></a>Implementación de autenticación sin contraseña

Siga los pasos que se indican a continuación para el método elegido.

### <a name="required-administrative-roles"></a>Roles administrativos requeridos

| Rol de Azure AD | DESCRIPCIÓN |
| --- | --- |
| Administrador de autenticación | Rol con menos privilegios capaz de implementar y administrar métodos de autenticación. |
| Usuario | Rol con menos privilegios para configurar la aplicación Authenticator en el dispositivo o para inscribir el dispositivo de llave de seguridad para el inicio de sesión web o Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Implementación de inicio de sesión con teléfono con la aplicación Microsoft Authenticator

Siga los pasos del artículo [Habilitación del inicio de sesión sin contraseña con la aplicación Microsoft Authenticator](howto-authentication-passwordless-phone.md) para habilitar la aplicación de Microsoft Authenticator como método de autenticación sin contraseña en la organización.

### <a name="deploy-fido2-security-key-sign-in"></a>Implementación de inicio de sesión con llave de seguridad FIDO2

Siga los pasos descritos en el artículo [Habilitación del inicio de sesión sin contraseña con llave de seguridad en Azure AD](howto-authentication-passwordless-security-key.md) para habilitar llaves de seguridad FIDO2 como métodos de autenticación sin contraseña en su organización.

### <a name="troubleshoot-phone-sign-in"></a>Solución de problemas de inicio de sesión con teléfono

| Escenario | Solución |
| --- | --- |
| El usuario no puede realizar el registro combinado | Asegúrese de que el [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| El usuario no puede habilitar el inicio de sesión con teléfono en la aplicación Authenticator | Asegúrese de que el usuario está en el ámbito de la implementación. |
| El usuario NO está en el ámbito de la autenticación sin contraseña, pero se le ofrece la opción de inicio de sesión sin contraseña, que no puede completar. | Este escenario se produce cuando el usuario ha habilitado el inicio de sesión con teléfono en la aplicación antes de que se haya creado la directiva. <br> Para habilitar el inicio de sesión: Agregue el usuario al ámbito de los usuarios habilitados para el inicio de sesión sin contraseña. <br> Para bloquear el inicio de sesión: haga que el usuario quite sus credenciales de la aplicación. |

### <a name="troubleshoot-security-key-sign-in"></a>Solución de problemas de inicio de sesión con llave de seguridad

| Escenario | Solución |
| --- | --- |
| El usuario no puede realizar el registro combinado | Asegúrese de que el [registro combinado](concept-registration-mfa-sspr-combined.md) está habilitado. |
| El usuario no puede agregar una llave de seguridad a su [configuración de seguridad](https://aka.ms/mysecurityinfo) | Asegúrese de que las [llaves de seguridad](howto-authentication-passwordless-security-key.md) estén habilitadas. |
| El usuario no puede agregar una llave de seguridad en las opciones de inicio de sesión de Windows 10 | [Asegúrese de que las llaves de seguridad para el inicio de sesión de Windows](howto-authentication-passwordless-enable.md) estén habilitadas. |
| **Mensaje de error**: Hemos detectado que este sistema operativo o explorador no admite llaves de seguridad FIDO2. | Los dispositivos de seguridad FIDO2 sin contraseña solo se pueden registrar en exploradores compatibles (Microsoft Edge, Firefox versión 67) en Windows 10 versión 1809 o posteriores. |
| **Mensaje de error**: La directiva de la empresa requiere que use otro método para iniciar sesión. | No está seguro de si las llaves de seguridad están habilitadas en el inquilino. |
| El usuario no puede administrar mi llave de seguridad en la versión 1809 de Windows 10 | La versión 1809 requiere que use el software de administración de llaves de seguridad proporcionado por el proveedor de llaves FIDO2. Póngase en contacto con el proveedor para obtener soporte técnico. |
| Creo que mi llave de seguridad FIDO2 puede ser defectuosa, ¿cómo puedo probarla? | Vaya a [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), escriba las credenciales de una cuenta de prueba, conecte la llave de seguridad sospechosa, haga clic en el botón "+" situado en la parte superior derecha de la pantalla, haga clic en crear y continúe con el proceso de creación. Si se produce un error en este escenario, es posible que el dispositivo esté defectuoso. |

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación del inicio de sesión sin contraseña con llaves de seguridad en Azure AD](howto-authentication-passwordless-security-key.md)
- [Habilitación del inicio de sesión sin contraseña con la aplicación Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Más información sobre el uso de métodos de autenticación](howto-authentication-methods-usage-insights.md)
