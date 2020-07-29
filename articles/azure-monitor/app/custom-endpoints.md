---
title: Azure Application Insights åsidosätter standard-SDK-slutpunkter
description: Ändra standard Azure Monitor Application Insights SDK-slutpunkter för regioner som Azure Government.
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: references_regions, devx-track-javascript
ms.openlocfilehash: 22f81b327c161df4f6605cfcb64b48b8d75beed3
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373138"
---
# <a name="application-insights-overriding-default-endpoints"></a>Application Insights som åsidosätter standard slut punkter

Om du vill skicka data från Application Insights till vissa regioner måste du åsidosätta standard slut punkts adresserna. Varje SDK kräver något annorlunda ändringar, som beskrivs i den här artikeln. Dessa ändringar kräver att du justerar exempel koden och ersätter plats hållar värden för `QuickPulse_Endpoint_Address` , `TelemetryChannel_Endpoint_Address` och `Profile_Query_Endpoint_address` med de faktiska slut punkts adresserna för din specifika region. Slutet av den här artikeln innehåller länkar till slut punkts adresser för regioner där denna konfiguration krävs.

> [!NOTE]
> [Anslutnings strängar](./sdk-connection-string.md?tabs=net) är den nya bästa metoden för att ställa in anpassade slut punkter i Application Insights.

---

## <a name="sdk-code-changes"></a>Ändringar i SDK-kod

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Den applicationinsights.config filen skrivs automatiskt över när en SDK-uppgradering utförs. När du har genomfört en SDK-uppgradering måste du ange regionens angivna slut punkts värden igen.

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

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Ändra den appsettings.jsfilen i projektet på följande sätt för att justera huvud slut punkten:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "TelemetryChannel_Endpoint_Address"
    }
  }
```

Värdena för Live mått och profil frågans slut punkt kan bara anges via kod. Om du vill åsidosätta standardvärdena för alla slut punkts värden via kod gör du följande ändringar i `ConfigureServices` `Startup.cs` fil metoden:

```csharp
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse; //Place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="QuickPulse_Endpoint_Address");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "Profile_Query_Endpoint_address" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "TelemetryChannel_Endpoint_Address" });

    //Place in the ConfigureServices method. Place this before services.AddApplicationInsightsTelemetry("instrumentation key"); if it's present
```

# <a name="azure-functions"></a>[Azure Functions](#tab/functions)

För Azure Functions rekommenderar vi nu att du använder [anslutnings strängar](./sdk-connection-string.md?tabs=net) som anges i funktionens program inställningar. Om du vill komma åt program inställningar för din funktion från rutan funktioner väljer du **Inställningar**  >  **konfiguration**  >  **program inställningar**. 

Namn: `APPLICATIONINSIGHTS_CONNECTION_STRING` värde:`Connection String Value`

# <a name="java"></a>[Java](#tab/java)

Ändra den applicationinsights.xml filen om du vill ändra standard slut punkts adressen.

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

Ändra `application.properties` filen och Lägg till:

```yaml
azure.application-insights.channel.in-process.endpoint-address= TelemetryChannel_Endpoint_Address
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "TelemetryChannel_Endpoint_Address"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "Profile_Query_Endpoint_address"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "QuickPulse_Endpoint_Address"; //live metrics
appInsights.Configuration.start();
```

Slut punkterna kan också konfigureras via miljövariabler:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "Profile_Query_Endpoint_address"
Live Metrics Endpoint: "QuickPulse_Endpoint_Address"
```

# <a name="javascript"></a>[JavaScript](#tab/js)

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

# <a name="python"></a>[Python](#tab/python)

Vägledning om hur du ändrar inmatnings slut punkten för Open-inventering – python SDK finns i [openräkningar-python-lagrings platsen.](https://github.com/census-instrumentation/opencensus-python/blob/af284a92b80bcbaf5db53e7e0813f96691b4c696/contrib/opencensus-ext-azure/opencensus/ext/azure/common/__init__.py)

---

## <a name="regions-that-require-endpoint-modification"></a>Regioner som kräver slut punkts ändring

För närvarande är de enda regionerna som kräver slut punkts ändringar [Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) och [Azure Kina](/azure/china/resources-developer-guide).

|Region |  Slut punkts namn | Värde |
|-----------------|:------------|:-------------|
| Azure Kina | Telemetri kanal | `https://dc.applicationinsights.azure.cn/v2/track` |
| Azure Kina | QuickPulse (Live metrics) |`https://live.applicationinsights.azure.cn/QuickPulseService.svc` |
| Azure Kina | Profil fråga |`https://dc.applicationinsights.azure.cn/api/profiles/{0}/appId`  |
| Azure Government | Telemetri kanal |`https://dc.applicationinsights.us/v2/track` |
| Azure Government | QuickPulse (Live metrics) |`https://quickpulse.applicationinsights.us/QuickPulseService.svc` |
| Azure Government | Profil fråga |`https://dc.applicationinsights.us/api/profiles/{0}/appId` |

Om du för närvarande använder [Application Insights REST API](https://dev.applicationinsights.io/
) som normalt används via "API.applicationinsights.io" måste du använda en slut punkt som är lokal i din region:

|Region |  Slut punkts namn | Värde |
|-----------------|:------------|:-------------|
| Azure Kina | REST-API | `api.applicationinsights.azure.cn` |
| Azure Government | REST-API | `api.applicationinsights.us`|

> [!NOTE]
> Kod utan kod/tillägg baserad övervakning för Azure App-tjänster **stöds för närvarande inte** i dessa regioner. Så snart den här funktionen blir tillgänglig kommer den här artikeln att uppdateras.

## <a name="next-steps"></a>Nästa steg

- Mer information om anpassade ändringar för Azure Government finns i den detaljerade vägledningen för [Azure-övervakning och-hantering](../../azure-government/compare-azure-government-global-azure.md#application-insights).
- Mer information om Azure Kina finns i [Azure Kina-Spelbok](/azure/china/).
