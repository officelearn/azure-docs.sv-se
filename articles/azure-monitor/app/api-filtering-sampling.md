---
title: Filtrera och Förbearbeta i Azure Application Insights SDK | Microsoft Docs
description: Skriva telemetri processorer och telemetri-initierare för SDK om du vill filtrera eller lägga till egenskaper i data innan telemetri som skickas till Application Insights-portalen.
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
ms.openlocfilehash: 095ecdccde930db20ee858bfce7fc0811163cdd9
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038270"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrera och Förbearbeta telemetri i Application Insights SDK


Du kan skriva och konfigurera plugin-program för Application Insights SDK att anpassa hur telemetri samlas in och bearbetas innan det skickas till Application Insights-tjänsten.

* [Sampling](../../azure-monitor/app/sampling.md) minskar mängden telemetri utan att påverka din statistik. Den bevarar tillsammans relaterade datapunkter så att du kan navigera mellan dem vid diagnostisering av ett problem. I portalen är de totala antalet multipliceras för att kompensera för provtagning.
* Filtrering med telemetri processorer [för ASP.NET](#filtering) eller [Java](../../azure-monitor/app/java-filter-telemetry.md) du kan välja eller ändra telemetri i SDK: N innan den skickas till servern. Exempelvis kan minska du mängden telemetri genom att exkludera begäranden från robotar. Men filtrering är en enklare metod för att minska trafiken än sampling. Den låter dig större kontroll över vad skickas, men du måste vara medveten om att det påverkar din statistik – till exempel om du filtrerar ut alla lyckade begäranden.
* [Telemetri-initierare Lägg till egenskaper](#add-properties) till någon telemetri som skickas från din app, inklusive telemetri från standard-moduler. Exempelvis kan du lägga till beräknade värden. eller versionsnummer som du vill filtrera data i portalen.
* [SDK-API: et](../../azure-monitor/app/api-custom-events-metrics.md) används för att skicka anpassade händelser och mått.

Innan du börjar:

* Installera Application Insights [SDK för ASP.NET](../../azure-monitor/app/asp-net.md) eller [SDK för Java](../../azure-monitor/app/java-get-started.md) i din app.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrering: ITelemetryProcessor
Den här metoden ger mer kontroll över vad är inkluderas eller uteslutas från telemetriströmmen. Du kan använda den tillsammans med Sampling, eller separat.

Om du vill filtrera telemetri, Skriv en telemetri-processor och registrera den med SDK: N. All telemetri går igenom din processor och du kan välja att ta bort den från dataströmmen eller Lägg till egenskaper. Detta inkluderar telemetri från standard moduler, till exempel http-begäran insamlaren och beroende insamlaren, samt telemetri som du har skrivit själv. Du kan till exempel filtrera ut telemetri om förfrågningar från robotar eller lyckade beroendeanrop.

> [!WARNING]
> Filtrera telemetri som skickas från SDK kan med hjälp av processorer förskjuta den statistikinformation som visas i portalen och gör det svårt att följa relaterade objekt.
>
> I stället använda [sampling](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Skapa en telemetri-processor (C#)
1. Kontrollera att Application Insights SDK i ditt projekt är version 2.0.0 eller senare. Högerklicka på projektet i Visual Studio Solution Explorer och välj Hantera NuGet-paket. Kontrollera Microsoft.ApplicationInsights.Web i NuGet-Pakethanteraren.
2. Implementera ITelemetryProcessor för att skapa ett filter. Det här är en annan aspekt för utökningsbarhet som telemetri-modulen, telemetri-initierare och telemetri kanal.

    Observera att telemetri processorer konstruera en kedja av bearbetning. När du skapa en instans av en telemetri-processor, kan du skicka en länk till nästa processorn i kedjan. När en datapunkt för telemetri skickas till metoden processen, arbetar och anropar sedan nästa telemetri processorn i kedjan.

    ```csharp

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
3. Infoga detta i ApplicationInsights.config:

```xml

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Detta är samma avsnitt där du kan initiera ett filter för sampling.)

Du kan skicka strängvärden från .config-filen genom att tillhandahålla offentliga namngivna egenskaper i klassen.

> [!WARNING]
> Var noga med för att matcha namnet på och alla egenskapsnamn i .config-filen till klassen och egenskapen namn i koden. Om .config-filen refererar till en obefintlig eller egenskapen, kan SDK: N tyst inte skickar någon telemetri.
>
>

**Du kan också** du kan initiera filtret i kod. Infoga processorn i kedjan i en lämplig initieringen – till exempel AppStart i Global.asax.cs:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients som skapats efter den här punkten kommer att använda processorerna.

### <a name="example-filters"></a>Exempel-filter
#### <a name="synthetic-requests"></a>Syntetiska förfrågningar
Filtrera ut robotar och web tester. Även om Metrics Explorer ger dig möjlighet att filtrera bort syntetiska källor, minskar det här alternativet trafik genom att filtrera dem på SDK.

```csharp

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Misslyckade autentiseringar
Filtrera ut begäranden med ett ”401”-svar.

```csharp

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrera bort snabbt remote beroendeanrop
Om du bara vill att diagnostisera anrop som är långsam, filtrera bort de fast.

> [!NOTE]
> Detta kommer ge skeva statistik som visas på portalen. Beroende diagrammet ser ut som om beroendeanrop finns alla fel.
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
[Den här bloggen](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) beskriver ett projekt för att diagnostisera beroendeproblem genom att automatiskt skicka regelbundna ping till beroenden.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Lägg till egenskaper: ITelemetryInitializer
Använda telemetri-initierare för att definiera globala egenskaper som skickas med all telemetri; och för att åsidosätta valda beteende standardmoduler moduler.

Till exempel Application Insights för webbpaket samlar in telemetri om HTTP-förfrågningar. Som standard flaggar som misslyckad alla förfrågningar med svarskoden > = 400. Men om du vill hantera 400 som lyckas, kan du ange en telemetri-initierare som anger egenskapen lyckades.

Om du anger en telemetri-initierare kan anropas när någon av metoderna Track*() anropas. Detta inkluderar metoder som anropas av standardmoduler-moduler. Enligt konventionen används anger dessa moduler inte en egenskap som redan har gjorts av en initierare.

**Definiera din initieraren**

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
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Läsa in din initieraren**

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

*Du kan också* du kan skapa en instans av initierare i kod, till exempel i Global.aspx.cs:

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Se mer av det här exemplet.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="java-telemetry-initializers"></a>Java telemetri-initierare

[Java SDK-dokumentation](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility._telemetry_initializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Sedan registrera anpassade initieraren i filen applicationinsights.xml.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
<Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript telemetri-initierare
*JavaScript*

Infoga en telemetri-initierare omedelbart efter initieringskoden som du fick från portalen:

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

                // To check the telemetry item�s type - for example PageView:
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

En sammanfattning av icke-anpassade egenskaper som är tillgängliga på telemetryItem finns [exportera datamodellen i Application Insights](../../azure-monitor/app/export-data-model.md).

Du kan lägga till så många initierare som du vill.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor och ITelemetryInitializer
Vad är skillnaden mellan telemetri processorer och telemetri-initierare?

* Det finns vissa överlappningar i vad du kan göra med dem: båda kan användas för att lägga till egenskaper i telemetri.
* TelemetryInitializers körs alltid innan TelemetryProcessors.
* TelemetryProcessors kan du att fullständigt ersätta eller ta bort en telemetri-objektet.
* TelemetryProcessors bearbeta inte prestandatelemetri för räknaren.

## <a name="troubleshooting-applicationinsightsconfig"></a>Felsöka ApplicationInsights.config
* Bekräfta att det fullständigt kvalificerade typnamnet och sammansättningsnamnet är korrekta.
* Bekräfta att filen applicationinsights.config i utdatakatalogen och innehåller alla de senaste ändringarna.

## <a name="reference-docs"></a>Referensdokument
* [API-översikt](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET-referens](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-koden
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Nästa steg
* [Sökhändelser och loggar](../../azure-monitor/app/diagnostic-search.md)
* [Sampling](../../azure-monitor/app/sampling.md)
* [Felsökning](../../azure-monitor/app/troubleshoot-faq.md)
