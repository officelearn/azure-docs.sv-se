---
title: Filtrering och förbearbetning i Azure Application Insights SDK | Microsoft Docs
description: Skriva telemetri processorer och telemetri initierare för SDK för att filtrera eller lägga till egenskaper i data innan telemetrin skickas till Application Insights-portalen.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: mbullwin; borooji
ms.openlocfilehash: 06f116fc5096fe4bda9c8433c3a33726acb67eea
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrering och förbearbetning telemetri i Application Insights SDK


Du kan skriva och konfigurera plugin-program för Application Insights SDK att anpassa hur telemetri fångas och bearbetas innan den skickas till Application Insights-tjänsten.

* [Provtagning](app-insights-sampling.md) minskar mängden telemetri utan att påverka din statistik. Den bevarar tillsammans relaterade datapunkter så att du kan navigera mellan dem när de undersöker ett problem. I portalen, totalt antal multipliceras med för att kompensera för provtagning.
* Filtrering med telemetri processorer [för ASP.NET](#filtering) eller [Java](app-insights-java-filter-telemetry.md) kan du välja eller ändra telemetri i SDK innan den skickas till servern. Exempelvis kan du minska mängden telemetri exkluderar begäranden från robotar. Men filtrering är en enklare metod för att minska trafik än samplingsfrekvensen. Det gör att du mer kontroll över vad som överförs, men du måste vara medveten om att den påverkar din statistik – till exempel om du filtrerar ut alla lyckade begäranden.
* [Telemetri initierare lägga till egenskaper](#add-properties) till alla telemetri som skickas från din app, inklusive telemetri från modulerna som standard. Exempelvis kan du lägga till beräknade värden. eller versionsnummer som du vill filtrera data i portalen.
* [SDK-API](app-insights-api-custom-events-metrics.md) används för att skicka anpassade händelser och mått.

Innan du börjar:

* Installera Application Insights [SDK för ASP.NET](app-insights-asp-net.md) eller [SDK för Java](app-insights-java-get-started.md) i din app.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtrering: ITelemetryProcessor
Den här metoden ger mer kontroll över vad inkluderas eller uteslutas från telemetri dataströmmen. Du kan använda tillsammans med provtagning, eller separat.

Om du vill filtrera telemetri skriva en telemetri processor och registrera den med SDK. All telemetri passerar processorn och du kan välja att ta bort den från dataströmmen eller lägga till egenskaper. Detta inkluderar telemetri från standard moduler, till exempel http-begäran insamlaren och beroende insamlaren samt telemetri som du har skrivit själv. Du kan till exempel filtrera ut telemetri om förfrågningar från robotar eller lyckade beroendeanrop.

> [!WARNING]
> Filtrering telemetri som skickas från SDK kan-processorer ge skeva statistik som visas i portalen och gör det svårt att följa relaterade objekt.
>
> I stället använda [provtagning](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Skapa en telemetri-processor (C#)
1. Kontrollera att Application Insights SDK i projektet är version 2.0.0 eller senare. Högerklicka på projektet i Visual Studio Solution Explorer och välja hantera NuGet-paket. Kontrollera Microsoft.ApplicationInsights.Web i NuGet-Pakethanteraren.
2. Implementera ITelemetryProcessor om du vill skapa ett filter. Det här är en annan utökningspunkt som telemetri modul, telemetri initieraren och telemetri kanal.

    Observera att telemetri processorer konstruera en kedja av bearbetning. När du initierar en telemetri processor, skicka en länk till nästa processorn i kedjan. När en datapunkt telemetri skickas till metoden processen arbetar och anropar sedan nästa telemetri processorn i kedjan.

    ``` C#

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
1. Infoga det i ApplicationInsights.config:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Detta är samma avsnitt där du vill initiera ett filter för provtagning.)

Du kan överföra strängvärden från .config-filen genom att tillhandahålla offentliga namngivna egenskaper i klassen.

> [!WARNING]
> Se till att matcha namnet på och eventuella egenskapsnamn i .config-filen till klassen och egenskapen namn i koden. Om .config-filen refererar till en obefintlig eller egenskapen, misslyckas tyst SDK att skicka alla telemetri.
>
>

**Du kan också** du kan initiera filter i koden. I en lämplig initieringen Infoga klassen – till exempel AppStart i Global.asax.cs - processorn i kedjan:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients som skapats efter den här punkten kommer att använda din processorer.

Följande kod visar hur du lägger till en telemetri initieraren i ASP.NET Core.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var initializer = new SuccessfulDependencyFilter();
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();
    configuration.TelemetryInitializers.Add(initializer);
}
```

### <a name="example-filters"></a>Exempel filter
#### <a name="synthetic-requests"></a>Syntetiska begäranden
Filtrera ut robotar och web tester. Även om Metrics Explorer ger dig möjlighet att filtrera ut syntetiska källor, minskar det här alternativet trafik genom att filtrera dem på SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Misslyckad verifiering
Filtrera ut begäranden med svaret ”401”.

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrera bort snabb remote beroendeanrop
Om du bara vill diagnosticera anrop som är långsamma filtrera bort de fast som.

> [!NOTE]
> Detta kan ge skeva statistik som visas på portalen. Beroende diagrammet ser ut som om beroendeanrop är alla fel.
>
>

``` C#

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
[Den här bloggen](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) beskriver ett projekt för att diagnostisera beroende problem genom att automatiskt skicka regelbunden ping till beroenden.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Lägga till egenskaper: ITelemetryInitializer
Använd telemetri initierare för att definiera globala egenskaper som skickas med all telemetri; och åsidosätta valda beteendet för modulerna som standard telemetri.

Till exempel Programinsikter för webbpaketet samlar in telemetri om HTTP-förfrågningar. Som standard flaggor som misslyckad varje begäran med en svarskoden > = 400. Men om du vill behandla 400 som lyckas, kan du ange en telemetri initieraren som anger egenskapen lyckades.

Om du anger en telemetri initieraren kallas när någon av metoderna Track*() anropas. Detta inkluderar metoder kallas moduler som standard telemetri. Dessa moduler ange konventionen är inte en egenskap som redan har ställts in av en initierare.

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

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Du kan också* du kan initiera initierare i koden, till exempel i Global.aspx.cs:

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

### <a name="javascript-telemetry-initializers"></a>JavaScript telemetri initierare
*JavaScript*

Infoga en telemetri initieraren omedelbart efter initiering som du har fått från portalen:

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

                // To check the telemetry item’s type - for example PageView:
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

En sammanfattning av icke-anpassade egenskaper som är tillgängliga på telemetryItem finns [exportera datamodellen i Application Insights](app-insights-export-data-model.md).

Du kan lägga till så många initierare som du vill.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor och ITelemetryInitializer
Vad är skillnaden mellan telemetri processorer och telemetri initierare?

* Det finns vissa överlappningar i vad du kan göra med dem: båda kan användas för att lägga till egenskaper till telemetri.
* TelemetryInitializers körs alltid innan TelemetryProcessors.
* TelemetryProcessors kan du ersätta eller ta bort ett objekt telemetri helt.
* TelemetryProcessors bearbeta inte prestandaräknaren telemetri.

## <a name="troubleshooting-applicationinsightsconfig"></a>Felsökning av ApplicationInsights.config
* Bekräfta att det fullständigt kvalificerade namnet och sammansättningsnamnet är korrekta.
* Bekräfta att filen applicationinsights.config i utdatakatalogen och innehåller alla ändringar.

## <a name="reference-docs"></a>Referensdokument
* [API-översikt](app-insights-api-custom-events-metrics.md)
* [ASP.NET-referens](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-kod
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK FÖR ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Nästa steg
* [Sök händelser och loggar](app-insights-diagnostic-search.md)
* [Sampling](app-insights-sampling.md)
* [Felsökning](app-insights-troubleshoot-faq.md)
