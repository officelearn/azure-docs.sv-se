---
title: Filtrering och förbearbetning i SDK för Azure Application Insights | Microsoft-dokument
description: Skriv telemetriprocessorer och telemetriinitierare för SDK att filtrera eller lägga till egenskaper till data innan telemetri skickas till Application Insights-portalen.
ms.topic: conceptual
ms.date: 11/23/2016
ms.openlocfilehash: 8f2064f73821a017046cbb552a8dcf592ce13267
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983766"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrering och förbearbetning telemetri i Application Insights SDK

Du kan skriva och konfigurera plugin-program för Programstatistik SDK för att anpassa hur telemetri kan berikas och bearbetas innan den skickas till application insights-tjänsten.

* [Sampling](sampling.md) minskar mängden telemetri utan att påverka din statistik. Den håller ihop relaterade datapunkter så att du kan navigera mellan dem när du diagnostiserar ett problem. I portalen multipliceras de totala antalet för att kompensera för provtagningen.
* Genom att filtrera med telemetriprocessorer kan du filtrera bort telemetri i SDK innan den skickas till servern. Du kan till exempel minska mängden telemetri genom att utesluta begäranden från robotar. Filtrering är en mer grundläggande metod för att minska trafiken än sampling. Det ger dig mer kontroll över vad som överförs, men du måste vara medveten om att det påverkar din statistik - till exempel om du filtrerar bort alla lyckade förfrågningar.
* [Telemetriinitierare lägger till eller ändrar egenskaper](#add-properties) i alla telemetri som skickas från din app, inklusive telemetri från standardmodulerna. Du kan till exempel lägga till beräknade värden. eller versionsnummer som data i portalen ska filtreras efter.
* [SDK API](../../azure-monitor/app/api-custom-events-metrics.md) används för att skicka anpassade händelser och mått.

Innan du börjar:

* Installera lämplig SDK för ditt program: [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [Icke HTTP/Worker för .NET/.NET Core](worker-service.md)eller [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtrering

Den här tekniken ger dig direkt kontroll över vad som ingår eller utesluts från telemetriströmmen. Filtrering kan användas för att släppa telemetriobjekt från att skickas till Application Insights. Du kan använda den tillsammans med Sampling, eller separat.

Om du vill filtrera telemetri skriver du en telemetriprocessor och registrerar den `TelemetryConfiguration`med . All telemetri går igenom processorn och du kan välja att släppa den från strömmen eller ge den till nästa processor i kedjan. Detta inkluderar telemetri från standardmodulerna, till exempel samlare av HTTP-begäranden och beroendeinsamlaren, och telemetri som du har spårat själv. Du kan till exempel filtrera bort telemetri om begäranden från robotar eller lyckade beroendeanrop.

> [!WARNING]
> Om du filtrerar telemetrin som skickas från SDK med processorer kan du förvränga den statistik som visas i portalen och göra det svårt att följa relaterade objekt.
>
> Överväg i stället att använda [sampling](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Skapa en telemetriprocessor (C#)

1. Om du vill `ITelemetryProcessor`skapa ett filter implementerar du .

    Observera att telemetriprocessorer konstruerar en bearbetningskedja. När du instansierar en telemetriprocessor får du en referens till nästa processor i kedjan. När en telemetridatapunkt skickas till processmetoden gör den sitt arbete och anropar sedan (eller inte) nästa telemetriprocessor i kedjan.

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

2. Lägg till processorn.

**ASP.NET appar** Infoga det här kodavsnittet i ApplicationInsights.config:

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

Du kan skicka strängvärden från config-filen genom att ange offentliga namngivna egenskaper i klassen.

> [!WARNING]
> Var noga med att matcha typnamnet och eventuella egenskapsnamn i config-filen med klass- och egenskapsnamnen i koden. Om config-filen refererar till en icke-existerande typ eller egenskap kan SDK tyst misslyckas med att skicka någon telemetri.
>

**Du** kan också initiera filtret i kod. I en lämplig initieringsklass `Global.asax.cs` - till exempel AppStart in - sätt in processorn i kedjan:

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

TelemetriClients som skapats efter denna punkt kommer att använda dina processorer.

**ASP.NET Appar för Core/Worker Service**

> [!NOTE]
> Att lägga `ApplicationInsights.config` till `TelemetryConfiguration.Active` processor med eller använda är inte giltigt för ASP.NET Core-program eller om du använder Microsoft.ApplicationInsights.WorkerService SDK.

För appar som skrivs med [ASP.NET Core](asp-net-core.md#adding-telemetry-processors) eller `TelemetryProcessor` [WorkerService](worker-service.md#adding-telemetry-processors)görs det `IServiceCollection`med tilläggsmetod `AddApplicationInsightsTelemetryProcessor` på , som visas nedan. Den här metoden `ConfigureServices` anropas `Startup.cs` i klassens metod.

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

### <a name="example-filters"></a>Exempel på filter

#### <a name="synthetic-requests"></a>Syntetiska förfrågningar

Filtrera bort robotar och webbtester. Även om Metrics Explorer ger dig möjlighet att filtrera bort syntetiska källor, minskar det här alternativet trafik- och inmatningsstorleken genom att filtrera dem på själva SDK.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>Misslyckad autentisering

Filtrera bort begäranden med ett "401"-svar.

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrera bort snabba fjärrberoendeanrop

Om du bara vill diagnostisera samtal som är långsamma filtrerar du bort de snabba.

> [!NOTE]
> Detta kommer att förvränga den statistik du ser på portalen.
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

#### <a name="diagnose-dependency-issues"></a>Problem med diagnostisera beroende

[Den här bloggen](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) beskriver ett projekt för att diagnostisera beroendeproblem genom att automatiskt skicka regelbundna pingar till beroenden.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript-webbprogram

**Filtrering med ITelemetryInitializer**

1. Skapa en telemetriinitelliseringsanropsfunktion. Motringningsfunktionen `ITelemetryItem` tar som en parameter, vilket är den händelse som bearbetas. Om `false` du återvänder från den här motringningen visas det telemetriobjekt som ska filtreras bort.  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. Lägg till din telemetriinitiering:

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>Lägg till/ändra egenskaper: ITelemetryInitializer


Använd telemetriinitierare för att berika telemetri med ytterligare information och/eller åsidosätt telemetriegenskaper som angetts av standardmodulerna för telemetri.

Programstatistik för webbpaket samlar till exempel in telemetri om HTTP-begäranden. Som standard flaggas en begäran som inte kunde begäras med en svarskod >= 400. Men om du vill behandla 400 som en framgång kan du tillhandahålla en telemetriinitierare som anger egenskapen Framgång.

Om du anger en telemetriinitierare anropas den när någon av metoderna Track*() anropas. Detta `Track()` inkluderar metoder som anropas av standardelemetrimodulerna. Av konvention anger dessa moduler inte någon egenskap som redan har angetts av en initializer. Telemetriinitierare anropas innan telemetriprocessorer anropas. Så alla berikanden som görs av initializers är synliga för processorer.

**Definiera din initializer**

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

**ASP.NET appar: Ladda din initializer**

I ApplicationInsights.config:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*Alternativt* kan du instansiera initializern i kod, till exempel i Global.aspx.cs:

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[Visa mer av det här exemplet.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core/ Worker Service-appar: Ladda din initializer**

> [!NOTE]
> Att lägga `ApplicationInsights.config` till `TelemetryConfiguration.Active` initializer med eller använda är inte giltigt för ASP.NET Core-program eller om du använder Microsoft.ApplicationInsights.WorkerService SDK.

För appar som skrivs med [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) eller `TelemetryInitializer` [WorkerService](worker-service.md#adding-telemetryinitializers)görs det genom att lägga till en ny genom att lägga till den i behållaren för beroendeinjektion, som visas nedan. Detta görs `Startup.ConfigureServices` metod.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript telemetriinitierare
*JavaScript*

Infoga en telemetriinitierare direkt efter initieringskoden som du fick från portalen:

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

En sammanfattning av de icke-anpassade egenskaper som är tillgängliga på telemetriItem finns i [Exportdatamodell för programinsikter](../../azure-monitor/app/export-data-model.md).

Du kan lägga till så många initializers som du vill, och de anropas i den ordning de läggs till.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python telemetriprocessorer

Telemetriprocessorer i OpenCensus Python är helt enkelt motringningsfunktioner som anropas för att bearbeta telemetri innan de exporteras. Motringningsfunktionen måste acceptera en [kuvertdatatyp](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) som parameter. Om du vill filtrera bort telemetri från att exporteras kontrollerar du att motringningsfunktionen returnerar `False`. Du kan se schemat för Azure Monitor-datatyper i kuverten [här](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py).

> [!NOTE]
> Du kan `cloud_RoleName` ändra `ai.cloud.role` attributet i `tags` fältet genom att ändra attributet i fältet.

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name.py'
```

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
Du kan lägga till så många processorer som du vill, och de anropas i den ordning de läggs till. Om en processor skulle kasta ett undantag påverkar det inte följande processorer.

### <a name="example-telemetryinitializers"></a>Exempel telemetriinitializers

#### <a name="add-custom-property"></a>Lägg till anpassad egenskap

Följande exempelinitierar lägger till en anpassad egenskap i varje spårad telemetri.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>Lägga till molnrollnamn

Följande exempelinitierare anger molnrollnamn till varje spårad telemetri.

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

Vad är skillnaden mellan telemetriprocessorer och telemetriinitierare?

* Det finns vissa överlappningar i vad du kan göra med dem: båda kan användas för att lägga till eller ändra egenskaper för telemetri, men det rekommenderas att använda initializers för detta ändamål.
* TelemetriInitializers körs alltid före TelemetriProcessorer.
* TelemetriInitializers kan anropas mer än en gång. Av konvention anger de inte någon egenskap som redan har fastställts.
* Med telemetriprocessorer kan du helt ersätta eller ta bort en telemetriartikel.
* Alla registrerade TelemetryInitializers är garanterade att kallas för varje telemetri objekt. För telemetriprocessorer garanterar SDK att anropa den allra första telemetriprocessorn. Om resten av processorerna anropas eller inte avgörs av föregående telemetriprocessorer.
* Använd TelemetryInitializers för att berika telemetri med ytterligare egenskaper eller åsidosätta befintliga. Använd TelemetriProcessor för att filtrera bort telemetri.

## <a name="troubleshooting-applicationinsightsconfig"></a>Felsöka ApplicationInsights.config

* Bekräfta att det fullständiga typnamnet och sammansättningsnamnet är korrekta.
* Bekräfta att filen applicationinsights.config finns i utdatakatalogen och innehåller eventuella ändringar av de senaste ändringarna.

## <a name="reference-docs"></a>Referensdokument

* [API-översikt](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET referens](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-kod

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Nästa steg
* [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md)
* [Samling](../../azure-monitor/app/sampling.md)
* [Felsökning](../../azure-monitor/app/troubleshoot-faq.md)
