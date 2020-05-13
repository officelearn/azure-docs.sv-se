---
title: Anslutnings strängar i Azure Application Insights | Microsoft Docs
description: Så här använder du anslutnings strängar.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 92c4ccf7246c4e763cbf92aee3c48398d79e0ecc
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125714"
---
# <a name="connection-strings"></a>Anslutningssträngar

## <a name="overview"></a>Översikt

Anslutnings strängar ger program insikter användare med en enda konfigurations inställning, vilket eliminerar behovet av flera proxyinställningar. Mycket användbart för webb servrar i intranät, suveräna eller hybrid moln miljöer som vill skicka data till övervaknings tjänsten.

Nyckel värdes paren ger ett enkelt sätt för användare att definiera en kombination av prefixet prefix för varje Application Insights (AI)-tjänst/produkt.

> [!IMPORTANT]
> Vi rekommenderar inte att du anger både anslutnings sträng och instrument nyckel. I händelse av att en användare anger båda, prioriteras det som angetts senast. 


## <a name="scenario-overview"></a>Scenarioöversikt 

Kund scenarier där vi visualiserar detta med största påverkan:

- Brand Väggs undantag eller omdirigering av proxy 

    I de fall där övervakning av intranäts webb server krävs, uppmanade vi våra tidigare lösningar att lägga till enskilda tjänst slut punkter i konfigurationen. Mer information finns [här](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    Anslutnings strängar ger ett bättre alternativ genom att minska den här ansträngningen till en enda inställning. Ett enkelt prefix innebär att suffixet ändras tillåter automatisk ifyllning och omdirigering av alla slut punkter till rätt tjänster. 

- Suveräna miljöer eller hybrid moln miljöer

    Användare kan skicka data till en definierad [Azure Government Region](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
    Med anslutnings strängar kan du definiera slut punkts inställningar för dina intranät servrar eller hybrid moln inställningar. 

## <a name="getting-started"></a>Komma igång

### <a name="finding-my-connection-string"></a>Hittar du min anslutnings sträng?

Anslutnings strängen visas på bladet översikt i Application Insights resursen.

![bladet anslutnings sträng på Översikt](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Högsta längd

Anslutningen har en maximal längd på 4096 tecken som stöds.

#### <a name="key-value-pairs"></a>Nyckel/värde-par

Anslutnings strängen består av en lista med inställningar som representeras som nyckel/värde-par avgränsade med semikolon:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey`(t. ex.: 00000000-0000-0000-0000-000000000000)  Anslutnings strängen är ett **obligatoriskt** fält.
- `Authorization`(t. ex.: iKey) (Den här inställningen är valfri eftersom vi bara har stöd för iKey-auktorisering i dag.)
- `EndpointSuffix`(t. ex.: applicationinsights.azure.cn) Genom att ställa in Endpoint-suffixet instruerar du SDK: n som Azure-molnet ska ansluta till. SDK monterar resten av slut punkten för enskilda tjänster.
- Explicita slut punkter.
  Alla tjänster kan åsidosättas explicit i anslutnings strängen.
   - `IngestionEndpoint`(t. ex.: `https://dc.applicationinsights.azure.com` )
   - `LiveEndpoint`(t. ex.: `https://live.applicationinsights.azure.com` )
   - `ProfilerEndpoint`(t. ex.: `https://profiler.applicationinsights.azure.com` )
   - `SnapshotEndpoint`(t. ex.: `https://snapshot.applicationinsights.azure.com` )

#### <a name="endpoint-schema"></a>Slut punkts schema

`<prefix>.<suffix>`
- Prefix: definierar en tjänst. 
- Suffix: definierar det gemensamma domän namnet.

##### <a name="valid-suffixes"></a>Giltiga suffix

Här är en lista över giltiga suffix 
- applicationinsights.azure.cn
- applicationinsights.us


Se även:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Giltiga prefix

- Inmatning av [telemetri](./app-insights-overview.md):`dc`
- [Live-mått](./live-stream.md):`live`
- [Profiler](./profiler-overview.md):`profiler`
- [Ögonblicks bild](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Exempel på anslutnings strängar


### <a name="minimal-valid-connection-string"></a>Minimal giltig anslutnings sträng

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

I det här exemplet har endast Instrumentation-nyckeln angetts.

- Authorization Scheme-standardvärdet till "iKey" 
- Instrumentation-nyckel: 00000000-0000-0000-0000-000000000000
- De regionala tjänst-URI: erna baseras på [SDK-standardvärdena](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) och kommer att ansluta till den offentliga globala Azure:
   - Inmatning`https://dc.services.visualstudio.com/`
   - Live-mått:`https://rt.services.visualstudio.com/`
   - Profiler`https://agent.azureserviceprofiler.net/`
   - Fel sökare`https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>Anslutnings sträng med Endpoint-suffix

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

I det här exemplet anger den här anslutnings strängen slut punktens suffix och SDK skapar tjänstens slut punkter.

- Authorization Scheme-standardvärdet till "iKey" 
- Instrumentation-nyckel: 00000000-0000-0000-0000-000000000000
- De regionala tjänst-URI: erna baseras på angivet slut punkts suffix: 
   - Inmatning`https://dc.ai.contoso.com`
   - Live-mått:`https://live.ai.contoso.com`
   - Profiler`https://profiler.ai.contoso.com`
   - Fel sökare`https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Anslutnings sträng med explicit slut punkts åsidosättningar 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

I det här exemplet anger den här anslutnings strängen explicita åsidosättningar för varje tjänst. SDK använder de exakta slut punkter som tillhandahålls utan ändringar.

- Authorization Scheme-standardvärdet till "iKey" 
- Instrumentation-nyckel: 00000000-0000-0000-0000-000000000000
- De regionala tjänst-URI: erna baseras på de explicita värdena för åsidosättning: 
   - Inmatning`https://custom.com:111/`
   - Live-mått:`https://custom.com:222/`
   - Profiler`https://custom.com:333/`
   - Fel sökare`https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Ange en anslutnings sträng

Anslutnings strängar stöds i följande SDK-versioner:
- .NET-och .NET Core v-2.12.0
- Java v-2.5.1 och Java 3,0
- Java Script v-2.3.0
- NodeJS v-1.5.0
- Python v-1.0.0

En anslutnings sträng kan anges antingen i kod, i en miljö variabel eller i en konfigurations fil.



### <a name="environment-variable"></a>Miljövariabel

- Anslutnings sträng:`APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/. NetCore](#tab/net)

TelemetryConfiguration. ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net har uttryckligen angetts:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.Net-konfigurations fil:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config. JSON: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v 2.5. x) explicit angiven:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights. XML
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Viktigt: Java Script stöder inte användning av miljövariabler.

Använda kodfragmentet:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


Manuell installation:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Vi rekommenderar att användarna anger miljövariabeln.

Ange anslutnings strängen explicit:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Nästa steg

Kom igång under körningsfasen med:

* [Virtuella Azure-datorer och skalnings uppsättningar i IIS-värdbaserade appar](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS-server](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Kom igång under utvecklingsfasen med:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
