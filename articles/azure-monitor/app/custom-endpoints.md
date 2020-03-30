---
title: Azure Application Insights åsidosätter standard-SDK-slutpunkter
description: Ändra standard-Azure Monitor Application Insights SDK-slutpunkter för regioner som Azure Government.
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: b4ab05c7ee815b385ffb2d1ff9e621063d744dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298326"
---
# <a name="application-insights-overriding-default-endpoints"></a>Programstatistik åsidosätter standardslutpunkter

Om du vill skicka data från Application Insights till vissa regioner måste du åsidosätta standardslutpunktsadresserna. Varje SDK kräver lite olika ändringar, som alla beskrivs i den här artikeln. Dessa ändringar kräver att exempelkoden justeras och `QuickPulse_Endpoint_Address` `TelemetryChannel_Endpoint_Address`att `Profile_Query_Endpoint_address` platshållarvärdena för , och med de faktiska slutpunktsadresserna för din specifika region. Slutet av den här artikeln innehåller länkar till slutpunktsadresser för regioner där den här konfigurationen krävs.

## <a name="sdk-code-changes"></a>Ändringar av SDK-kod

### <a name="net-with-applicationinsightsconfig"></a>.NET med applicationinsights.config

> [!NOTE]
> Filen applicationinsights.config skrivs automatiskt över när som helst en SDK-uppgradering utförs. När du har utfört en SDK-uppgradering ska du ange regionspecifika slutpunktsvärden igen.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>Custom_QuickPulse_Endpoint_Address</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>Profile_Query_Endpoint_address</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

Ändra filen appsettings.json i projektet på följande sätt för att justera huvudslutpunkten:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Värdena för livemått och slutpunkten för profilfråga kan endast anges via kod. Om du vill åsidosätta standardvärdena för alla slutpunktsvärden `Startup.cs` via kod gör du följande ändringar i metoden för `ConfigureServices` filen:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

### <a name="azure-functions-v2x"></a>Azure-funktioner v2.x

Installera följande paket i funktionsprojektet:

- Microsoft.ApplicationInsights version 2.10.0
- Version 2.10.0 för Microsoft.ApplicationInsights.PerfCounterCollector
- Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel version 2.10.0

Lägg sedan till (eller ändra) startkoden för funktionsprogrammet:

```csharp
[assembly: WebJobsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IWebJobsBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "QuickPulse_Endpoint_Address"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });
      }
  }
}
```

### <a name="java"></a>Java

Ändra filen applicationinsights.xml om du vill ändra standardslutpunktsadressen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>TelemetryChannel_Endpoint_Address</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Ändra `application.properties` filen och lägg till:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Slutpunkterna kan också konfigureras via miljövariabler:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      endpointUrl: "TelemetryChannel_Endpoint_Address"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="python"></a>Python

För vägledning om hur du ändrar slutpunkten för intag för opencensus-python SDK finns i [opencensus-python repo.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

## <a name="regions-that-require-endpoint-modification"></a>Regioner som kräver ändring av slutpunkt

För närvarande är de enda regioner som kräver slutpunktsändringar [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights) och [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide).

|Region |  Namn på slutpunkt | Värde |
|-----------------|:------------|:-------------|
| Azure Kina | Telemetrikanal | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure Kina | QuickPulse (Live Metrics) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure Kina | Profilfråga |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Telemetrikanal |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Live Metrics) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profilfråga |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Om du för närvarande använder [REST-API:et för Application Insights](https://dev.applicationinsights.io/
) som normalt nås via "api.applicationinsights.io" måste du använda en slutpunkt som är lokal för din region:

|Region |  Namn på slutpunkt | Värde |
|-----------------|:------------|:-------------|
| Azure Kina | REST API | `api.applicationinsights.azure.cn` |
| Azure Government | REST API | `api.applicationinsights.us`|

> [!NOTE]
> Kodlös agent/tilläggsbaserad övervakning för Azure App Services **stöds för närvarande inte** i dessa regioner. Så snart den här funktionen blir tillgänglig kommer den här artikeln att uppdateras.

## <a name="next-steps"></a>Nästa steg

- Mer information om anpassade ändringar för Azure Government finns i detaljerad vägledning för [Azure-övervakning och hantering](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
- Mer information om Azure China finns i [Azure China Playbook](https://docs.microsoft.com/azure/china/).
