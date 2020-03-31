---
title: Telemetrisprovtagning i Azure Application Insights | Microsoft-dokument
description: Hur man håller volymen av telemetri under kontroll.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: fc9db23f7733f97ca207e834d4543fbdb1b9db5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275833"
---
# <a name="sampling-in-application-insights"></a>Sampling i Application Insights

Sampling är en funktion i [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Det är det rekommenderade sättet att minska telemetritrafik, datakostnader och lagringskostnader, samtidigt som en statistiskt korrekt analys av programdata bevaras. Sampling hjälper dig också att undvika Application Insights begränsning din telemetri. Samplingsfiltret väljer objekt som är relaterade, så att du kan navigera mellan objekt när du gör diagnostiska undersökningar.

När måttantal presenteras i portalen renormalized de att ta hänsyn till provtagning. Om du gör det minimeras eventuella effekter på statistiken.

## <a name="brief-summary"></a>Kort sammanfattning

* Det finns tre olika typer av provtagning: adaptiv provtagning, provtagning med fast hastighet och provtagning för intag.
* Adaptiv sampling är aktiverad som standard i alla de senaste versionerna av Application Insights ASP.NET och ASP.NET Core Software Development Kits (SDK). Den används också av [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).
* Provsmakning med fast hastighet är tillgänglig i de senaste versionerna av Application Insights SDK:er för ASP.NET, ASP.NET Core, Java och Python.
* Inmatningsprovtagning fungerar på tjänsten Application Insights-tjänstslutpunkten. Det gäller endast när inget annat provtagning är i kraft. Om SDK-exemplen tar prover på telemetrin inaktiveras inmatningsprovtagningen.
* Om du loggar anpassade händelser och behöver se till att en uppsättning händelser behålls eller `OperationId` ignoreras tillsammans måste händelserna ha samma värde för webbprogram.
* Om du skriver Analytics-frågor bör du [ta hänsyn till sampling](../../azure-monitor/log-query/aggregations.md). I synnerhet i stället för att `summarize sum(itemCount)`bara räkna poster, bör du använda .
* Vissa telemetrityper, inklusive prestandamått och anpassade mått, behålls alltid oavsett om sampling är aktiverad eller inte.

I följande tabell sammanfattas de provtagningstyper som är tillgängliga för varje SDK och typ av program:

| Programinsikter SDK | Adaptiv provtagning stöds | Provtagning med fast hastighet stöds | Provtagning för intag stöds |
|-|-|-|-|
| ASP.NET | [Ja (på som standard)](#configuring-adaptive-sampling-for-aspnet-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-applications) | Endast om ingen annan provtagning är i kraft |
| ASP.NET Core | [Ja (på som standard)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Ja](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Endast om ingen annan provtagning är i kraft |
| Azure Functions | [Ja (på som standard)](#configuring-adaptive-sampling-for-azure-functions) | Inga | Endast om ingen annan provtagning är i kraft |
| Java | Inga | [Ja](#configuring-fixed-rate-sampling-for-java-applications) | Endast om ingen annan provtagning är i kraft |
| Python | Inga | [Ja](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Endast om ingen annan provtagning är i kraft |
| Alla andra | Inga | Inga | [Ja](#ingestion-sampling) |

> [!NOTE]
> Informationen på större delen av den här sidan gäller för de aktuella versionerna av SDK:erna för Application Insights. Information om äldre versioner av SDK:erna [finns i avsnittet nedan](#older-sdk-versions).

## <a name="types-of-sampling"></a>Typ av provtagning

Det finns tre olika provtagningsmetoder:

* **Anpassad sampling** justerar automatiskt volymen av telemetri som skickas från SDK i din ASP.NET/ASP.NET Core-app och från Azure Functions. Detta är standardprovtagningen när du använder ASP.NET eller ASP.NET Core SDK. Adaptiv sampling är för närvarande endast tillgänglig för ASP.NET telemetri på serversidan och för Azure-funktioner.

* **Provsmakning med fast hastighet** minskar mängden telemetri som skickas från både din ASP.NET eller ASP.NET Core- eller Java-servern och från användarnas webbläsare. Du ställer in hastigheten. Klienten och servern synkroniserar deras sampling så att du i Sök kan navigera mellan relaterade sidvisningar och begäranden.

* **Inmatningsprovtagning** sker vid tjänsten Application Insights-tjänstslutpunkten. En del av telemetrin som kommer från appen tas bort med en samplingsfrekvens som du anger. Det minskar inte telemetritrafiken som skickas från din app, men hjälper dig att hålla dig inom din månatliga kvot. Den största fördelen med intagsprovtagning är att du kan ställa in samplingsfrekvensen utan att distribuera om appen. Intagsprovtagning fungerar enhetligt för alla servrar och klienter, men det gäller inte när andra typer av provtagning är i drift.

> [!IMPORTANT]
> Om provtagningsmetoder med fast eller fast hastighet är i drift inaktiveras intagsprovtagningen.

## <a name="adaptive-sampling"></a>Adaptiv provtagning

Anpassad sampling påverkar volymen av telemetri som skickas från webbserverappen till tjänsten Application Insights-slutpunkten.

> [!TIP]
> Adaptiv sampling är aktiverad som standard när du använder ASP.NET SDK eller ASP.NET Core SDK och är också aktiverat som standard för Azure-funktioner.

Volymen justeras automatiskt för att hålla sig inom en angiven maximal `MaxTelemetryItemsPerSecond`trafikhastighet och styrs via inställningen . Om programmet ger en låg mängd telemetri, till exempel när felsökning eller på grund av låg användning, kommer objekten inte att tas bort av samplingsprocessorn så länge volymen är lägre `MaxTelemetryItemsPerSecond`. I takt med att volymen av telemetri ökar justeras samplingshastigheten så att målvolymen uppnås. Justeringen räknas om med jämna mellanrum och baseras på ett glidande medelvärde av den utgående överföringshastigheten.

För att uppnå målvolymen ignoreras en del av den genererade telemetrin. Men precis som andra typer av samplingar behåller algoritmen relaterade telemetriobjekt. När du till exempel inspekterar telemetrin i Sök kan du hitta begäran som är relaterad till ett visst undantag.

Måttantal som begärandehastighet och undantagsfrekvens justeras för att kompensera för samplingsfrekvensen, så att de visar ungefär korrekta värden i Metric Explorer.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Konfigurera adaptiv sampling för ASP.NET applikationer

> [!NOTE]
> Det här avsnittet gäller ASP.NET program, inte ASP.NET Core-program. [Lär dig mer om hur du konfigurerar anpassad sampling för ASP.NET Core-program senare i det här dokumentet.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

I [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)kan du justera flera `AdaptiveSamplingTelemetryProcessor` parametrar i noden. De siffror som visas är standardvärdena:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Den målhastighet som den adaptiva algoritmen syftar till **på varje servervärd**. Om webbappen körs på många värdar minskar du det här värdet så att du håller dig inom målhastigheten för trafiken på application insights-portalen.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Det intervall med vilket den aktuella telemetrihastigheten omvärderas. Utvärderingen utförs som ett glidande medelvärde. Du kanske vill förkorta detta intervall om din telemetri kan leda till plötsliga skurar.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    När provtagning procentuellt värde ändras, hur snart efter får vi sänka samplingsprocenten igen för att fånga mindre data?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    När provtagning procentuellt värde ändras, hur snart efter får vi öka samplingsprocenten igen för att fånga in mer data?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    När samplingsprocenten varierar, vilket är det lägsta värde vi får ange?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    När samplingsprocenten varierar, vilket är det högsta värde vi får ange?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Vid beräkningen av det glidande medelvärdet anger detta den vikt som ska tilldelas det senaste värdet. Använd ett värde som är lika med eller mindre än 1. Mindre värden gör algoritmen mindre reaktiv vid plötsliga ändringar.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Mängden telemetri som ska provas när appen just har startat. Minska inte det här värdet när du felsöker.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    En semikolonavgränsad lista över typer som du inte vill ska bli föremål för sampling. Erkända typer `Dependency`är: `Exception` `PageView`, `Request` `Event` `Trace`, , , , . All telemetri av de angivna typerna överförs. de typer som inte har angetts kommer att provtas.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    En semikolonavgränsad lista över typer som du vill ta prover på. Erkända typer `Dependency`är: `Exception` `PageView`, `Request` `Event` `Trace`, , , , . De angivna typerna kommer att provtas. all telemetri av de andra typerna kommer alltid att överföras.

**Om du vill stänga** av `AdaptiveSamplingTelemetryProcessor` adaptiv sampling `ApplicationInsights.config`tar du bort noden från .

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativ: Konfigurera adaptiv sampling i kod

I stället för att `.config` ange samplingsparametern i filen kan du programmässigt ange dessa värden.

1. Ta bort `AdaptiveSamplingTelemetryProcessor` alla noder `.config` från filen.
2. Använd följande kodavsnitt för att konfigurera adaptiv sampling:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    (Lär[dig mer om telemetriprocessorer](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Du kan också justera samplingsfrekvensen för varje telemetrityp individuellt, eller till och med utesluta att vissa typer samplas alls:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Konfigurera adaptiv sampling för ASP.NET Core-applikationer

Det finns `ApplicationInsights.config` ingen för ASP.NET Core-program, så all konfiguration sker via kod.
Adaptiv sampling är aktiverad som standard för alla ASP.NET Core-program. Du kan inaktivera eller anpassa samplingsbeteendet.

#### <a name="turning-off-adaptive-sampling"></a>Stänga av adaptiv provtagning

Standardprovtagningsfunktionen kan inaktiveras när application insights-tjänsten lägger till, i metoden `ConfigureServices`, med hjälp av `ApplicationInsightsServiceOptions` filen: `Startup.cs`

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

Ovanstående kod kommer att inaktivera adaptiv sampling. Följ stegen nedan för att lägga till sampling med fler anpassningsalternativ.

#### <a name="configure-sampling-settings"></a>Konfigurera samplingsinställningar

Använd tilläggsmetoder `TelemetryProcessorChainBuilder` som visas nedan för att anpassa samplingsbeteende.

> [!IMPORTANT]
> Om du använder den här metoden för att `aiOptions.EnableAdaptiveSampling` konfigurera `false` sampling, se till att ställa in egenskapen till när du ringer `AddApplicationInsightsTelemetry()`.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Konfigurera adaptiv sampling för Azure-funktioner

Följ instruktionerna från [den här sidan](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) för att konfigurera anpassad sampling för appar som körs i Azure Functions.

## <a name="fixed-rate-sampling"></a>Provtagning med fast hastighet

Tidsbegränsad sampling minskar trafiken som skickas från webbservern och webbläsare. Till skillnad från adaptiv sampling minskar den telemetri med en fast hastighet som du har beslutat om. Provtagning med fast hastighet är tillgänglig för ASP.NET, ASP.NET Core-, Java- och Python-program.

Liksom andra provtagningstekniker behåller detta också relaterade poster. Den synkroniserar också klient- och serverprovtagningen så att relaterade objekt behålls, till exempel när du tittar på en sidvy i Sök kan du hitta dess relaterade serverbegäranden. 

I Metrics Explorer multipliceras priser som begäran och undantagsantal med en faktor för att kompensera för samplingsfrekvensen, så att de är ungefär korrekta.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Konfigurera provtagning med fast hastighet för ASP.NET applikationer

1. **Inaktivera adaptiv**sampling [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md): I , `AdaptiveSamplingTelemetryProcessor` ta bort eller kommentera noden.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Aktivera provtagningsmodulen med fast hastighet.** Lägg till det [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)här kodavsnittet i:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      I stället för att ange samplingsparametern i `ApplicationInsights.config` filen kan du också programmässigt ange följande värden:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    (Lär[dig mer om telemetriprocessorer](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Konfigurera provtagning med fast hastighet för ASP.NET Core-program

1. **Inaktivera adaptiv provtagning:** Ändringar kan `ConfigureServices` göras `ApplicationInsightsServiceOptions`i metoden med hjälp av:

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

2. **Aktivera provtagningsmodulen med fast hastighet.** Ändringar kan göras `Configure` i metoden som visas i nedanstående utdrag:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Konfigurera tidsbegränsad sampling för Java-program

Som standard är ingen sampling aktiverad i Java SDK. För närvarande stöder den endast provtagning med fast hastighet. Adaptiv sampling stöds inte i Java SDK.

1. Ladda ner och konfigurera ditt webbprogram med de senaste [Application Insights Java SDK](../../azure-monitor/app/java-get-started.md).

2. **Aktivera samplingsmodulen med fast hastighet** genom `ApplicationInsights.xml` att lägga till följande kodavsnitt i filen:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. Du kan inkludera eller utesluta specifika typer av telemetri `Processor` från sampling `FixedRateSamplingTelemetryProcessor`med hjälp av följande taggar i taggens:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

De telemetrityper som kan inkluderas eller uteslutas `Event`från sampling `Trace`är: `Dependency`, , `Exception`, `PageView`, `Request`och .

> [!NOTE]
> För samplingsprocenten väljer du en procentsats som är nära 100/N där N är ett heltal.  Sampling stöder för närvarande inte andra värden.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Konfigurera provtagning med fast hastighet för OpenCensus Python-program

Instrumentera ditt program med de senaste [OpenCensus Azure Monitor-exportörerna](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> Provtagning med fast ränta är inte tillgängligt för den måttexportör som exporterar. Detta innebär att anpassade mått är de enda typerna av telemetri där sampling inte kan konfigureras. Den mått exportör kommer att skicka alla telemetri som den spårar.

#### <a name="fixed-rate-sampling-for-tracing"></a>Provtagning med fast hastighet för spårning ####
Du kan ange en `sampler` som en del av din `Tracer`-konfiguration. Om ingen explicit sampler `ProbabilitySampler` tillhandahålls, kommer den att användas som standard. Den `ProbabilitySampler` skulle använda en hastighet av 1 / 10000 som standard, vilket innebär att en av varje 10000 förfrågningar kommer att skickas till Application Insights. Se nedan om du vill ange en samplingsfrekvens.

Om du vill ange samplingsfrekvensen kontrollerar du att du `Tracer` anger en provtagare med en samplingsfrekvens mellan 0,0 och 1,0. En samplingsfrekvens på 1,0 representerar 100 %, vilket innebär att alla dina begäranden skickas som telemetri till Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Provtagning med fast hastighet för stockar ####
Du kan konfigurera sampling `AzureLogHandler` med fast `logging_sampling_rate` hastighet för genom att ändra det valfria argumentet. Om inget argument har lämnats kommer en samplingsfrekvens på 1,0 att användas. En samplingsfrekvens på 1,0 representerar 100 %, vilket innebär att alla dina begäranden skickas som telemetri till Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Konfigurera tidsbegränsad sampling för webbsidor med JavaScript

JavaScript-baserade webbsidor kan konfigureras för att använda Application Insights. Telemetri skickas från klientprogrammet som körs i användarens webbläsare och sidorna kan vara värd från vilken server som helst.

När du [konfigurerar dina JavaScript-baserade webbsidor för Application Insights](javascript.md)ändrar du det JavaScript-kodavsnitt som du får från application insights-portalen.

> [!TIP]
> I ASP.NET appar med JavaScript ingår går kodavsnittet vanligtvis in `_Layout.cshtml`.

Infoga en `samplingPercentage: 10,` linje som före instrumenteringsnyckeln:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

För samplingsprocenten väljer du en procentsats som är nära 100/N där N är ett heltal. Sampling stöder för närvarande inte andra värden.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>Samordnande server- och klientprovtagning

JavaScript SDK på klientsidan deltar i provbeslutningar med fast hastighet tillsammans med SDK på serversidan. De instrumenterade sidorna skickar endast telemetri på klientsidan från samma användare för vilken SDK på serversidan fattade sitt beslut att inkludera i provtagningen. Den här logiken är utformad för att upprätthålla integriteten för användarsessioner över klient- och serverprogram. Från ett visst telemetriobjekt i Application Insights kan du därför hitta alla andra telemetriobjekt för den här användaren eller sessionen och i Sök kan du navigera mellan relaterade sidvisningar och begäranden.

Om telemetrin på klient- och serversidan inte visar koordinerade exempel:

* Kontrollera att du har aktiverat sampling både på servern och klienten.
* Kontrollera att du anger samma samplingsprocent i både klienten och servern.
* Kontrollera att SDK-versionen är 2.0 eller högre.

## <a name="ingestion-sampling"></a>Provtagning av förtäring

Inmatningsprovtagningen fungerar vid den punkt där telemetrin från webbservern, webbläsarna och enheterna når tjänsten Application Insights-slutpunkten. Även om det inte minskar telemetritrafiken som skickas från din app minskar den det belopp som bearbetas och behålls (och debiteras för) av Application Insights.

Använd den här typen av sampling om din app ofta överskrider sin månatliga kvot och du inte har möjlighet att använda någon av de SDK-baserade typerna av sampling. 

Ange samplingsfrekvensen på sidan Användning och uppskattade kostnader:

![I programmets översiktsblad klickar du på Inställningar, Kvot, Exempel och väljer sedan en samplingsfrekvens och klickar på Uppdatera.](./media/sampling/data-sampling.png)

Precis som andra typer av samplingar behåller algoritmen relaterade telemetriobjekt. När du till exempel inspekterar telemetrin i Sök kan du hitta begäran som är relaterad till ett visst undantag. Måttantal som begärandegrad och undantagsfrekvens behålls korrekt.

Datapunkter som ignoreras genom sampling är inte tillgängliga i någon Application Insights-funktion, till exempel [Kontinuerlig export](../../azure-monitor/app/export-telemetry.md).

Intagsprovtagning fungerar inte när adaptiv provtagning eller provtagning med fast hastighet är i drift. Adaptiv sampling aktiveras som standard när ASP.NET SDK eller ASP.NET Core SDK används, eller när Application Insights är aktiverat i [Azure App Service](azure-web-apps.md) eller med hjälp av StatusÖvervakaren. När telemetri tas emot av tjänsten Application Insights-tjänstslutpunkten undersöks telemetrin och om samplingsfrekvensen rapporteras vara mindre än 100 % (vilket indikerar att telemetri samplas) ignoreras inmatningsprovtagningshastigheten som du anger.

> [!WARNING]
> Värdet som visas på portalpanelen anger det värde som du anger för inmatningsprovtagning. Den representerar inte den faktiska provtagningsfrekvensen om någon form av SDK-provtagning (adaptiv eller provtagning med fast hastighet) är i drift.

## <a name="when-to-use-sampling"></a>När du ska använda provtagning

I allmänhet, för de flesta små och medelstora program behöver du inte provtagning. Den mest användbara diagnostikinformationen och den mest exakta statistiken erhålls genom att samla in data om alla dina användaraktiviteter. 

De främsta fördelarna med provtagning är följande:

* Application Insights-tjänsten sjunker ("throttles") datapunkter när din app skickar en mycket hög telemetrihastighet på kort tid. Sampling minskar sannolikheten för att ditt program kommer att se begränsning uppstå.
* Så här håller du dig inom [kvoten](pricing.md) för datapunkter för din prisnivå. 
* Så här minskar du nätverkstrafiken från insamlingen av telemetri. 

### <a name="which-type-of-sampling-should-i-use"></a>Vilken typ av provtagning ska jag använda?

**Använd intagsprovtagning om:**

* Du använder ofta din månatliga kvot av telemetri.
* Du får för mycket telemetri från användarnas webbläsare.
* Du använder en version av SDK som inte stöder sampling , till exempel ASP.NET versioner tidigare än 2.

**Använd provtagning med fast hastighet om**

* Du vill ha synkroniserad sampling mellan klient och server så att du, när du undersöker händelser i [Sök,](../../azure-monitor/app/diagnostic-search.md)kan navigera mellan relaterade händelser på klienten och servern, till exempel sidvisningar och HTTP-begäranden.
* Du är säker på lämplig samplingsprocent för din app. Det bör vara tillräckligt hög för att få korrekta mått, men under den hastighet som överskrider din priskvot och begränsningsgränserna.

**Använd adaptiv provtagning:**

Om villkoren för användning av de andra formerna av provtagning inte gäller rekommenderar vi adaptiv provtagning. Den här inställningen är aktiverad som standard i ASP.NET/ASP.NET Core SDK. Det kommer inte att minska trafiken förrän en viss miniminivå har uppnåtts, därför kommer låg användning platser förmodligen inte tas prov alls.

## <a name="knowing-whether-sampling-is-in-operation"></a>Att veta om provtagningen är i drift

Om du vill ta reda på den faktiska samplingsfrekvensen oavsett var den har tillämpats använder du en [Analytics-fråga](../../azure-monitor/app/analytics.md) som denna:

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Om du `RetainedPercentage` ser att för någon typ är mindre än 100, samplas den typen av telemetri.

> [!IMPORTANT]
> Application Insights exempel inte session, mått (inklusive anpassade mått) eller prestandaräknare telemetrityper i någon av provtagningsteknikerna. Dessa typer är alltid undantagna från provtagning eftersom en minskning av precisionen kan vara mycket oönskade för dessa telemetrityper.

## <a name="how-sampling-works"></a>Så här fungerar sampling

Samplingsalgoritmen bestämmer vilka telemetriobjekt som ska släppas och vilka som ska behållas. Detta gäller oavsett om sampling görs av SDK eller i application insights-tjänsten. Provtagningsbeslutet baseras på flera regler som syftar till att bevara alla inbördes relaterade datapunkter intakta, vilket upprätthåller en diagnostisk upplevelse i Application Insights som är användbar och tillförlitlig även med en reducerad datauppsättning. Om din app till exempel har en misslyckad begäran som ingår i ett exempel, behålls de ytterligare telemetriobjekten (till exempel undantag och spårningar som loggats för den här begäran). Provtagning antingen håller eller droppar dem alla tillsammans. När du tittar på information om begäran i Application Insights kan du därför alltid se begäran tillsammans med tillhörande telemetriobjekt.

Provtagningsbeslutet baseras på begärans drift-ID, vilket innebär att alla telemetriartiklar som tillhör en viss operation antingen bevaras eller tas bort. För telemetriobjekt som inte har en åtgärds-ID-uppsättning (till exempel telemetriobjekt som rapporteras från asynkrona trådar utan HTTP-kontext) samlar sampling helt enkelt en procentandel telemetriobjekt av varje typ.

När du presenterar telemetri tillbaka till dig justerar application insights-tjänsten måtten med samma samplingsprocent som användes vid insamlingstillfället, för att kompensera för de saknade datapunkterna. Därför, när man tittar på telemetri i Application Insights, användarna ser statistiskt korrekta approximationer som är mycket nära de verkliga siffrorna.

Noggrannheten i approximationen beror till stor del på den konfigurerade samplingsprocenten. Dessutom ökar noggrannheten för program som hanterar en stor mängd allmänt liknande förfrågningar från många användare. Å andra sidan, för program som inte fungerar med en betydande belastning, är provtagning inte behövs eftersom dessa program kan vanligtvis skicka alla sina telemetri medan du håller sig inom kvoten, utan att orsaka dataförlust från begränsning. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

*Vad är standardprovtagningsbeteendet i ASP.NET och ASP.NET Core SDK:er?*

* Om du använder en av de senaste versionerna av ovanstående SDK aktiveras adaptiv sampling som standard med fem telemetriobjekt per sekund.
  Det finns `AdaptiveSamplingTelemetryProcessor` två noder tillagda som `Event` standard och den ena `Event` innehåller typen i sampling, medan den andra utesluter typen från sampling. Den här konfigurationen innebär att SDK försöker begränsa telemetriobjekt `Event` till fem telemetriobjekt av typer och fem telemetriobjekt av alla andra typer som kombineras, vilket säkerställer att `Events` de samplas separat från andra telemetrityper. Händelser används vanligtvis för affärstelemetri och bör troligen inte påverkas av diagnostiska telemetrivolymer.
  
  Följande visar standardfilen `ApplicationInsights.config` som genereras. I ASP.NET Core är samma standardbeteende aktiverat i kod. Använd [exemplen i det tidigare avsnittet på den här sidan](#configuring-adaptive-sampling-for-aspnet-core-applications) för att ändra standardbeteendet.

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

*Kan telemetri provtas mer än en gång?*

* Nej. SamplingTelemetryProcessorer ignorerar artiklar från samplingsöverväganden om artikeln redan har provtagits. Detsamma gäller även för intagsprovtagning, som inte kommer att tillämpa provtagning på de artiklar som redan provtagits i själva SDK.

*Varför är inte sampling en enkel "samla X procent av varje telemetri typ"?*

* Även om den här samplingsmetoden skulle ge en hög precisionsnivå i metriska approximationer, skulle det bryta möjligheten att korrelera diagnostikdata per användare, session och begäran, vilket är avgörande för diagnostik. Därför fungerar sampling bättre med principer som "samla in alla telemetriobjekt för X procent av appanvändarna" eller "samla in all telemetri för X procent av appbegäranden". För telemetriobjekt som inte är associerade med begäranden (t.ex. bakgrundsasynkron bearbetning) är återgången att "samla in X procent av alla artiklar för varje telemetrityp". 

*Kan samplingsprocenten ändras över tid?*

* Ja, adaptiv provtagning ändrar gradvis samplingsprocenten, baserat på telemetrins för närvarande observerade volym.

*Om jag använder provsmakning med fast hastighet, hur vet jag vilken samplingsprocent som fungerar bäst för min app?*

* Ett sätt är att börja med adaptiv provtagning, ta reda på vilken hastighet den sätter på (se ovanstående fråga) och sedan byta till fast sampling med hjälp av denna hastighet. 
  
    Annars måste du gissa. Analysera din aktuella telemetrianvändning i Application Insights, observera eventuell begränsning som sker och uppskatta volymen för den insamlade telemetrin. Dessa tre indata, tillsammans med den valda prisnivån, föreslår hur mycket du kanske vill minska volymen på den insamlade telemetrin. En ökning av antalet användare eller någon annan förskjutning av volymen av telemetri kan dock göra uppskattningen ogiltig.

*Vad händer om jag konfigurerar samplingsprocenten för låg?*

* Alltför låga samplingsprocent orsakar överaggressiv sampling och minskar noggrannheten i approximationerna när Application Insights försöker kompensera visualiseringen av data för datavolymreducering. Även din diagnostiska upplevelse kan påverkas negativt, eftersom vissa av de sällan misslyckas eller långsamma begäranden kan provtas ut.

*Vad händer om jag konfigurerar samplingsprocenten för hög?*

* Om du konfigurerar en för hög samplingsprocent (inte tillräckligt aggressivt) leder det till en otillräcklig minskning av volymen på den insamlade telemetrin. Du kan fortfarande uppleva telemetridataförlust relaterad till begränsning, och kostnaden för att använda Application Insights kan vara högre än du planerat på grund av överliggande avgifter.

*På vilka plattformar kan jag använda sampling?*

* Intagsprovtagning kan ske automatiskt för telemetri över en viss volym, om SDK inte utför sampling. Den här konfigurationen skulle fungera, till exempel om du använder en äldre version av ASP.NET SDK eller Java SDK.
* Om du använder den aktuella ASP.NET eller ASP.NET Core SDK:er (finns antingen i Azure eller på din egen server) får du anpassad sampling som standard, men du kan växla till fast hastighet enligt beskrivningen ovan. Med provsmakning med fast hastighet synkroniseras webbläsarenSDK automatiskt med exempelrelaterade händelser. 
* Om du använder den aktuella Java SDK kan du konfigurera `ApplicationInsights.xml` för att aktivera provupptagning med fast hastighet. Samplingen är inaktiverad som standard. Med provtagning med fast hastighet synkroniseras webbläsaren SDK och servern automatiskt med exempelrelaterade händelser.

*Det finns vissa sällsynta händelser jag alltid vill se. Hur kan jag få dem förbi samplingsmodulen?*

* Det bästa sättet att uppnå detta är att skriva en anpassad `SamplingPercentage` [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer), som ställer in till 100 på telemetri objektet du vill behållas, som visas nedan. Eftersom initializers garanteras att köras före telemetriprocessorer (inklusive provtagning), säkerställer detta att alla provtagningstekniker ignorerar denna post från eventuella provtagningsöverväganden. Anpassade telemetriinitierare är tillgängliga i ASP.NET SDK, ASP.NET Core SDK, JavaScript SDK och Java SDK. Du kan till exempel konfigurera en telemetriinitierare med hjälp av ASP.NET SDK:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Äldre SDK-versioner

Adaptiv sampling är tillgänglig för Application Insights SDK för ASP.NET v2.0.0-beta3 och senare, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 och senare, och är aktiverat som standard.

Provsmakning med fast hastighet är en funktion i SDK i ASP.NET versioner från 2.0.0 och Java SDK version 2.0.1 och framåt.

Före v2.5.0-beta2 av ASP.NET SDK och v2.2.0-beta3 av ASP.NET Core SDK baserades samplingsbeslutet på hash-värdet för användar-ID för program som definierar "användare" (det vill än mest typiska webbapplikationer). För de typer av program som inte definierade användare (t.ex. webbtjänster) baserades samplingsbeslutet på åtgärds-ID för begäran. De senaste versionerna av ASP.NET och ASP.NET Core SDK:er använder åtgärds-ID för samplingsbeslutet.

## <a name="next-steps"></a>Nästa steg

* [Filtrering](../../azure-monitor/app/api-filtering-sampling.md) kan ge striktare kontroll över vad din SDK skickar.
* Läs artikeln Optimera telemetri för utvecklarnätverk [med Application Insights](https://msdn.microsoft.com/magazine/mt808502.aspx).
