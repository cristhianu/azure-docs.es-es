---
title: Correlación de telemetría de Azure Application Insights | Microsoft Docs
description: Correlación de Telemetría de Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: bcdc6633980ec3684217c8c19b4799befe2af3a3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576869"
---
# <a name="telemetry-correlation-in-application-insights"></a>Correlación de Telemetría en Application Insights

En el mundo de los microservicios, todas las operaciones lógicas requieren que el trabajo se realice en diversos componentes del servicio. Cada uno de estos componentes puede supervisarse por separado mediante [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights admite la correlación de telemetría distribuida, que se usa para detectar el componente que es responsable de los errores o de una degradación del rendimiento.

Este artículo explica el modelo de datos utilizado por Application Insights para poner en correlación la telemetría enviada por varios componentes. También se explican los protocolos y las técnicas de propagación contextual, así como los conceptos de correlación en distintos lenguajes y plataformas.

## <a name="data-model-for-telemetry-correlation"></a>Modelo de datos de correlación de telemetría

Application Insights define el [modelo de datos](../../azure-monitor/app/data-model.md) para la correlación de telemetría distribuida. Para asociar la telemetría con la operación lógica, todos los elementos de telemetría tienen el campo de contexto denominado `operation_Id`. Todos los elementos de telemetría comparten este identificador en el seguimiento distribuido. De este modo, aun con la pérdida de telemetría de una sola capa, se pueden seguir asociando los datos telemétricos notificados por otros componentes.

Normalmente, una operación lógica distribuida consta de un conjunto de operaciones más pequeñas, que son solicitudes que se procesan en uno de los componentes. Estas operaciones se definen mediante [telemetría de solicitudes](../../azure-monitor/app/data-model-request-telemetry.md). Cada telemetría de solicitudes cuenta con su propio `id`, que la identifica de forma global e inequívoca. Además, todos los elementos de telemetría (como los seguimientos y las excepciones) que están asociados a dicha solicitud deben establecer `operation_parentId` en el valor de la solicitud `id`.

La [telemetría de dependencias](../../azure-monitor/app/data-model-dependency-telemetry.md) representa a todas las operaciones salientes, como una llamada HTTP a otro componente. La telemetría de dependencia también define su propio `id`, que es globalmente único. La telemetría de solicitudes, que se inicia mediante esta llamada de dependencia, utiliza este `id` como su `operation_parentId`.

Puede crear una vista de la operación lógica distribuida usando `operation_Id`, `operation_parentId` y `request.id` con `dependency.id`. Estos campos también definen el orden de causalidad de las llamadas de telemetría.

En el entorno de los microservicios, los seguimientos de componentes pueden ir a distintos elementos de almacenamiento. Cada componente puede tener su propia clave de instrumentación en Application Insights. Para obtener la telemetría de la operación lógica, la experiencia del usuario de Application Insights consulta los datos de cada elemento de almacenamiento. Si el número de elementos de almacenamiento es enorme, necesitará una pista para saber dónde debe mirar a continuación. El modelo de datos de Application Insights define dos campos, `request.source` y `dependency.target`, para solucionar este problema. El primer campo identifica el componente que inició la solicitud de dependencia y, el segundo, el componente que devolvió la respuesta de la llamada de dependencia.

## <a name="example"></a>Ejemplo

Tomemos como ejemplo una aplicación llamada Stock Prices, en la que el precio de mercado actual de una acción se muestra utilizando una API externa denominada `Stock`. La aplicación Stock Prices tiene una página llamada `Stock page` que el explorador web del cliente abre mediante `GET /Home/Stock`. La aplicación consulta la API `GET /api/stock/value` utilizando la llamada HTTP `Stock`.

Puede analizar la telemetría resultante ejecutando una consulta:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

En los resultados, observe que todos los elementos de telemetría comparten la raíz `operation_Id`. Cuando se realiza una llamada Ajax desde la página, se asigna un nuevo identificador único (`qJSXU`) a la telemetría de dependencia y el identificador de pageView se usa como `operation_ParentId`. La solicitud al servidor utiliza después el identificador de Ajax como `operation_ParentId`.

| itemType   | Nombre                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Cuando se realiza la llamada `GET /api/stock/value` a un servicio externo, necesita conocer la identidad de ese servidor para poder establecer el campo `dependency.target` correctamente. Si el servicio externo no admite la supervisión, `target` se establece en el nombre de host del servicio (por ejemplo, `stock-prices-api.com`). Sin embargo, si ese servicio se identifica devolviendo un encabezado HTTP predefinido, `target` contiene la identidad de servicio que permite a Application Insights crear un seguimiento distribuido consultando la telemetría de ese servicio.

## <a name="correlation-headers"></a>Encabezados de correlación

Haremos la transición a la especificación [Trace-Context de W3C](https://w3c.github.io/trace-context/) que define:

- `traceparent`: lleva el identificador único global de la operación y un identificador único de la llamada.
- `tracestate`: lleva un contexto específico del sistema de seguimiento.

Las versiones más recientes de los SDK de Application Insights admiten el protocolo de Trace-Context, pero es posible que tenga que usarlo (este mantendrá la compatibilidad con el antiguo protocolo de correlación compatible con los SDK de ApplicationInsights).

El [protocolo HTTP de correlación, también conocido como Request-Id](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md), está en desuso. Este protocolo define dos encabezados:

- `Request-Id`: lleva el identificador único global de la llamada.
- `Correlation-Context`: lleva la colección de pares nombre-valor de las propiedades del seguimiento distribuido.

Application Insights también define la [extensión](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) del protocolo HTTP de correlación. Usa pares nombre-valor `Request-Context` para propagar la colección de propiedades utilizadas por el autor o el destinatario de la llamada. El SDK de Application Insights usa este encabezado para establecer los campos `dependency.target` y `request.source`.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Habilitación de la compatibilidad con el seguimiento distribuido de W3C en aplicaciones clásicas de ASP.NET
 
  > [!NOTE]
  > No se necesita configuración a partir de `Microsoft.ApplicationInsights.Web` y `Microsoft.ApplicationInsights.DependencyCollector` 

La compatibilidad con la especificación Trace-Context de W3C se realiza de forma que sea compatible con versiones anteriores y, a su vez, se espera que la correlación funcione con aplicaciones que están instrumentadas con versiones anteriores de SDK (que no es compatible con W3C). 

Si por alguna razón quiere seguir usando el protocolo heredado `Request-Id`, puede *deshabilitar* Trace-Context con la siguiente configuración.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Si ejecuta una versión anterior del SDK, le recomendamos actualizarlo o aplicar la siguiente configuración para habilitar Trace-Context.
Esta característica está disponible en los paquetes `Microsoft.ApplicationInsights.Web` y `Microsoft.ApplicationInsights.DependencyCollector` a partir de la versión 2.8.0-beta1.
De forma predeterminada, está deshabilitada. Para habilitarla, cambie `ApplicationInsights.config`:

- En `RequestTrackingTelemetryModule` agregue el elemento `EnableW3CHeadersExtraction` con el valor establecido en `true`.
- En `DependencyTrackingTelemetryModule` agregue el elemento `EnableW3CHeadersInjection` con el valor establecido en `true`.
- Agregue `W3COperationCorrelationTelemetryInitializer` en el valor de `TelemetryInitializers` similar a 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Habilitar la compatibilidad con el seguimiento distribuido de W3C para aplicaciones de ASP.NET Core

 > [!NOTE]
  > No se necesita configuración a partir de la versión 2.8.0 de `Microsoft.ApplicationInsights.AspNetCore`.
 
La compatibilidad con la especificación Trace-Context de W3C se realiza de forma que sea compatible con versiones anteriores y, a su vez, se espera que la correlación funcione con aplicaciones que están instrumentadas con versiones anteriores de SDK (que no es compatible con W3C). 

Si por alguna razón quiere seguir usando el protocolo heredado `Request-Id`, puede *deshabilitar* Trace-Context con la siguiente configuración.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Si ejecuta una versión anterior del SDK, le recomendamos actualizarlo o aplicar la siguiente configuración para habilitar Trace-Context.

Esta característica está en la versión 2.5.0-beta1 de `Microsoft.ApplicationInsights.AspNetCore` y en la versión 2.8.0-beta1 de `Microsoft.ApplicationInsights.DependencyCollector`.
De forma predeterminada, está deshabilitada. Para habilitarla, cambie `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` a `true`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Habilitar la compatibilidad con el seguimiento distribuido de W3C para aplicaciones de Java

- **Configuración de entrada**

  - En el caso de las aplicaciones de Java EE, agregue lo siguiente a la etiqueta `<TelemetryModules>` en el archivo ApplicationInsights.xml:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - En el caso de las aplicaciones de Spring Boot, agregue las siguientes propiedades:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Configuración de salida**

  Agregue lo siguiente al archivo AI-Agent.xml:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > El modo de compatibilidad con versiones anteriores está habilitado de forma predeterminada, por lo que el `enableW3CBackCompat` parámetro es opcional. Solo debe utilizarlo cuando desee desactivar la compatibilidad con versiones anteriores.
  >
  > Lo ideal sería desactivar la compatibilidad cuando todos los servicios se hayan actualizado a la versión más reciente de los SDK compatibles con el protocolo W3C. Se recomienda encarecidamente que cambie a estos SDK más recientes lo antes posible.

> [!IMPORTANT]
> Asegúrese de que las configuraciones de entrada y salida sean exactamente iguales.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Habilitar la compatibilidad con el seguimiento distribuido de W3C para aplicaciones web

Esta característica está en `Microsoft.ApplicationInsights.JavaScript`. De forma predeterminada, está deshabilitada. Para habilitarla, use la configuración `distributedTracingMode`. AI_AND_W3C se proporciona para la compatibilidad con versiones anteriores de cualquier servicio instrumentado de Application Insights heredado:

- **Configuración de NPM (omitir si se usa Configuración de fragmentos de código)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Configuración de fragmentos de código (omitir si se usa Configuración de NPM)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing y Application Insights

La [especificación del modelo de datos de OpenTracing](https://opentracing.io/) y los modelos de datos de Application Insights se asignan de la siguiente manera:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` con `span.kind = server`                  |
| `Dependency`                          | `Span` con `span.kind = client`                  |
| `Id` de `Request` y `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` de tipo `ChildOf` (el intervalo primario)   |

Para más información, consulte [Modelo de datos de telemetría de Application Insights](../../azure-monitor/app/data-model.md). 

Para ver definiciones de los conceptos de OpenTracing, consulte [specification](https://github.com/opentracing/specification/blob/master/specification.md) y [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) de OpenTracing.

## <a name="telemetry-correlation-in-opencensus-python"></a>Correlación de los datos de telemetría en OpenCensus Python

OpenCensus Python sigue las especificaciones del modelo de datos `OpenTracing` descritas anteriormente. También admite el [contexto de seguimiento de W3C](https://w3c.github.io/trace-context/) sin necesidad de ninguna configuración.

### <a name="incoming-request-correlation"></a>Correlación de las solicitudes entrantes

OpenCensus Python correlaciona los encabezados de contexto de seguimiento de W3C de las solicitudes entrantes a los intervalos que se generan a partir de las solicitudes. OpenCensus lo hace automáticamente con integraciones para los siguientes marcos de aplicaciones web populares, como `flask`, `django` y `pyramid`. Los encabezados de contexto de seguimiento de W3C simplemente deben rellenarse con el [formato correcto](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) y enviarse con la solicitud. A continuación se muestra una aplicación de un ejemplo `flask` que muestra esto.

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

Ejecuta una aplicación `flask` de un ejemplo en la máquina local, escuchando el puerto `8080`. Para correlacionar el contexto de seguimiento, se envía una solicitud al punto de conexión. En este ejemplo, se puede usar un comando `curl`.
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
Al examinar el [formato de encabezado de contexto de seguimiento](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format), se deriva la siguiente información: `version`: `00`
`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`
`parent-id/span-id`: `00f067aa0ba902b7`
`trace-flags`: `01`

Si echamos un vistazo a la entrada de solicitud que se envió a Azure Monitor, podemos ver los campos rellenados con la información de encabezado de seguimiento. Puede encontrar estos datos en Registros (Analytics) en el recurso Application Insights de Azure Monitor.

![Captura de pantalla de los datos de telemetría de solicitudes en Logs Analytics, con los campos de encabezado de seguimiento resaltados en el cuadro rojo](./media/opencensus-python/0011-correlation.png)

El campo `id` está en el formato `<trace-id>.<span-id>`, donde el `trace-id` se toma del encabezado de seguimiento que se pasó en la solicitud y el `span-id` es una matriz de 8 bytes generada para este intervalo. 

El campo `operation_ParentId` tiene el formato `<trace-id>.<parent-id>`, donde tanto `trace-id` como `parent-id` se toman del encabezado de seguimiento que se pasó en la solicitud.

### <a name="logs-correlation"></a>Correlación de los registros

OpenCensus Python permite la correlación de registros al enriquecer las entradas de registro con el identificador de seguimiento, el identificador de intervalo y la marca de muestreo. Para ello, se realiza la instalación de [la integración del registro](https://pypi.org/project/opencensus-ext-logging/) de OpenCensus. Los atributos siguientes se agregarán a los `LogRecord`de Python: `traceId`, `spanId` y `traceSampled`. Tenga en cuenta que esto solo se aplica a los registradores creados después de la integración.
A continuación se muestra una aplicación de ejemplo que muestra esto.

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
Cuando se ejecuta este código, se obtiene lo siguiente en la consola:
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
Observe cómo hay un identificador spanId presente para el mensaje de registro que se encuentra dentro del intervalo, que es el mismo spanId que pertenece al intervalo denominado `hello`.

Puede exportar los datos de registro mediante `AzureLogHandler`. Puede encontrar más información [aquí](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python#logs)

## <a name="telemetry-correlation-in-net"></a>Correlación de telemetría en .NET

Con el tiempo, .NET ha establecido diversas formas para establecer correspondencias entre la telemetría y los registros de diagnósticos:

- `System.Diagnostics.CorrelationManager` permite realizar un seguimiento de [LogicalOperationStack y ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` y Seguimiento de eventos para Windows (ETW) definen el método [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx).
- `ILogger` usa [ámbitos de registro](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) y HTTP conectan la propagación del contexto "actual".

Sin embargo, esos métodos no proporcionaban compatibilidad con el seguimiento distribuido automático. `DiagnosticSource` es un mecanismo para admitir la correlación automática entre máquinas. Las bibliotecas de .NET admiten "DiagnosticSource" y permiten la propagación automática entre máquinas del contexto de correlación mediante el transporte correspondiente; por ejemplo, HTTP.

En la [guía de Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) de `DiagnosticSource`, se explican los conceptos básicos de las actividades de seguimiento.

ASP.NET Core 2.0 admite la extracción de encabezados HTTP e inicia una nueva actividad.

A partir de la versión 4.1.0, `System.Net.Http.HttpClient` permite insertar automáticamente los encabezados HTTP de correlación y realizar un seguimiento de la llamada HTTP como actividad.

Hay un nuevo módulo HTTP, [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) para ASP.NET clásico. Este módulo implementa la correlación de telemetría mediante `DiagnosticSource`. Las actividades se inician en función de los encabezados de solicitud entrantes. También establece correlación entre los datos de telemetría de las distintas fases del procesamiento de solicitudes, incluso en los casos en los que cada fase de procesamiento de Internet Information Services (IIS) se ejecuta en un subproceso administrado diferente.

A partir de la versión 2.4.0-beta1, el SDK de Application Insights utiliza `DiagnosticSource` y `Activity` para recopilar datos telemétricos y asociarlos con la actividad actual.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Correlación de telemetría en el SDK de Java

El [SDK de Application Insights para Java](../../azure-monitor/app/java-get-started.md) permite la correlación automática de datos de telemetría desde la versión 2.0.0. Rellena automáticamente el valor de `operation_id` en todos los elementos de telemetría (seguimientos, excepciones y eventos personalizados) que se emiten en el ámbito de una solicitud. También se encarga de propagar los encabezados de correlación (descritos anteriormente) de las llamadas de un servicio de a otro mediante HTTP si el [agente del SDK de Java](../../azure-monitor/app/java-agent.md) está configurado.

> [!NOTE]
> La característica de correlación solamente admite las llamadas realizadas mediante Apache HTTPClient. Si usa Spring RestTemplate o Feign, los dos pueden utilizarse con Apache HTTPClient en segundo plano.

En la actualidad, no se admite la propagación automática de contextos entre tecnologías de mensajería (como Kafka, RabbitMQ o Azure Service Bus). Sin embargo, es posible codificar estos escenarios manualmente utilizando las API `trackDependency` y `trackRequest`. En estas API, la telemetría de dependencias representa un mensaje agregado a una cola por un productor, mientras que la solicitud representa un mensaje procesado por el consumidor. En este caso, tanto `operation_id` como `operation_parentId` deben propagarse en las propiedades del mensaje.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Correlación de la telemetría en una aplicación de Java asincrónica

Para establecer la correlación de la telemetría en la aplicación asincrónica Spring Boot, siga [este](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) artículo detallado. Proporciona orientación para la instrumentación de [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) y [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html) en Spring. 


<a name="java-role-name"></a>
## <a name="role-name"></a>Nombre de rol

Es posible que, en ocasiones, quiera personalizar el modo en que los nombres de los componentes aparecen en el [mapa de aplicación](../../azure-monitor/app/app-map.md). Para ello, puede establecer manualmente el valor de `cloud_RoleName` a través de uno de los siguientes mecanismos:

- A partir del SDK de Java 2.5.0 para Application Insights, puede especificar el nombre del rol en la nube si agrega `<RoleName>` al archivo `ApplicationInsights.xml`, por ejemplo,

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Si usa Spring Boot con el iniciador de Spring Boot de Application Insights, el único cambio necesario es establecer el nombre personalizado para la aplicación en el archivo application.properties.

  `spring.application.name=<name-of-app>`

  El código de inicio de Spring Boot asigna automáticamente `cloudRoleName` al valor que se especifica para la propiedad `spring.application.name`.

## <a name="next-steps"></a>Pasos siguientes

- Escriba una [telemetría personalizada](../../azure-monitor/app/api-custom-events-metrics.md).
- En el caso de escenarios avanzados de correlación en ASP.NET Core y ASP.NET, consulte el artículo sobre [seguimiento de operaciones personalizadas](custom-operations-tracking.md).
- Obtenga más información sobre la [opción cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) en otros SDK.
- Incorpore todos los componentes de un microservicio en Application Insights. Consulte las [plataformas compatibles](../../azure-monitor/app/platforms.md).
- Consulte en el [modelo de datos](../../azure-monitor/app/data-model.md) los tipos de Application Insights.
- Obtenga información sobre cómo [ampliar y filtrar la telemetría](../../azure-monitor/app/api-filtering-sampling.md).
- Consulte la [referencia de configuración de Application Insights](configuration-with-applicationinsights-config.md).
