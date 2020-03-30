---
title: Anslutningssträngar i Azure Application Insights | Microsoft-dokument
description: Så här använder du anslutningssträngar.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 7b049c04913d3415074f46b9d90ec34be874a2da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136714"
---
# <a name="connection-strings"></a>Anslutningssträngar

## <a name="overview"></a>Översikt

Anslutningssträngar ger Application Insight-användare en enda konfigurationsinställning, vilket eliminerar behovet av flera proxyinställningar. Mycket användbart för intranätwebbservrar, suveräna eller hybridmolnmiljöer som vill skicka in data till övervakningstjänsten.

Nyckelvärdesparen är ett enkelt sätt för användare att definiera en kombination av prefixsuffix för varje AI-tjänst (Application Insights) för varje tjänst/produkt (Application Insights).

> [!IMPORTANT]
> Vi rekommenderar inte att du ställer in både anslutningssträng och instrumenteringsnyckel. I händelse av att en användare anger båda, den som angavs sist kommer att ha företräde. 


## <a name="scenario-overview"></a>Scenarioöversikt 

Kundscenarier där vi visualiserar detta med störst inverkan:

- Brandväggsund undantag eller proxyomdirigeringar 

    I de fall där övervakning för intranätwebbserver krävs, bad vår tidigare lösning kunderna att lägga till enskilda tjänstslutpunkter i din konfiguration. Mer information finns [här](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#can-i-monitor-an-intranet-web-server). 
    Anslutningssträngar erbjuder ett bättre alternativ genom att minska denna ansträngning till en enda inställning. Ett enkelt prefix, suffix ändring tillåter automatisk population och omdirigering av alla slutpunkter till rätt tjänster. 

- Suveräna moln- eller hybridmolnmiljöer

    Användare kan skicka data till en definierad [Azure Government Region](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#application-insights).
    Med anslutningssträngar kan du definiera slutpunktsinställningar för intranätservrar eller hybridmolninställningar. 

## <a name="getting-started"></a>Komma igång

### <a name="finding-my-connection-string"></a>Hittar du min anslutningssträng?

Anslutningssträngen visas på översiktsbladet för din Application Insights-resurs.

![anslutningssträng på översiktsbladet](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Högsta längd

Anslutningen har en maximal längd på 4096 tecken som stöds.

#### <a name="key-value-pairs"></a>Nyckel/värde-par

Anslutningssträngen består av en lista med inställningar som representeras som nyckelvärdespar avgränsade med semikolon:`key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey`(ex: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000  Anslutningssträngen är ett **obligatoriskt** fält.
- `Authorization`(t.ex. ikey) (Den här inställningen är valfri eftersom vi idag bara stöder ikey-auktorisering.)
- `EndpointSuffix`(t.ex. applicationinsights.azure.cn) Om du ställer in slutpunktssuffixet instrueras SDK som Azure-molnet ska ansluta till. SDK monterar resten av slutpunkten för enskilda tjänster.
- Explicita slutpunkter.
  Alla tjänster kan uttryckligen åsidosättas i anslutningssträngen.
   - `IngestionEndpoint`(t.ex.https://dc.applicationinsights.azure.com)
   - `LiveEndpoint`(t.ex.https://live.applicationinsights.azure.com)
   - `ProfilerEndpoint`(t.ex.https://profiler.applicationinsights.azure.com)
   - `SnapshotEndpoint`(t.ex.https://snapshot.applicationinsights.azure.com)

#### <a name="endpoint-schema"></a>Slutpunktsschema

`<prefix>.<suffix>`
- Prefix: Definierar en tjänst. 
- Suffix: Definierar det vanliga domännamnet.

##### <a name="valid-suffixes"></a>Giltiga suffix

Här är en lista över giltiga suffix 
- applicationinsights.azure.cn
- applicationinsights.us


Se även:https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>Giltiga prefix

- [Intag av telemetri:](./app-insights-overview.md)`dc`
- [Live Mått:](./live-stream.md)`live`
- [Profiler:](./profiler-overview.md)`profiler`
- [Ögonblicksbild](./snapshot-debugger.md):`snapshot`



## <a name="connection-string-examples"></a>Exempel på anslutningssträng


### <a name="minimal-valid-connection-string"></a>Minimal giltig anslutningssträng

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

I det här exemplet har endast instrumenteringsnyckeln ställts in.

- Tillståndsschema som standard är "ikey" 
- Instrumenteringsnyckel: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
- De regionala tjänst-URI:erna baseras på [SDK-standardvärdena](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6) och ansluter till den offentliga globala Azure:
   - Intag:https://dc.services.visualstudio.com/
   - Live-mått:https://rt.services.visualstudio.com/
   - Profiler:https://agent.azureserviceprofiler.net/
   - Debugger:https://agent.azureserviceprofiler.net/  



### <a name="connection-string-with-endpoint-suffix"></a>Anslutningssträng med ändpunktssuffix

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

I det här exemplet anger den här anslutningssträngen slutpunktssuffixet och SDK ska konstruera tjänstslutpunkter.

- Tillståndsschema som standard är "ikey" 
- Instrumenteringsnyckel: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
- De regionala tjänst-URI:erna baseras på angivna slutpunktssuffix: 
   - Intag:https://dc.ai.contoso.com
   - Live-mått:https://live.ai.contoso.com
   - Profiler:https://profiler.ai.contoso.com 
   - Debugger:https://snapshot.ai.contoso.com   



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Anslutningssträng med explicita åsidosättningar av slutpunkt 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

I det här exemplet anger den här anslutningssträngen explicita åsidosättningar för varje tjänst. SDK kommer att använda de exakta slutpunkterna som tillhandahålls utan ändringar.

- Tillståndsschema som standard är "ikey" 
- Instrumenteringsnyckel: 00000000-0000-0000-0000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
- Uri:erna för den regionala tjänsten baseras på de explicita åsidosättningsvärdena: 
   - Intag: https:\//custom.com:111/
   - Live-mått: https:\//custom.com:222/
   - Profiler: https:\//custom.com:333/ 
   - Felsökare: https:\//custom.com:444/   


## <a name="how-to-set-a-connection-string"></a>Så här ställer du in en anslutningssträng

Anslutningssträngar stöds i följande SDK-versioner:
- .NET och .NET Core v2.12.0
- Java v2.5.1
- Javascript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

En anslutningssträng kan anges med antingen kod, miljövariabel eller konfigurationsfil.



### <a name="environment-variable"></a>Miljövariabel

- Anslutningssträng:`APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="net-sdk-example"></a>.Net SDK-exempel

TelemetriKonfiguration.ConnectionString:https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.Net uttryckligen ange:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.Net Config-fil:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```


NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


### <a name="java-sdk-example"></a>Exempel på Java SDK


Java uttryckligen ange:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

### <a name="javascript-sdk-example"></a>Exempel på Javascript SDK

Viktigt: Javascript stöder inte användning av miljövariabler.

Använda kodavsnittet:

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

### <a name="node-sdk-example"></a>Exempel på Nod SDK

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

### <a name="python-sdk-example"></a>Python SDK-exempel

Vi rekommenderar användare att ställa in miljövariabeln.

Så här anger du anslutningssträngen explicit:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>Nästa steg

Kom igång under körningsfasen med:

* [Azure VM och Azure virtuell dator skala uppsättning IIS-värdappar](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [IIS-server](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Web Apps](../../azure-monitor/app/azure-web-apps.md)

Kom igång under utvecklingsfasen med:

* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)
