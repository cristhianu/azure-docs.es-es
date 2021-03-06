---
title: Uso de Microsoft Authenticator o el Portal de empresa de Microsoft Intune en aplicaciones de Xamarin iOS y Android | Azure
description: Aprenda a migrar aplicaciones de Xamarin iOS que pueden usar Microsoft Authenticator de la biblioteca de autenticación de Azure AD para .NET (ADAL.NET) a la biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: abac7b95ceed0a199531b7ba30cea5fc1c9cc1e0
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103970"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Uso de Microsoft Authenticator o el Portal de empresa de Microsoft Intune en aplicaciones de Xamarin

En iOS y Android, agentes como Microsoft Authenticator o el Portal de empresa de Microsoft Intune habilitan lo siguiente (solo Android):

- Inicio de sesión único (SSO). Los usuarios no tendrán que iniciar sesión en cada aplicación.
- Identificación de dispositivos. El agente accede al certificado del dispositivo que se creó en este al unirse al área de trabajo.
- Comprobación de identificación de la aplicación. Cuando una aplicación llama al agente, pasa su dirección URL de redireccionamiento y el agente la comprueba.

Para habilitar una de estas características, los desarrolladores de aplicaciones deben usar el parámetro `WithBroker()` al llamar al método `PublicClientApplicationBuilder.CreateApplication`. `.WithBroker()` se establece en true de forma predeterminada. Además, los desarrolladores deben seguir los pasos que se indican aquí para las aplicaciones [iOS](#brokered-authentication-for-ios) o [Android](#brokered-authentication-for-android).

## <a name="brokered-authentication-for-ios"></a>Autenticación asincrónica para iOS

Siga estos pasos para permitir que la aplicación de Xamarin.iOS establezca comunicación con la aplicación [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="step-1-enable-broker-support"></a>Paso 1: Habilitar la compatibilidad con el agente
La compatibilidad con el agente se habilita por PublicClientApplication. De forma predeterminada, está deshabilitada. Use el parámetro `WithBroker()` (establecido en true de forma predeterminada) al crear PublicClientApplication a través de PublicClientApplicationBuilder.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Paso 2: Actualizar AppDelegate para controlar la devolución de llamada
Cuando la biblioteca de autenticación de Microsoft para .NET (MSAL.NET) llama al agente, el agente devuelve la llamada a la aplicación mediante el método `OpenUrl` de la clase `AppDelegate`. Como MSAL espera la respuesta del agente, la aplicación debe cooperar para volver a llamar a MSAL.NET. Para permitir esta cooperación, actualice el archivo `AppDelegate.cs` para invalidar el método siguiente.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {               
         return false;                
    }
    
    return true;     
}           
```

Este método se invoca cada vez que se inicia la aplicación. Se usa como una oportunidad para procesar la respuesta del agente y finalizar el proceso de autenticación iniciado por MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Paso 3: Establecer un UIViewController()
En el archivo `AppDelegate.cs`, deberá establecer una ventana de objeto. Normalmente, con Xamarin iOS, no es necesario hacerlo. Para enviar y recibir respuestas del agente, se necesita una ventana de objeto. 

Para ello, haga dos cosas. 
1. En `AppDelegate.cs`, establezca `App.RootViewController` en un nuevo `UIViewController()`. Esta asignación garantiza que hay un objeto UIViewController con la llamada al agente. Si no se establece correctamente, puede obtener este error: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`.
1. En la llamada a AcquireTokenInteractive, use `.WithParentActivityOrWindow(App.RootViewController)` y pase la referencia a la ventana de objeto que usará.

**Por ejemplo:**

En `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
En `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
En la llamada al token de adquisición:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-4-register-a-url-scheme"></a>Paso 4: Registrar un esquema de dirección URL
MSAL.NET usa direcciones URL para invocar al agente y devolver la respuesta del agente a la aplicación. Para finalizar el recorrido de ida y vuelta, registre un esquema de dirección URL para la aplicación en el archivo `Info.plist`.

El nombre de `CFBundleURLSchemes` debe incluir un prefijo `msauth.`, seguido de su `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Por ejemplo:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Este esquema de dirección URL se convierte en parte del URI de redirección que se usa para identificar de forma única la aplicación cuando recibe la respuesta del agente.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Paso 5: Agregar el identificador de agente a la sección LSApplicationQueriesSchemes
MSAL usa `–canOpenURL:` para comprobar si el agente está instalado en el dispositivo. En iOS 9, Apple ha bloqueado los esquemas que puede consultar una aplicación. 

Agregue `msauthv2` a la sección `LSApplicationQueriesSchemes` del archivo `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>Paso 6: Registrar el URI de redirección en el portal de aplicaciones
El uso del agente agrega un requisito adicional al URI de redirección. El URI de redirección _debe_ tener el siguiente formato:
```CSharp
$"msauth.{BundleId}://auth"
```
**Por ejemplo:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Observe que el URI de redirección coincide con el nombre `CFBundleURLSchemes` que incluyó en el archivo `Info.plist`.

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Paso 7: Asegurarse de que el URI de redirección está registrado en la aplicación

Este URI de redirección debe estar registrado en el portal de registro de aplicaciones (https://portal.azure.com) con un valor válido para la aplicación. 

El portal cuenta con una nueva experiencia de registro de aplicaciones para ayudarle a calcular el URI de respuesta asincrónica a partir del identificador de paquete.

1. En el registro de aplicaciones, elija **Autenticación** y seleccione **Probar la nueva experiencia**.

   ![Probar la nueva experiencia de registro de aplicaciones](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Seleccione **Agregar una plataforma**.

   ![Agregar una plataforma](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Cuando se admita la lista de plataformas, seleccione **iOS**.

   ![Configurar iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Escriba el identificador del conjunto que se solicita y, luego, presione **Configurar**.

   ![Escribir el identificador del conjunto](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. El URI de redirección se calcula automáticamente.

   ![Copiar el URI de redirección](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Autenticación asincrónica para Android

La compatibilidad con el agente no está disponible en Android.

## <a name="next-steps"></a>Pasos siguientes

Lea el artículo [Consideraciones específicas de la Plataforma universal de Windows con MSAL.NET](msal-net-uwp-considerations.md).