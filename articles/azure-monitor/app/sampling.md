---
title: Telemetri-sampling i Azure Application Insights | Microsoft Docs
description: Hur du behåller volymen av telemetri under kontroll.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.openlocfilehash: 4b0dca1215cfecea5c9943bd27ee8a5c1de45311
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893373"
---
# <a name="sampling-in-application-insights"></a>Sampling i Application Insights

Sampling är en funktion i [Azure Application insikter](../../azure-monitor/app/app-insights-overview.md). Vi rekommenderar att du minskar trafik och lagring av telemetri, samtidigt som du behåller en statistisk korrekt analys av program data. Filtret väljer objekt som är relaterade, så att du kan navigera mellan objekt när du gör diagnostiska undersökningar.
När antalet mått visas i portalen, är de omnormaliserade för att ta hänsyn till konto insamlingen. Detta minimerar eventuell påverkan på statistiken.

Sampling minskar trafik och data kostnader och hjälper dig att undvika begränsning.

## <a name="in-brief"></a>I korthet:

* Samplingen behåller 1 i *n* poster och tar bort resten. Den kan till exempel behålla en i fem händelser, en samplings frekvens på 20%. 
* Adaptiv sampling är aktiverat som standard i alla de senaste versionerna av ASP.NET och ASP.NET Core SDK: er (Software Development Kits).
* Du kan också ange sampling manuellt. Detta kan konfigureras i portalen på *sidan användning och uppskattade kostnader*i ASP.NET SDK i filen ApplicationInsights. config i ASP.net Core SDK via kod eller i Java SDK i filen ApplicationInsights. xml.
* Om du loggar anpassade händelser och måste se till att en uppsättning händelser behålls eller ignoreras tillsammans, måste händelserna ha samma OperationId-värde.
* Samplings divisor *n* rapporteras i varje post i egenskapen `itemCount`, som i sökningen visas under det egna namnet "antal begär Anden" eller "antal händelser". `itemCount==1`när samplingen inte är i drift.
* Om du skriver analys frågor bör du [ta hänsyn till sampling](../../azure-monitor/log-query/aggregations.md). I stället för att helt enkelt räkna poster bör du använda `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typer av sampling

Det finns tre alternativa exempel metoder:

* **Adaptiv sampling** justerar automatiskt hur många telemetri som skickas från SDK: n i din ASP.net/ASP.net Core-app. Detta är standard sampling från ASP.NET Web SDK v 2.0.0-beta3 och Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 och senare.  Adaptiv sampling är för närvarande endast tillgängligt för ASP.NET på Server sidan.

* Med **fast pris sampling** minskar mängden telemetri som skickas från både din ASP.NET eller ASP.net Core eller Java Server och från användarnas webbläsare. Du ställer in priset. Klienten och servern synkroniserar sin sampling så att du i sökningen kan navigera mellan relaterade sidvyer och begär Anden.

* Inmatnings **sampling** Fungerar i Azure Portal. Den tar bort vissa av Telemetrin som kommer från din app, enligt en samplings frekvens som du anger. Det minskar inte telemetri trafik som skickas från din app, men hjälper dig att hålla dig inom din månads kvot. Den största fördelen med inmatnings sampling är att du kan ange samplings frekvensen utan att distribuera om appen. Provtagnings samplingen fungerar enhetligt för alla servrar och klienter.

Om anpassningsbara eller fasta samplings frekvenser är i drift inaktive ras inmatnings sampling.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Anpassningsbar sampling i ASP.NET/ASP.NET Core-webbprogram

Adaptiv sampling är tillgängligt för Application Insights SDK för ASP.NET v 2.0.0-beta3 och senare, Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0-beta1 och senare, och är aktiverat som standard.

Adaptiv sampling påverkar hur många telemetri som skickas från din webbapp till Application Insights tjänstens slut punkt. Volymen justeras automatiskt för att hållas inom en angiven högsta trafik hastighet och styrs via inställningen `MaxTelemetryItemsPerSecond`. Om programmet genererar en liten mängd telemetri, till exempel vid fel sökning eller på grund av låg användning, kommer objekten inte att släppas av samplings processorn så länge volymen är under `MaxTelemetryItemsPerSecond`. När telemetri ökar justeras samplings frekvensen så att mål volymen uppnås.

För att uppnå mål volymen ignoreras en del av den genererade Telemetrin. Men som andra typer av sampling behåller algoritmen relaterade telemetri objekt. När du till exempel inspekterar Telemetrin i sökningen kommer du att kunna hitta begäran som är relaterad till ett visst undantag.

Mått, till exempel begär ande frekvens och undantags frekvens justeras för att kompensera för samplings frekvensen, så att de visar ungefär rätt värden i Metric Explorer.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurera adaptiv sampling för ASP.NET-program

[Läs mer](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) om hur du konfigurerar adaptiv sampling för ASP.net Core program. 

I [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)kan du justera flera parametrar i `AdaptiveSamplingTelemetryProcessor`-noden. De siffror som visas är standardvärden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Den mål taxa som den anpassningsbara algoritmen syftar **på på varje server värd**. Om din webbapp körs på många värdar kan du minska det här värdet så att det ligger kvar i trafik hastigheten på Application Insights portalen.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervallet då den aktuella takten för telemetri utvärderas igen. Utvärderingen utförs som ett glidande medelvärde. Du kanske vill förkorta det här intervallet om din telemetri är ansvarig för plötsliga burst-överföring.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    När samplings procentens värde ändras, hur lång tid det tar att sänka samplings procenten igen för att samla in mindre data.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    När samplings procentens värde ändras, hur snart vi har tillåtit att öka samplings procenten igen för att samla in mer data.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Som samplings procent varierar, vilket är det minsta värde som vi kan ange.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Som samplings procent varierar, vilket är det högsta värdet som vi får ange.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Vid beräkningen av det glidande medelvärdet har vikten tilldelats det senaste värdet. Använd ett värde som är lika med eller mindre än 1. Lägre värden gör algoritmen mindre aktiv till plötsliga ändringar.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Värdet som tilldelas när appen precis har startats. Minska inte värdet när du felsöker.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    En semikolonavgränsad lista med typer som du inte vill ska samplas. Identifierade typer är: beroende, händelse, undantag, sid visningar, begäran, spårning. Alla instanser av de angivna typerna överförs. de typer som inte har angetts är samplade.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    En semikolonavgränsad lista med typer som du vill ska samplas. Identifierade typer är: beroende, händelse, undantag, sid visningar, begäran, spårning. De angivna typerna samplas. alla instanser av andra typer skickas alltid.


**Om du vill inaktivera** anpassad sampling tar du bort AdaptiveSamplingTelemetryProcessor-noderna från applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativ: Konfigurera adaptiv sampling i kod

I stället för att ställa in parametern sampling i. config-filen kan du konfigurera dessa värden program mässigt.

1. Ta bort alla `AdaptiveSamplingTelemetryProcessor`-noder från. config-filen.
2. Använd följande kodfragment för att konfigurera adaptiv sampling.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Läs mer om telemetri-processorer](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Du kan också justera samplings frekvensen för varje typ av telemetri individuellt, eller så kan du till och med undanta vissa typer från att samplas över huvud taget. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurera adaptiv sampling för ASP.NET Core program.

Det finns inga `ApplicationInsights.Config` för ASP.NET Core program, så varje konfiguration görs via kod.
Adaptiv sampling är aktiverat som standard för alla ASP.NET Core-program. Du kan inaktivera eller anpassa samplings beteendet.

### <a name="turning-off-adaptive-sampling"></a>Stänga av adaptiv sampling

Standard samplings funktionen kan inaktive ras när du lägger till Application Insights tjänst i metoden ```ConfigureServices```med ```ApplicationInsightsServiceOptions``` i `Startup.cs`-filen:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);
    //...
}
```

Ovanstående kod kommer att inaktivera samplings funktionen. Följ stegen nedan för att lägga till sampling med fler anpassnings alternativ.

### <a name="configure-sampling-settings"></a>Konfigurera inställningar för sampling

Använd tilläggs metoder för ```TelemetryProcessorChainBuilder``` som visas nedan för att anpassa samplings beteendet.

> [!IMPORTANT]
> Om du använder den här metoden för att konfigurera sampling ska du se till att använda aiOptions. EnableAdaptiveSampling = false; inställningar med AddApplicationInsightsTelemetry ().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Om du använder metoden ovan för att konfigurera sampling ska du se till att använda ```aiOptions.EnableAdaptiveSampling = false;``` inställningar med AddApplicationInsightsTelemetry ().**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications"></a>Fast pris sampling för ASP.NET, ASP.NET Core, Java webbplatser och python-program

Sampling av fast pris minskar trafiken som skickas från webb servern och webbläsare. Till skillnad från adaptiv sampling minskar den Telemetrin till en fast pris kontroll. Den synkroniserar också klient-och Server samplingen så att relaterade objekt bevaras, till exempel när du tittar på en sidvy i sökningen kan du hitta dess relaterade förfrågan.

Precis som andra samplings tekniker behåller detta även relaterade objekt. För varje HTTP-begäran, tas begäran och dess relaterade händelser antingen bort eller överförs tillsammans.

I Metrics Explorer multipliceras priser som begäran och undantags antal med en faktor för att kompensera för samplings frekvensen, så att de är ungefär korrekta.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurera fast priss insamling i ASP.NET

1. **Inaktivera adaptiv sampling**: i [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)tar du bort eller kommenterar `AdaptiveSamplingTelemetryProcessor`-noden.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Aktivera samplings-modulen för fast pris.** Lägg till det här kodfragmentet i [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativ: Aktivera fast priss insamling i Server koden
    
    I stället för att ställa in parametern sampling i. config-filen kan du konfigurera dessa värden program mässigt. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([Läs mer om telemetri-processorer](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Konfigurera fast priss insamling i ASP.NET Core

1. **Inaktivera adaptiv sampling**: ändringar kan göras i metoden ```ConfigureServices```med ```ApplicationInsightsServiceOptions```:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    //...
    }
    ```

2. **Aktivera samplings-modulen för fast pris.** Ändringar kan göras i metoden ```Configure``` som du ser i följande kodfragment:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurera fast priss insamling i JAVA ###

1. Hämta och konfigurera ditt webb program med de senaste [Application Insights Java SDK: n](../../azure-monitor/app/java-get-started.md)

2. **Aktivera samplings funktionen för fast pris** genom att lägga till följande kodfragment i ApplicationInsights. XML-filen.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. Du kan inkludera eller exkludera vissa typer av telemetri från sampling med hjälp av följande Taggar i processor tag gen "FixedRateSamplingTelemetryProcessor"
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

De typer av telemetri som kan tas med eller undantas från sampling är: beroende, Event, Exception, sid visningar, Request och trace.

> [!NOTE]
> För samplings procenten väljer du en procents ATS som ligger nära 100/N där N är ett heltal.  För närvarande stöder sampling inte andra värden.
> 
> 

<a name="other-web-pages"></a>

### <a name="configuring-fixed-rate-sampling-in-opencensus-python"></a>Konfigurera fast priss insamling i openräkningar python ###

1. Instrumentera ditt program med de senaste [Azure Monitor exportörerna för openräkning](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Fast Rate-sampling är bara tillgängligt med spårnings export verktyget. Det innebär att inkommande och utgående begär Anden är de enda typerna av telemetri där sampling kan konfigureras.
> 
> 

2. Du kan ange en `sampler` som en del av din `Tracer`-konfiguration. Om inget explicit sampler anges används ProbabilitySampler som standard. ProbabilitySampler skulle använda en frekvens på 1/10000 som standard, vilket innebär att en av varje 10000-begäran skickas till Application Insights. Se nedan om du vill ange en samplingsfrekvens.

3. När du anger en sampler kontrollerar du att din `Tracer` anger en sampler med en samplingsfrekvens från och med 0,0 till och med 1,0. En samplings frekvens på 1,0 representerar 100%, vilket innebär att alla dina begär Anden skickas som telemetri till Application Insights.

    ```python
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
        sampler=ProbabilitySampler(1.0),
    )
    ```

## <a name="ingestion-sampling"></a>Inmatnings sampling

Den här typen av sampling fungerar på den plats där telemetri från webb servern, webbläsare och enheter når Application Insights tjänstens slut punkt. Även om den inte minskar telemetri trafiken som skickas från din app, minskar den mängden bearbetad och bevaras (och debiteras för) av Application Insights.

Använd den här typen av sampling om din app ofta går över sin månads kvot och du inte har möjlighet att använda någon av SDK-baserade typer av sampling. 

Ange samplings frekvensen på sidan användning och uppskattade kostnader:

![Från bladet program Översikt klickar du på Inställningar, kvot, exempel och väljer sedan en samplings frekvens och klickar på Uppdatera.](./media/sampling/04.png)

I likhet med andra typer av sampling behåller algoritmen relaterade telemetri objekt. När du till exempel inspekterar Telemetrin i sökningen kommer du att kunna hitta begäran som är relaterad till ett visst undantag. Mått, till exempel begär ande frekvens och undantags frekvens, bevaras korrekt.

Data punkter som tas bort från sampling är inte tillgängliga i någon Application Insights funktion som [kontinuerlig export](../../azure-monitor/app/export-telemetry.md).

Provtagnings samplingen fungerar inte när SDK-baserad anpassningsbar eller fast pris sampling är i drift. Adaptiv sampling är aktiverat som standard när ASP.NET/ASP.NET Core SDK är aktiverat i Visual Studio eller aktive ras i Azure Web App-tillägg eller med hjälp av Statusövervakare, och inmatnings sampling är inaktive rad. Om samplings frekvensen på SDK: n är mindre än 100% (dvs. objekt som samplas) ignoreras den inmatnings samplings frekvens som du har angett.

> [!WARNING]
> Värdet som visas på panelen visar det värde som du angav för inmatnings sampling. Den representerar inte den faktiska samplings frekvensen om SDK-sampling är i drift.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>Sampling för webb sidor med Java Script
Du kan konfigurera webb sidor för sampling med fast pris från vilken server som helst. 

När du [konfigurerar webb sidorna för Application Insights](../../azure-monitor/app/javascript.md)ändra JavaScript-kodfragmentet som du får från Application Insights Portal. (I ASP.NET-appar hamnar kodfragmentet vanligt vis i _Layout. cshtml.)  Infoga en rad som `samplingPercentage: 10,` före Instrumentation-tangenten:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

För samplings procenten väljer du en procents ATS som ligger nära 100/N där N är ett heltal.  För närvarande stöder sampling inte andra värden.

Om du även aktiverar fast priss ökning på servern synkroniseras klienterna och servern så att du i sökningen kan navigera mellan relaterade sidvyer och begär Anden.

## <a name="when-to-use-sampling"></a>När ska du använda sampling?

Adaptiv sampling aktive ras automatiskt i de senaste .NET-och .NET Core SDK: erna. Oavsett vilken version av SDK du använder kan du aktivera inmatnings sampling för att tillåta Application Insights att sampla insamlade data.

Som standard är ingen sampling aktive rad i Java SDK. För närvarande stöder den bara fast priss insamling. Anpassad sampling stöds inte i Java SDK.

I allmänhet för de flesta små och medel stora program behöver du inte använda sampling. Den mest användbara diagnostikinformation och den mest exakta statistiken erhålls genom att samla in data för alla dina användar aktiviteter. 

De största fördelarna med sampling är:

* Application Insights tjänst släpper ("begränsning") data punkter när appen skickar en mycket hög telemetri i kort tidsintervall. 
* För att hålla dig inom [kvoten](../../azure-monitor/app/pricing.md) för data punkter för din pris nivå. 
* För att minska nätverks trafiken från insamling av telemetri. 

### <a name="which-type-of-sampling-should-i-use"></a>Vilken typ av sampling ska jag använda?

**Använd inmatnings sampling om:**

* Du går ofta igenom din månads kvot för telemetri.
* Du använder en version av SDK som inte stöder sampling, t. ex. ASP.NET versioner som är äldre än 2.
* Du får för mycket telemetri från användarnas webbläsare.

**Använd sampling med fast pris om:**

* Du använder Application Insights SDK för ASP.NET Web Services version 2.0.0 eller senare eller Java SDK v 2.0.1 eller senare, och
* Du vill ha synkroniserad sampling mellan klienten och servern, så att när du undersöker händelser i [sökningen](../../azure-monitor/app/diagnostic-search.md)kan du navigera mellan relaterade händelser på klienten och servern, till exempel sidvyer och HTTP-begäranden.
* Du är säker på rätt samplings procent för din app. Det bör vara tillräckligt högt för att få korrekta mått, men under den hastighet som överskrider din prissättnings kvot och begränsnings gränser. 

**Använd anpassningsbar sampling:**

Om villkoren för att använda andra former av sampling inte gäller rekommenderar vi anpassad sampling. Den här inställningen är aktive rad som standard i ASP.NET/ASP.NET Core Server SDK. Det minskar inte trafiken förrän en viss minimi hastighet uppnås, och därför påverkas inte webbplatser med låg användning.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Hur gör jag för att vet du om samplingen är i drift?

Om du vill identifiera den faktiska samplings frekvensen oavsett var den har använts använder du en [analys fråga](../../azure-monitor/app/analytics.md) som detta:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Om RetainedPercentage för någon typ är mindre än 100 samplas det objektet.

**Application Insights inte samplar sessioner, statistik och prestanda räknare typer av telemetri i alla samplings tekniker som beskrivs ovan. De här typerna utesluts alltid från sampling eftersom minskning av precisionen kan vara mycket olämplig för dessa typer av telemetri**

## <a name="how-does-sampling-work"></a>Hur fungerar sampling?

Funktionen för sampling av fast pris i SDK i ASP.NET-versioner från 2.0.0 och Java SDK version 2.0.1 och senare. Adaptiv sampling är en funktion i SDK i ASP.NET-versioner från 2.0.0 och senare. Inmatnings sampling är en funktion i Application Insightss tjänsten och kan användas om SDK: n inte utför sampling.

Samplings algoritmen bestämmer vilka telemetri-objekt som ska släppas och vilka som ska behållas (oavsett om det är i SDK eller i Application Insights tjänsten). Samplings beslutet baseras på flera regler som syftar till att bevara alla samrelaterade data punkter, vilket bibehåller en diagnostisk upplevelse i Application Insights som är åtgärds bara och tillförlitliga, även med en reducerad data uppsättning. Om till exempel en misslyckad begäran om att din app skickar ytterligare telemetri-objekt (till exempel undantag och spår som loggats från denna begäran), kommer samplingen inte att dela denna begäran och annan telemetri. Den behåller eller släpper ihop dem tillsammans. När du tittar på förfrågnings informationen i Application Insights kan du alltid se begäran tillsammans med tillhör ande mallobjekt. 

Samplings beslutet baseras på åtgärds-ID för begäran, vilket innebär att alla telemetridata som tillhör en viss åtgärd antingen bevaras eller tas bort. För telemetri-objekt som inte har något åtgärds-ID (t. ex. telemetri-objekt som rapporter ATS från asynkrona trådar utan http-kontext) fångar samplingen bara en procent andel telemetri objekt av varje typ. Före 2.5.0-beta2 av .NET SDK och 2.2.0-beta3 för ASP.NET Core SDK baserades samplings beslutet på hashen för användar-ID: t för program som definierar "användare" (det vill säga de flesta vanliga webb program). För de typer av program som inte definierade användare (t. ex. webb tjänster) var samplings beslutet baserat på åtgärds-ID för begäran.

När du presenterar telemetri tillbaka till dig justerar Application Insights-tjänsten måtten efter samma samplings procent som användes vid tidpunkten för samlingen, för att kompensera för de data punkter som saknas. När du tittar på Telemetrin i Application Insights, ser användarna då statistiskt korrekta uppskattningar som är mycket nära de reella talen.

Noggrannheten i uppskattningen beror i stor utsträckning på den konfigurerade samplings procenten. Dessutom ökar noggrannheten för program som hanterar en stor mängd av allmänt förekommande liknande förfrågningar från många användare. För program som inte fungerar med en betydande belastning behövs dock inte samplingen eftersom dessa program normalt kan skicka all telemetri samtidigt som de ligger inom kvoten, utan att orsaka data förlust från begränsning. 

> [!WARNING]
> Application Insights kan inte sampla mått och typer av telemetri för sessioner. Minskning av precisionen kan vara mycket olämplig för dessa typer av telemetri.
> 

### <a name="adaptive-sampling"></a>Anpassningsbar sampling

Adaptiv sampling lägger till en komponent som övervakar den aktuella överföringshastigheten från SDK och justerar samplings procenten för att försöka stanna inom den högsta tillåtna mål nivån. Justeringen omberäknas med jämna mellanrum och baseras på ett glidande medelvärde för utgående överföringshastigheten.

## <a name="sampling-and-the-javascript-sdk"></a>Sampling och Java Script SDK

SDK: n för klient sidan (Java Script) ingår i fast pris-sampling tillsammans med SDK för Server sidan. De instrumenterade sidorna skickar endast telemetri på klient sidan från samma användare som server sidan gjorde sitt beslut till "Sample in". Den här logiken är utformad för att upprätthålla integriteten hos användarsessionen mellan klient-och Server sidor. Därför kan du från ett visst telemetri-objekt i Application Insights hitta alla andra telemetridata för den här användaren eller sessionen. 

*Min-klient och telemetri på Server sidan visar inte koordinerade exempel som beskrivs ovan.*

* Kontrol lera att du har aktiverat sampling med fast taxa både på Server och klient.
* Kontrol lera att SDK-versionen är 2,0 eller högre.
* Kontrol lera att du anger samma samplings procent i både klienten och servern.

### <a name="sampling-in-azure-functions"></a>Sampling i Azure Functions

Följ instruktionerna nedan [för att konfigurera](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) sampling för appar som körs i Azure Functions.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*Vad är standard beteendet för sampling i ASP.NET och ASP.NET Core SDK?*

* Om du använder någon av de senaste versionerna av ovanstående SDK, aktive ras adaptiv sampling som standard med fem telemetri objekt per sekund.
  Det finns två AdaptiveSamplingTelemetryProcessors som läggs till som standard och en innehåller händelse typ i sampling och den andra händelse typen undantar från sampling. Den här konfigurationen innebär att SDK försöker begränsa telemetri-objekt till fem telemetri objekt av händelse typer och fem telemetridata av alla andra typer, vilket säkerställer att händelser samplas separat från andra typer av telemetri. Händelser används vanligt vis för affärstelemetri, och de mest sannolika bör inte påverkas av diagnostiska telemetri volymer.
  
  Nedan visas standard filen ApplicationInsights. config som genereras. Som det beskrivs finns det två separata AdaptiveSamplingTelemetryProcessor-noder som har lagts till, en exklusive händelse typer och en annan inklusive den. I ASP.NET Core är exakt samma standard beteende aktive rad i kod. Använd exemplen i det tidigare avsnittet av dokumentet för att ändra det här standard beteendet.

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*Kan telemetri samplas mer än en gång?*

* Nej. SamplingTelemetryProcessors ignorerar objekt från samplings överväganden om objektet redan har testats. Samma sak gäller för insamlings sampling, som inte använder sampling för de objekt som redan har tagits med i själva SDK: n.

*Varför är det inte att sampla en enkel "samla in X procent av varje typ av telemetri"?*

* Även om den här samplings metoden skulle ge en hög precisions nivå i mått uppskattningar skulle det brytas möjlighet att korrelera diagnostikdata per användare, session och begäran, vilket är kritiskt för diagnostik. Därför fungerar samplingen bättre med "samla in alla telemetridata för X% av App-användare" eller "samla in all telemetri för X% av app-begäranden"-logiken. För telemetri-objekten som inte är associerade med begär Anden (till exempel bakgrunds asynkron bearbetning) är återställningen till "samla in X procent av alla objekt för varje typ av telemetri". 

*Kan samplings procent förändringen över tid?*

* Ja, anpassningsbar sampling ändrar gradvis samplings procent baserat på den aktuella observerade volymen för Telemetrin.

*Hur vet jag vilken samplings procent som fungerar bäst för min app om jag använder sampling med fast pris.*

* Ett sätt är att börja med anpassningsbara samplingar, ta reda på vilken taxa den har påbörjat (se ovanstående fråga) och växla sedan till fast pris sampling med denna hastighet. 
  
    Annars måste du gissa. Analysera den aktuella telemetri användningen i Application Insights, Observera eventuella begränsningar som inträffar och beräkna volymen för den insamlade telemetri. Dessa tre indata, tillsammans med den valda pris nivån, föreslår hur mycket du kanske vill minska mängden insamlad telemetri. En ökning av antalet användare eller någon annan Skift i volymen av telemetri kan dock ogiltig förklara beräkningen.

*Vad händer om jag konfigurerar samplings procent för lågt?*

* En alltför låg andel samplings procent (över aggressiva samplingar) minskar noggrannheten i ungefärligt, när Application Insights försöker kompensera visualiseringen för data volymens minskning. Dessutom kan diagnostiken påverkas negativt, eftersom vissa av de fel som Miss lyckas eller långsamma begär Anden kan samplas ut.

*Vad händer om jag konfigurerar samplings procent för hög?*

* Om du konfigurerar för hög samplings procent (inte aggressivt tillräckligt) resulterar det i en otillräcklig minskning av volymen för den insamlade Telemetrin. Du kan fortfarande uppleva telemetri data förlust relaterat till begränsning, och kostnaden för att använda Application Insights kan vara högre än vad du har planerat på grund av överutnyttjade avgifter.

*På vilka plattformar kan jag använda sampling?*

* Provtagnings sampling kan ske automatiskt för en telemetri över en viss volym, om SDK: n inte utför sampling. Den här konfigurationen fungerar, till exempel om du använder en äldre version av ASP.NET SDK eller en tidigare version av Java SDK (1.0.10 eller tidigare).
* Om du använder ASP.NET SDK-versioner 2.0.0 och senare eller ASP.NET CORE SDK-version 2.2.0 och senare (som finns i Azure eller på din egen server) får du anpassningsbara samplingar som standard, men du kan växla till fast pris enligt beskrivningen ovan. Med fast Rate-sampling synkroniserar webbläsarens SDK automatiskt till exempel relaterade händelser. 
* Om du använder Java SDK version 2.0.1 eller senare kan du konfigurera ApplicationInsights. xml för att aktivera fast pris sampling. Sampling är inaktive rad som standard. Med fast Rate-sampling synkroniserar webbläsarens SDK automatiskt till exempel relaterade händelser.

*Det finns vissa sällsynta händelser som jag alltid vill se. Hur kan jag få dem förbi samplings modulen?*

* Det bästa sättet att åstadkomma detta är att skriva en anpassad [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), som anger `SamplingPercentage` till 100 på det telemetridata som du vill ha kvar, som du ser nedan. Eftersom initierare är garanterat att köras före telemetri-processorer (inklusive sampling) ser detta till att alla samplings tekniker kommer att ignorera det här objektet från alla exempel på att tänka på.

```csharp
     public class MyTelemetryInitializer : ITelemetryInitializer
      {
         public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)item).SamplingPercentage = 100;
            }
        }
      }
```

## <a name="next-steps"></a>Nästa steg

* [Filtrering](../../azure-monitor/app/api-filtering-sampling.md) kan ge mer strikt kontroll över vad din SDK skickar.
* Läs artikeln om Developer Network [optimera telemetri med Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
