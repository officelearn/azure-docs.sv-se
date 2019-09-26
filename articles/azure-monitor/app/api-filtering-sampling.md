---
title: Filtrering och förbearbetning i Azure Application Insights SDK | Microsoft Docs
description: Skriv telemetri och telemetri initierare för SDK för att filtrera eller lägga till egenskaper till data innan telemetri skickas till Application Insights portalen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 095d539404412d34c66201646f6134ff740f86b7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299264"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrera och Förbearbeta telemetri i Application Insights SDK

Du kan skriva och konfigurera plugin-program för Application Insights SDK för att anpassa hur telemetri kan berikas och bearbetas innan de skickas till Application Insightss tjänsten.

* [Samplingen](sampling.md) minskar mängden telemetri utan att påverka din statistik. Den håller samman relaterade data punkter så att du kan navigera mellan dem när du diagnostiserar ett problem. I portalen multipliceras det totala antalet för att kompensera för samplingen.
* Genom att filtrera med telemetri-processorer kan du filtrera bort telemetri i SDK innan det skickas till servern. Du kan till exempel minska mängden telemetri genom att undanta förfrågningar från robots. Filtrering är en grundläggande metod för att minska trafiken än sampling. Det ger dig större kontroll över vad som överförs, men du måste vara medveten om att det påverkar din statistik, till exempel om du filtrerar bort alla lyckade förfrågningar.
* [Telemetri initierare Lägg till eller ändra egenskaper](#add-properties) för all telemetri som skickas från din app, inklusive telemetri från standardmodulerna. Du kan till exempel lägga till beräknade värden. eller versions nummer som data ska filtreras efter i portalen.
* [SDK-API: et](../../azure-monitor/app/api-custom-events-metrics.md) används för att skicka anpassade händelser och mått.

Innan du börjar:

* Installera rätt SDK för ditt program. [ASP.net](asp-net.md) eller [ASP.net Core](asp-net-core.md) eller [icke-http/arbetare för .net/.net Core](worker-service.md) eller [Java](../../azure-monitor/app/java-get-started.md) i din app.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrering: ITelemetryProcessor

Med den här metoden får du direkt kontroll över vad som ingår eller exkluderas från telemetri-dataströmmen. Filtrering kan användas för att släppa telemetri-objekt från att skickas till Application Insights. Du kan använda den tillsammans med sampling eller separat.

Om du vill filtrera telemetri skriver du en telemetri-processor och registrerar den `TelemetryConfiguration`med. All telemetri går igenom din processor och du kan välja att släppa den från data strömmen eller ge den till nästa processor i kedjan. Detta inkluderar telemetri från standardmodulerna, till exempel insamlaren av HTTP-begäran och beroende insamlaren, och telemetri som du har spårat själv. Du kan till exempel filtrera bort telemetri om begär Anden från robotar eller lyckade beroende anrop.

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

Du kan **också** initiera filtret i kod. I en lämplig initierings klass – till exempel AppStart `Global.asax.cs` in-INSERT The-processor i kedjan:

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
> Att lägga till `ApplicationInsights.config` processor med `TelemetryConfiguration.Active` eller använda är inte giltigt för ASP.net Core program eller om du använder Microsoft. ApplicationInsights. WorkerService SDK.

För appar som skrivits med [ASP.net Core](asp-net-core.md#adding-telemetry-processors) eller [WorkerService](worker-service.md#adding-telemetry-processors)görs en ny `TelemetryProcessor` åtgärd med hjälp `AddApplicationInsightsTelemetryProcessor` av tilläggs metoden på `IServiceCollection`, som visas nedan. Den här metoden anropas `ConfigureServices` i-metoden `Startup.cs` för din klass.

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

#### <a name="diagnose-dependency-issues"></a>Diagnostisera beroendeproblem

[Den här bloggen](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) beskriver ett projekt för att diagnostisera beroende problem genom att automatiskt skicka vanliga pingar till beroenden.

<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Lägg till egenskaper: ITelemetryInitializer

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

**ASP.NET appar: Läs in din initierare**

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

**ASP.NET Core/Worker service-appar: Läs in din initierare**

> [!NOTE]
> Det går inte att `ApplicationInsights.config` lägga till `TelemetryConfiguration.Active` initieraren med eller använda är inte giltig för ASP.net Core program eller om du använder Microsoft. ApplicationInsights. WorkerService SDK.

För appar som skrivits med [ASP.net Core](asp-net-core.md#adding-telemetryinitializers) eller [WorkerService](worker-service.md#adding-telemetryinitializers)görs en ny `TelemetryInitializer` genom att lägga till en ny genom att lägga till den i behållaren för beroende insprutning, som visas nedan. Detta görs i `Startup.ConfigureServices` metod.

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

## <a name="reference-docs"></a>Referensdokument

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
