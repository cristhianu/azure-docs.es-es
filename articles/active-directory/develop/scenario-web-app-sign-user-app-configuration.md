---
title: 'Aplicación web que inicia la sesión de los usuarios (configuración del código): Plataforma de identidad de Microsoft'
description: Obtener información sobre cómo crear una aplicación web que inicie la sesión de los usuarios (configuración del código)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f558ecf583c96f36b8bbee19c7c9cbb2ee57aa31
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596730"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplicación web que inicia la sesión de los usuarios: configuración del código

Obtenga información sobre cómo configurar el código para la aplicación web que inicia la sesión de los usuarios.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotecas utilizadas para proteger Web Apps

<!-- This section can be in an include for Web App and Web APIs -->
Las bibliotecas utilizadas para proteger una aplicación web (y una API web) son:

| Plataforma | Biblioteca | DESCRIPCIÓN |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensiones de modelo de identidad para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Utilizadas directamente por ASP.NET y ASP.NET Core, Microsoft Identity Extensions para .NET propone un conjunto de DLL que se ejecutan tanto en .NET Framework como en .NET Core. Desde una aplicación web de ASP.NET o ASP.NET Core, puede controlar la validación del token mediante la clase **TokenValidationParameters** (en concreto, en algunos escenarios de ISV) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL para Java está actualmente en versión preliminar pública |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL para Python está actualmente en versión preliminar pública |

Seleccione la pestaña correspondiente a la plataforma que le interese:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Los fragmentos de código de este artículo y los siguientes se extraen del [capítulo 1 del tutorial incremental de aplicaciones web de ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Es posible que desee consultar este tutorial para obtener detalles completos de la implementación.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Los fragmentos de código de este artículo y de los siguientes se han extraído del [ejemplo de aplicación web de ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Es posible que desee consultar dicho ejemplo para obtener detalles completos de la implementación.

# <a name="javatabjava"></a>[Java](#tab/java)

Los fragmentos de código de este artículo y los siguientes se extraen del ejemplo de aplicación web msal4j de [aplicación web de Java que llama a Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp).

Es posible que desee consultar dicho ejemplo para obtener detalles completos de la implementación.

# <a name="pythontabpython"></a>[Python](#tab/python)

Los fragmentos de código de este artículo y los siguientes se extraen del ejemplo de aplicación web MSAL.Python de [aplicación web de Python que llama a Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp).

Es posible que desee consultar dicho ejemplo para obtener detalles completos de la implementación.

---

## <a name="configuration-files"></a>Archivos de configuración

Las aplicaciones web en las que los usuarios inician sesión con la plataforma de identidad de Microsoft se configuran normalmente mediante archivos de configuración. La configuración que debe rellenar es:

- la nube `Instance` si desea que la aplicación se ejecute en las nubes nacionales, por ejemplo
- el público en `tenantId`
- `clientId` para la aplicación, tal y como se copió de Azure Portal.

Algunas veces, las aplicaciones se pueden parametrizar mediante `authority`, que constituye la concatenación de `instance` y `tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

En ASP.NET Core, estos valores se encuentran en el archivo [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8), en la sección "AzureAD".

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

En ASP.NET Core, hay otro archivo [properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) que contiene la dirección URL (`applicationUrl`) y el puerto SSL (`sslPort`) de la aplicación, así como diversos perfiles.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

En Azure Portal, los URI de respuesta que necesita registrar en la página **Autenticación** de la aplicación deben coincidir con estas direcciones URL; es decir, para los dos archivos de configuración anteriores, serían `https://localhost:44321/signin-oidc`, ya que applicationUrl es `http://localhost:3110` pero `sslPort` se especifica (44321) y `CallbackPath` es `/signin-oidc` tal y como se define en `appsettings.json`.
  
De la misma manera, el URI de cierre de sesión se establecería en `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

En ASP.NET, la aplicación se configura en las líneas 12 a 15 del archivo [Web.Config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15).

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

En Azure Portal, los identificadores URI de respuesta que debe registrar en la página de **autenticación** de la aplicación deben coincidir con estas direcciones URL; es decir, con `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

En Java, la configuración se encuentra en el archivo [Application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) que está en `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

En Azure Portal, los identificadores URI de respuesta que debe registrar en la página de **autenticación** de la aplicación deben coincidir con los identificadores redirectUris definidos por la aplicación, es decir, con `http://localhost:8080/msal4jsample/secure/aad` y `http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

El archivo de configuración de Python es [app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> En este inicio rápido se va a almacenar el secreto de cliente en el archivo de configuración para que sea más sencillo. En la aplicación de producción, querrá usar otras maneras de almacenar el secreto, como KeyVault, o una variable de entorno, tal como se describe en la documentación de Flask: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Código de inicialización

El código de inicialización será diferente según la plataforma. En el caso de ASP.NET Core y ASP.NET, el inicio de sesión de los usuarios se delega en el middleware OpenIDConnect. En nuestro caso, la plantilla de ASP.NET y ASP.NET Core genera aplicaciones web para el punto de conexión de Azure AD v1.0. Por lo tanto, se necesita algo de configuración para adaptarlos al punto de conexión de la plataforma de identidad de Microsoft (v2.0). En el caso de Java, se controla mediante Spring con la ayuda de la aplicación.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

En las aplicaciones web de ASP.NET Core (y API web), la aplicación está protegida porque el usuario dispone de un atributo `[Authorize]` en los controladores o en las acciones de estos. Este atributo comprueba que el usuario está autenticado. El código que realiza la inicialización de la aplicación se encuentra en el archivo `Startup.cs` y, para agregar la autenticación con la Plataforma de identidad de Microsoft (anteriormente, Azure AD v2.0), necesitará agregar el siguiente código. Los comentarios que se encuentren en el código deben ser claros.

  > [!NOTE]
  > Si inicia el proyecto con el proyecto web de ASP.NET Core predeterminado dentro de Visual Studio o mediante `dotnet new mvc`, el método `AddAzureAD` está disponible de forma predeterminada porque los paquetes relacionados se cargan automáticamente.
  > Sin embargo si crea un proyecto desde cero e intenta usar el siguiente código, le recomendamos que agregue el paquete NuGet **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** al proyecto para hacer que el método `AddAzureAD` esté disponible.
  
El siguiente código está disponible en [Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);
  
      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication` es un método de extensión que está definido en [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Este:

- permite agregar el servicio de autenticación
- configura las opciones para leer el archivo de configuración
- configura las opciones de OpenIDConnect para que la autoridad usada sea el punto de conexión de la plataforma de identidad de Microsoft (anteriormente Azure AD v2.0)
- valida el emisor del token
- asigna las notificaciones correspondientes al nombre a partir de la notificación "preferred_username" del token de identificador 

Además de la configuración, puede especificar, cuando llame a `AddMicrosoftIdentityPlatformAuthentication`:

- el nombre de la sección de configuración (de forma predeterminada, AzureAD)
- si desea realizar un seguimiento de los eventos de middleware de OpenIdConnect, lo cual puede ayudarle a solucionar problemas de la aplicación web si la autenticación no funciona: si establece `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` en `true` le mostrará cómo se elabora la información mediante la configuración del middleware de ASP.NET Core a medida que avanza desde la respuesta HTTP a la identidad del usuario en `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

La clase `AadIssuerValidator` permite que el emisor del token se valide en muchos casos (token de v1.0 o v2.0, aplicación de un solo inquilino, aplicación multiinquilino o aplicación en la que los usuarios inician sesión con sus cuentas personales de Microsoft, en la nube pública de Azure o en nubes nacionales). Está disponible en [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

El código relacionado con la autenticación en la aplicación web ASP.NET o las API web se encuentra en el archivo [App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61).

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

El ejemplo de Java usa Spring Framework. La aplicación está protegida porque implementa un `Filter`, que intercepta cada respuesta HTTP. En el inicio rápido de la aplicación web de Java, este es `AuthFilter` en `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. El filtro procesa el flujo del código de autorización de OAuth 2.0 y por consiguiente:

- comprueba si el usuario está autenticado (método `isAuthenticated()`)
- si el usuario no está autenticado, procesa la dirección URL de los puntos de conexión de autorización de Azure AD y redirige el explorador a este identificador URI
- cuando llega la respuesta, que contiene el flujo de código de autorización, permite a msal4j adquirir el token.
- si finalmente recibe el token del punto de conexión de token (en el URI de redirección), el usuario ha iniciado sesión.

Para más información, consulte el método `doFilter()` en [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> El código de `doFilter()` se escribe en un orden ligeramente distinto, pero el flujo es el que se describe.

Consulte [Plataforma de identidad y flujo de código de autorización de OAuth 2.0](v2-oauth2-auth-code-flow.md) para más información sobre el flujo de código de autorización que desencadena este método.

# <a name="pythontabpython"></a>[Python](#tab/python)

En el ejemplo de Python se usa Flask. La inicialización de Flask y MSAL.Python se realiza en [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Pasos siguientes

En el siguiente artículo, aprenderá a desencadenar el inicio y el cierre de sesión.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Inicio y cierre de sesión](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Inicio y cierre de sesión](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Inicio y cierre de sesión](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Inicio y cierre de sesión](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
