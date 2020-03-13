---
title: Filtrering och förbearbetning i Azure Application Insights SDK | Microsoft Docs
description: Skriv telemetri och telemetri initierare för SDK för att filtrera eller lägga till egenskaper till data innan telemetri skickas till Application Insights portalen.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 9f4df83ed60ba94913702b9a32a298f0ac62f9f4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276327"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrera och Förbearbeta telemetri i Application Insights SDK

Du kan skriva och konfigurera plugin-program för Application Insights SDK för att anpassa hur telemetri kan berikas och bearbetas innan de skickas till Application Insightss tjänsten.

* [Samplingen](sampling.md) minskar mängden telemetri utan att påverka din statistik. Den håller samman relaterade data punkter så att du kan navigera mellan dem när du diagnostiserar ett problem. I portalen multipliceras det totala antalet för att kompensera för samplingen.
* Genom att filtrera med telemetri-processorer kan du filtrera bort telemetri i SDK innan det skickas till servern. Du kan till exempel minska mängden telemetri genom att undanta förfrågningar från robots. Filtrering är en grundläggande metod för att minska trafiken än sampling. Det ger dig större kontroll över vad som överförs, men du måste vara medveten om att det påverkar din statistik, till exempel om du filtrerar bort alla lyckade förfrågningar.
* [Telemetri initierare Lägg till eller ändra egenskaper](#add-properties) för all telemetri som skickas från din app, inklusive telemetri från standardmodulerna. Du kan till exempel lägga till beräknade värden. eller versions nummer som data ska filtreras efter i portalen.
* [SDK-API: et](../../azure-monitor/app/api-custom-events-metrics.md) används för att skicka anpassade händelser och mått.

Innan du börjar:

* Installera lämplig SDK för ditt program: [ASP.net](asp-net.md), [ASP.net Core](asp-net-core.md), [icke-http/Worker för .net/.net Core](worker-service.md), [Java](../../azure-monitor/app/java-get-started.md) eller [Java Script](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtrering

Med den här metoden får du direkt kontroll över vad som ingår eller exkluderas från telemetri-dataströmmen. Filtrering kan användas för att släppa telemetri-objekt från att skickas till Application Insights. Du kan använda den tillsammans med sampling eller separat.

Om du vill filtrera telemetri skriver du en telemetri-processor och registrerar den med `TelemetryConfiguration`. All telemetri går igenom din processor och du kan välja att släppa den från data strömmen eller ge den till nästa processor i kedjan. Detta inkluderar telemetri från standardmodulerna, till exempel insamlaren av HTTP-begäran och beroende insamlaren, och telemetri som du har spårat själv. Du kan till exempel filtrera bort telemetri om begär Anden från robotar eller lyckade beroende anrop.

> [!WARNING]
> Filtrering av telemetri som skickas från SDK med hjälp av processorer kan skeva statistiken som visas i portalen och göra det svårt att följa relaterade objekt.
>
> Överväg i stället att använda [sampling](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Skapa en telemetri processor (C#)

1. Implementera `ITelemetryProcessor`för att skapa ett filter.

    Observera att telemetri-processorer konstruerar en kedja av bearbetning. När du instansierar en telemetri-processor får du en referens till nästa processor i kedjan. När en telemetri-datapunkt skickas till process-metoden utför den sitt arbete och anropar (eller inte anropar) nästa telemetri-processor i kedjan.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. Lägg till din processor.

**ASP.net-appar** Infoga det här kodfragmentet i ApplicationInsights. config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Du kan skicka sträng värden från. config-filen genom att tillhandahålla offentliga namngivna egenskaper i din-klass.

> [!WARNING]
> Var noga med att matcha typnamn och eventuella egenskaps namn i. config-filen med klass-och egenskaps namnen i koden. Om. config-filen refererar till en icke-befintlig typ eller egenskap kan SDK: n Miss lyckas med att skicka telemetri.
>

Du kan **också** initiera filtret i kod. I en lämplig initierings klass – till exempel AppStart i `Global.asax.cs` – infoga din processor i kedjan:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetryClients som skapats efter att den här punkten använder dina processorer.

**ASP.NET Core/Worker service-appar**

> [!NOTE]
> Att lägga till processor med `ApplicationInsights.config` eller använda `TelemetryConfiguration.Active` är inte giltigt för ASP.NET Core program eller om du använder Microsoft. ApplicationInsights. WorkerService SDK.

För appar som skrivits med [ASP.net Core](asp-net-core.md#adding-telemetry-processors) eller [WorkerService](worker-service.md#adding-telemetry-processors)görs en ny `TelemetryProcessor` med hjälp av `AddApplicationInsightsTelemetryProcessor`-tilläggs metoden i `IServiceCollection`, som visas nedan. Den här metoden anropas i `ConfigureServices` metod i `Startup.cs`-klassen.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>Exempel filter

#### <a name="synthetic-requests"></a>Syntetiska begär Anden

Filtrera bort robotar-och webbtester. Även om Metrics Explorer ger dig möjlighet att filtrera bort syntetiska källor, minskar det här alternativet trafik-och inmatnings storlek genom att filtrera dem i själva SDK: n.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Misslyckad autentisering

Filtrera ut begär Anden med ett "401"-svar.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrera ut snabba fjärrberoende anrop

Om du bara vill diagnostisera samtal som är långsamma kan du filtrera bort dem snabbt.

> [!NOTE]
> Detta innebär att statistiken som visas i portalen skevas.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>Diagnostisera beroende problem

[Den här bloggen](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) beskriver ett projekt för att diagnostisera beroende problem genom att automatiskt skicka vanliga pingar till beroenden.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript-webbprogram

**Filtrera med ITelemetryInitializer**

1. Skapa en funktion för motanrop för telemetri. Motringningsfunktionen tar `ITelemetryItem` som en parameter, vilket är den händelse som bearbetas. Om du returnerar `false` från den här återanrops resultatet i telemetri-objektet som ska filtreras bort.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. Lägg till motanrop för telemetri:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Lägg till/ändra egenskaper: ITelemetryInitializer


Använd telemetri initierare för att utöka telemetri med ytterligare information och/eller för att åsidosätta egenskaper för telemetri som anges i standardmodulen för telemetri.

Application Insights för webb paket samlar till exempel telemetri om HTTP-begäranden. Som standard flaggas det som misslyckade förfrågningar med svars koden > = 400. Men om du vill att 400 ska behandlas som lyckad kan du ange en telemetri-initierare som anger egenskapen lyckades.

Om du anger en telemetri-initierare, anropas den när någon av metoderna Track () anropas. Detta inkluderar `Track()` metoder som anropas av standardmodulerna för telemetri. I konvention anger de här modulerna inte någon egenskap som redan har angetts av en initierare. Telemetri initierare anropas innan du anropar telemetri-processorer. Så alla berikare som görs av initierare är synliga för processorer.

**Definiera din initierare**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET appar: läsa in din initierare**

I ApplicationInsights. config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

Du kan *också* skapa en instans av initieraren i kod, till exempel i global.aspx.CS:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Se mer av det här exemplet.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/Worker service-appar: läsa in initieraren**

> [!NOTE]
> Att lägga till initieraren med `ApplicationInsights.config` eller använda `TelemetryConfiguration.Active` är inte giltig för ASP.NET Core program eller om du använder Microsoft. ApplicationInsights. WorkerService SDK.

För appar som skrivits med [ASP.net Core](asp-net-core.md#adding-telemetryinitializers) eller [WorkerService](worker-service.md#adding-telemetryinitializers)görs en ny `TelemetryInitializer` genom att lägga till en ny genom att lägga till den i behållaren för beroende inmatning, som visas nedan. Detta görs i `Startup.ConfigureServices` metod.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Initierare för Java-telemetri

[Java SDK-dokumentation](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Registrera sedan den anpassade initieraren i din applicationinsights. XML-fil.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Initierare för JavaScript-telemetri
*JavaScript*

Infoga en telemetri-initierare direkt efter initierings koden som du fick från portalen:

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

En sammanfattning av de icke-anpassade egenskaperna som är tillgängliga på telemetryItem finns i [Application Insights exportera data modell](../../azure-monitor/app/export-data-model.md).

Du kan lägga till så många initierare som du vill, och de anropas i den ordning som de har lagts till.

### <a name="opencensus-python-telemetry-processors"></a>Processorer för att openräkningar python-telemetri

Telemetri-processorer i openräkningar python är bara återanrops funktioner som kallas för att bearbeta telemetri innan de exporteras. Funktionen motringning måste acceptera en [Kuvert](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) data typ som parameter. Om du vill filtrera bort telemetri från att exporteras kontrollerar du att funktionen motringning returnerar `False`. Du kan se schemat för Azure Monitor data typer på kuverten [här](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
Du kan lägga till så många processorer som du vill, och de anropas i den ordning de läggs till. Om en processor ska utlösa ett undantag påverkar den inte följande processorer.

### <a name="example-telemetryinitializers"></a>Exempel TelemetryInitializers

#### <a name="add-custom-property"></a>Lägg till anpassad egenskap

Följande exempel initierare lägger till en anpassad egenskap i varje spårad telemetri.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Lägg till moln roll namn

I följande exempel initierare anges moln roll namnet till varje spårad telemetri.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor och ITelemetryInitializer

Vad är skillnaden mellan telemetri-processorer och telemetri-initierare?

* Det finns vissa överlappningar i vad du kan göra med dem: båda kan användas för att lägga till eller ändra egenskaper för telemetri, men det rekommenderas att du använder initierare för detta ändamål.
* TelemetryInitializers körs alltid före TelemetryProcessors.
* TelemetryInitializers kan anropas mer än en gång. Enligt konvention anger de inte någon egenskap som redan har angetts.
* Med TelemetryProcessors kan du helt ersätta eller ta bort ett telemetri-objekt.
* Alla registrerade TelemetryInitializers garanteras för att anropas för varje telemetri-objekt. För telemetri-processorer garanterar SDK att den allra första telemetri-processorn anropas. Oavsett om resten av processorerna anropas eller inte, bestäms av de tidigare telemetri-processorerna.
* Använd TelemetryInitializers för att utöka telemetri med ytterligare egenskaper, eller åsidosätta det befintliga. Använd TelemetryProcessor för att filtrera bort telemetri.

## <a name="troubleshooting-applicationinsightsconfig"></a>Felsöka ApplicationInsights. config

* Bekräfta att det fullständigt kvalificerade typ namnet och sammansättnings namnet är korrekt.
* Bekräfta att filen applicationinsights. config finns i utdatakatalogen och innehåller eventuella nyligen gjorda ändringar.

## <a name="reference-docs"></a>Referens dokument

* [API-översikt](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET-referens](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-kod

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Nästa steg
* [Sök efter händelser och loggar](../../azure-monitor/app/diagnostic-search.md)
* [Sampling](../../azure-monitor/app/sampling.md)
* [Felsökning](../../azure-monitor/app/troubleshoot-faq.md)
