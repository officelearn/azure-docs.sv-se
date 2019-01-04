---
title: Telemetri linjer i Azure Application Insights | Microsoft Docs
description: Så här att hålla volymen telemetri under kontroll.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/02/2018
ms.reviewer: vitalyg
ms.author: mbullwin
ms.openlocfilehash: 17725f830b347839ddc57eba61ef7c65d5253568
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970890"
---
# <a name="sampling-in-application-insights"></a>Sampling i Application Insights


Sampling är en funktion i [Azure Application Insights](app-insights-overview.md). Det är det rekommenderade sättet att minska telemetritrafik och lagring samtidigt bevara en statistiskt korrekt analys av programdata. Objekt som är relaterade väljer filtret så att du kan navigera mellan objekt när du gör diagnostiska undersökningar.
När måttet antal upprättas till dig i portalen, är de renormalized hänsyn till sampling är att minimera dess inverkan på statistik.

Sampling minskar kostnader och trafik och hjälper dig att undvika begränsning.

## <a name="in-brief"></a>Kort sagt:
* Sampling behåller 1 i *n* registrerar och ignorerar resten. Det kan till exempel behåller 1: 5 händelser, en samplingsfrekvensen 20%. 
* Sampling sker automatiskt om ditt program skickar stora mängder telemetri i ASP.NET web server-appar.
* Du kan också ange sampling manuellt, antingen i portalen på användning och uppskattade kostnader sida. eller i ASP.NET SDK i .config-fil. eller i Java-SDK i filen ApplicationInsights.xml också minska nätverkstrafiken.
* Om du loggar anpassade händelser och du vill kontrollera att en uppsättning händelser är antingen bevaras eller tas bort tillsammans, se till att de har samma åtgärds-ID-värde.
* Sampling divisorn *n* rapporteras i varje post i egenskapen `itemCount`, som i sökningen visas under namnet ”begäran antal” eller ”händelseantal”. När sampling inte är i drift, `itemCount==1`.
* Om du skriver analysfrågor, bör du [ta hänsyn till sampling](../azure-monitor/log-query/aggregations.md). I synnerhet i stället för bara räkna poster, du bör använda `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typer av sampling
Det finns tre alternativ sampling metoder:

* **Adaptiv sampling** justeras automatiskt mängden telemetri som skickas från SDK: N i din ASP.NET-app. Från och med SDK v 2.0.0-beta3 är det här standardmetoden för sampling. Adaptiv sampling är för närvarande bara tillgänglig för ASP.NET serversidan telemetri. För Asp.NET Core-program som riktar in sig på fullständiga Framework, är adaptiv sampling tillgänglig från Microsoft.ApplicationInsights.AspNetCore SDK-version 1.0.0. Adaptiv sampling är tillgänglig från 2.2.0-beta1 av Microsoft.ApplicationInsights.AspNetCore SDK för Asp.NET Core-program som riktar in sig på NetCore.

* **Fast räntesats sampling** minskar mängden telemetri som skickas från både din ASP.NET- eller Java-server och från användarnas webbläsare. Du ställer in frekvensen. Klienten och servern ska synkronisera sina sampling så att, i sökningen som du kan navigera mellan relaterade sidvisningar och förfrågningar.
* **Inmatningssampling** fungerar i Azure-portalen. Det tar bort några av telemetrin som tas emot från din app på en samplingsfrekvensen som du anger. Det minskar inte telemetritrafik som skickas från din app, men det hjälper dig att skydda inom din månatliga kvot. Den största fördelen med inmatningssampling är att du kan ange samplingsfrekvensen utan att omdistribuera din app och den fungerar på ett enhetligt sätt på alla servrar och klienter. 

Om Adaptiv eller fast pris sampling är i drift, har inmatningssampling inaktiverats.

## <a name="ingestion-sampling"></a>Inmatningssampling
Den här typen av sampling körs vid den punkt där telemetri från webbservern, webbläsare och enheter når Application Insights-tjänstslutpunkt. Även om det inte minska telemetritrafik som skickas från din app, den minskar den bearbetas och behålls (och debiteras för) av Application Insights.

Använd den här typen av sampling om din app går ofta via sin månatliga kvot och du inte har alternativet att använda något av de SDK-baserade typerna av sampling. 

Ange samplingsfrekvensen i användning och uppskattade kostnader:

![Från översiktsbladet program klickar du på inställningar, kvot, exempel, och sedan väljer en samplingsfrekvensen och klicka på Uppdatera.](./media/app-insights-sampling/04.png)

Precis som andra typer av sampling behåller algoritmen relaterad telemetri objekt. Till exempel när du granska telemetri i Search kommer du att kunna hitta den relaterade till ett visst undantag. Mått räknar, till exempel förfrågningar och antal undantag bevaras korrekt.

Datapunkter som ignoreras av sampling är inte tillgängliga i alla Application Insights-funktioner som [löpande Export](../azure-monitor/app/export-telemetry.md).

Inmatningssampling fungerar inte när SDK-baserade anpassningsbara eller fast räntesats sampling är i drift. Observera att Adaptiv sampling är aktiverat som standard när ASP.NET SDK har aktiverats i Visual Studio eller med hjälp av statusövervakaren och inmatningssampling är inaktiverat. Om samplingsfrekvensen på SDK är mindre än 100%, ignoreras inmatning samplingsfrekvensen som du anger.

> [!WARNING]
> Värdet som visas på panelen visar det värde som du angett för inmatningssampling. Den representerar inte faktiska samplingsfrekvensen om SDK sampling är i drift.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Adaptiv sampling på webbservern
Adaptiv sampling är tillgängliga för Application Insights SDK för ASP.NET v 2.0.0-beta3 och senare och är aktiverad som standard. 

Adaptiv sampling påverkar mängden telemetri som skickas från web server-app till Application Insights-tjänstslutpunkt. Volymen justeras automatiskt så att du lagrar inom en angiven högsta mängden trafik.

Det fungerar inte vid låg mängder telemetri, så en app i felsökning eller en webbplats med låg belastning påverkas inte.

För att uppnå målvolymen viss telemetri som genererade ignoreras. Men precis som andra typer av sampling algoritmen behåller relaterad telemetri objekt. Till exempel när du granska telemetri i Search kommer du att kunna hitta den relaterade till ett visst undantag. 

Mått räknar, till exempel förfrågningar och antal undantag justeras för att kompensera för samplingsfrekvensen, så att de visar ungefär rätt värden i Metric Explorer.

### <a name="update-nuget-packages"></a>Uppdatera NuGet-paket ###

Uppdatera ditt projekts NuGet-paket till senast *förhandsversioner* version av Application Insights. Högerklicka på projektet i Solution Explorer i Visual Studio, väljer hantera NuGet-paket, kontrollera **inkludera förhandsversion** och Sök efter Microsoft.ApplicationInsights.Web. 

### <a name="configuring-adaptive-sampling"></a>Konfigurera Adaptiv sampling ###

I [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md), du kan justera flera parametrar i den `AdaptiveSamplingTelemetryProcessor` noden. Siffrorna som visas är standardvärden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Mål-hastighet som algoritmen anpassningsbar syftar för **på varje server-värd**. Om din webbapp körs på flera värdar kan minska det här värdet att den ska finnas kvar i dina mål mängden trafik i Application Insights-portalen.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Intervallet som aktuellt antal telemetri är ny utvärdering. Utvärdering körs ett glidande medelvärde. Du kanske vill förkorta intervallet om din telemetri kan tänkas plötsliga toppar.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    När provtagning procent ändras, hur snart när vi tillåts att sänka samplingsprocenten igen för att samla in mindre mängd data.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    När detta sker procent ändras, hur snart när vi får öka samplingsprocenten igen för att samla in mer data.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Eftersom sampling procent varierar, är vad det minsta värdet som vi är tillåtet att ställa in.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Eftersom sampling procent varierar, är vad det maximala värdet som vi är tillåtet att ställa in.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Vikten tilldelats det senaste värdet i beräkningen av glidande medelvärde. Använd ett värde som är lika med eller mindre än 1. Lägre värden gör algoritmen reagera mindre plötsliga ändringar.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Värdet som tilldelas när appen har startats. Inte minska detta medan du felsöker. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    En semikolonavgränsad lista av typer som du inte vill samlas in. Identifierade typer är: Beroende, händelse, undantag, sidvisningar, begäran, spåra. Alla instanser av de angivna typerna överförs. de typer som inte har angetts samplas.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    En semikolonavgränsad lista över typer som du vill ska samlas in. Identifierade typer är: Beroende, händelse, undantag, sidvisningar, begäran, spåra. De angivna typerna samplas; alla instanser av andra typer skickas alltid.


**Stänga av** Adaptiv sampling, ta bort noden adaptivesamplingtelemetryprocessor från applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativ: Konfigurera Adaptiv sampling i kod
I stället för att ange parametern sampling i .config-filen kan ange du programmässigt dessa värden. På så sätt kan du ange en återanropsfunktion som anropas när samplingsfrekvensen görs en ny utvärdering. Du kan använda detta, till exempel att ta reda på vilka samplingsfrekvensen som används.

Ta bort den `AdaptiveSamplingTelemetryProcessor` nod från .config-filen.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Lär dig mer om telemetri processorer](../azure-monitor/app/api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Sampling för webbsidor med JavaScript
Du kan konfigurera webbsidor för fast räntesats linjer från en server. 

När du [konfigurera webbsidor för Application Insights](../azure-monitor/app/javascript.md), ändra JavaScript-kodavsnitt som du får från Application Insights-portalen. (I ASP.NET-appar i kodfragment vanligtvis används i _Layout.cshtml.)  Infoga en rad som `samplingPercentage: 10,` innan instrumenteringsnyckeln:

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

Samplingsprocenten, Välj i procent som ligger nära 100/N där N är ett heltal.  Samlar för närvarande stöd inte för andra värden.

Om du aktiverar också fast räntesats sampling på servern, synkroniserar klienter och servrar så att, i sökningen som du kan navigera mellan relaterade sidvisningar och förfrågningar.

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>Fast räntesats samplingsfrekvensen för ASP.NET och Java-webbplatser
Fast pris sampling minskar den trafik som skickas från webbservern och webbläsare. Till skillnad från Adaptiv sampling minskar den telemetri till en fast kostnad som du valt. Det synkroniserar även klienten och servern sampling så att relaterade objekt bevaras – till exempel när du tittar på en Sidvisning i Search, du kan hitta relaterade begäran.

Samplingsalgoritmen behåller relaterade objekt. För varje HTTP-begäran är händelse, förfrågan och dess relaterade händelser antingen tas bort eller överförs tillsammans. 

I Metrics Explorer multipliceras priser, till exempel antalet begäranden och undantag med en faktor att kompensera för samplingsfrekvensen, så att de är ungefär rätt.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurera fast räntesats sampling i ASP.NET ###

1. **Uppdatera ditt projekts NuGet-paket** till senast *förhandsversioner* version av Application Insights. Högerklicka på projektet i Solution Explorer i Visual Studio, väljer hantera NuGet-paket, kontrollera **inkludera förhandsversion** och Sök efter Microsoft.ApplicationInsights.Web. 
2. **Inaktivera adaptiv sampling**: I [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md), ta bort eller kommentera ut den `AdaptiveSamplingTelemetryProcessor` noden.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **Aktivera insamlingsmodulen för fast räntesats.** Lägg till det här kodfragmentet att [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurera fast räntesats sampling i JAVA ###

1. Hämta och konfigurera ditt webbprogram med senaste [application insights java SDK](../azure-monitor/app/java-get-started.md)

2. **Aktivera fast räntesats insamlingsmodulen** genom att lägga till följande kodavsnitt ApplicationInsights.xml-fil.

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuiltInProcessors>
    <TelemetryProcessors/>
```

3. Du kan inkludera eller exkludera vissa typer av telemetri från med hjälp av följande taggar i Processor-taggen ”FixedRateSamplingTelemetryProcessor”
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
De typer av telemetri som kan inkluderas eller uteslutas från sampling är: Beroende, händelse, undantag, sidvisningar, begäran och spårning.

> [!NOTE]
> Samplingsprocenten, Välj i procent som ligger nära 100/N där N är ett heltal.  Samlar för närvarande stöd inte för andra värden.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativ: aktivera fast räntesats linjer i serverkoden
I stället för att ange parametern sampling i .config-filen kan ange du programmässigt dessa värden. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Lär dig mer om telemetri processorer](../azure-monitor/app/api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>När du ska använda sampling?
Adaptiv sampling aktiveras automatiskt om du använder ASP.NET SDK version 2.0.0-beta3 eller senare. Du kan aktivera inmatningssampling att Application Insights ska samlas in insamlade data oavsett vilken version av SDK: N som du använder.

Som standard aktiveras ingen sampling i Java SDK. Det stöder för närvarande endast fast pris Sampling. Adaptiv Sampling stöds inte i Java-SDK.

I allmänhet för de flesta små och medelstora program behöver du inte sampling. De mest användbara diagnostisk information och mest korrekta statistik hämtas genom att samla in data på alla användaraktiviteter. 

De huvudsakliga fördelarna med sampling är:

* Application Insights service släpper (”begränsas”) datapunkter när din app skickar en mycket hög andel telemetri i korthet tidsintervall. 
* Att hålla inom den [kvot](app-insights-pricing.md) med datapunkter för din prisnivå. 
* Att minska nätverkstrafiken från mängden telemetri. 

### <a name="which-type-of-sampling-should-i-use"></a>Vilken typ av sampling ska jag använda?
**Använd inmatning sampling om:**

* Du gå ofta igenom din månatliga kvot för telemetri.
* Du använder en version av SDK: N som inte stöder sampling – till exempel ASP.NET-versioner tidigare än 2.
* Du får mycket telemetri från användarnas webbläsare.

**Använd fast räntesats sampling om:**

* Du använder Application Insights SDK för ASP.NET web services-version 2.0.0 eller senare eller Java SDK v2.0.1 eller senare, och
* Du vill synkroniserade sampling mellan klienten och servern, så att när du undersöker händelser i [Search](../azure-monitor/app/diagnostic-search.md), du kan navigera mellan relaterade händelser på klienten och servern, till exempel sidvyer och http-begäranden.
* Du är för lämpliga samplingsprocenten för din app. Det bör vara tillräckligt högt för att få exakta mått, men under hastigheten som överskrider din prisnivå kvoter och begränsningar gränser. 

**Använd Adaptiv sampling:**

Om villkoren kan använda andra typer av sampling inte gäller, rekommenderar vi Adaptiv sampling. Detta är aktiverat som standard i ASP.NET server-SDK, version 2.0.0-beta3 eller senare. Det kommer inte att minska trafiken tills en viss taxa har uppnåtts, därför låg användning platser påverkas inte.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Hur vet jag om sampling pågår?
För att identifiera faktiska samplingsfrekvensen oavsett om den har tillämpats, använda en [analysfråga](../azure-monitor/app/analytics.md) som detta:

```
union * 
| where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h), itemType
| render timechart 
```

Bevaras i varje post, `itemCount` anger hur många ursprungliga poster som representerar, lika med 1 + antalet tidigare borttagna poster. 

## <a name="how-does-sampling-work"></a>Hur fungerar sampling?
Fast räntesats sampling funktion i ASP.NET-versioner från 2.0.0 SDK och Java SDK version 2.0.1 och och senare. Adaptiv sampling är en funktion av SDK i ASP.NET-versioner från 2.0.0 och senare. Inmatningssampling är en funktion i Application Insights-tjänsten och kan vara i drift om SDK inte presterar sampling. 

Samplingsalgoritmen avgör vilka objekt för telemetri att släppa och vilka som ska sparas (oavsett om den i SDK eller i Application Insights-tjänsten). Sampling beslutet baseras på flera regler som syftar till att bevara alla relaterade datapunkter intakta, underhålla en diagnostisk upplevelse i Application Insights som är användbara och tillförlitlig även med en lägre datauppsättning. Exempel: om din app skickar ytterligare telemetri objekt (till exempel undantag och spårningar som loggats från den här begäran) för en misslyckad förfrågning, sampling inte delar denna begäran och annan telemetri. Det ser till att eller utelämnar dem allt på samma plats. När du tittar på Frågedetaljer i Application Insights kan du därför alltid se begäran tillsammans med dess tillhörande telemetri-objekt. 

Sampling beslutet baseras på åtgärds-id för begäran, vilket innebär att alla telemetri-objekt som hör till en viss åtgärd antingen bevaras eller tas bort. För telemetri-objekt som inte har åtgärden avbildar id set (till exempel telemetri artiklar som rapporterats från asynkrona trådar med ingen http-kontexten) sampling helt enkelt en procentandel av telemetri objekt av varje typ av. Före 2.5.0-beta2 av .NET SDK och 2.2.0-beta3 av ASP.NET Core SDK, sampling beslutet baserades på hash för användar-id för program som definierar ”user” (det vill säga de vanligaste webbprogram). För typerna av program som inte har definierat användare (till exempel web services) har sampling beslut utifrån åtgärds-id för begäran.

När du presenterar telemetri till dig, justeras Application Insights-tjänsten mått med samma samplingsprocenten som användes vid tidpunkten för samlingen, för att kompensera för de saknade datapunkterna. När du tittar på telemetrin i Application Insights, därför kan ser användarna statistiskt korrekt med ungefärliga datum som ligger mycket nära reella tal.

Det arbete du utfört uppskattning beror huvudsakligen på konfigurerade samplingsprocenten. Dessutom ökar noggrannheten för program som hanterar ett stort antal Allmänt liknande begäranden från många olika användare. Å andra sidan för program som inte fungerar med en avsevärd belastning, behövs sampling inte dessa program kan vanligtvis skicka all telemetri utifrån kvoten utan att orsaka förlust av data från begränsning. 

> [!WARNING]
> Application Insights inte exempel telemetrityper mått och sessioner. Minskad precisionen kan vara mycket oönskade för dessa typer av telemetri.
> 

### <a name="adaptive-sampling"></a>Adaptiv sampling
Adaptiv sampling lägger till en komponent som övervakar den aktuella överföringshastigheten från SDK och justerar samplingsprocenten att hålla sig inom den maximala hastigheten mål. Justering beräknas med jämna mellanrum och baseras på ett glidande medelvärde för utgående överföringshastigheten.

## <a name="sampling-and-the-javascript-sdk"></a>Sampling och JavaScript-SDK
Klientsidan (JavaScript) SDK deltar i fast räntesats sampling tillsammans med SDK för serversidan. Sidorna instrumenterade endast skickar telemetri på klientsidan från samma användare som gjort serversidan beslutet att ”exempel på”. Den här logiken är utformad för att upprätthålla integriteten på användarens session i en klient - och en server-sidor. Därmed från ett visst telemetriobjekt i Application Insights hittar du alla andra telemetri-objekt för den här användaren eller sessionen. 

*Min klient- och serversidan telemetri Visa inte samordnad exempel som beskrivs ovan.*

* Kontrollera att du aktiverat fast räntesats sampling både servern och klienten.
* Se till att SDK-version 2.0 eller senare.
* Kontrollera att du ställer in samma samplingsprocenten i både klienten och servern.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
*Varför inte sampling en enkel ”samla in X procent av varje typ av telemetri”?*

* Den här Samplingsmetod skulle användas med en mycket hög precision i mått med ungefärliga datum men skulle brytas om möjligheten att korrelera diagnostiska data per användare, session och begäran, vilket är viktigt för diagnostik. Därför linjer fungerar bättre med ”samla in alla telemetri artiklar för X procent av app-användare” eller ”samla in all telemetri för X procent av appförfrågningar” logik. För telemetri-objekt som inte är associerade med begäranden (till exempel asynkron behandling i bakgrunden), är höst tillbaka till ”samla in X procent av alla objekt för varje typ av telemetri”. 

*Samplingsprocenten kan ändras med tiden?*

* Ja, ändrar Adaptiv sampling gradvis samplingsprocenten, baserat på den för närvarande observerade telemetrinivå.

*Om jag använder en fast räntesats sampling, hur vet jag vilka sampling procent som fungerar bäst för min app?*

* Ett sätt är att börja med adaptiv sampling, ta reda på vad Betygsätt igen på (Se ovanstående fråga) och sedan växla till en fast räntesats sampling med hjälp av denna kurs. 
  
    Annars kan behöva du gissa. Analysera din nuvarande förbrukning i telemetri i Application Insights, se begränsning som inträffar och beräkna mängden telemetri som samlas in. Dessa tre indata, tillsammans med din valda prisnivå föreslår hur mycket du kanske vill minska mängden telemetri som samlas in. Dock kan en ökning av antalet dina användare eller några andra SKIFT i mängden telemetri ogiltigförklaras beräkningen.

*Vad händer om jag konfigurera samplingsprocenten är för lågt?*

* Låg samplingsprocenten (aggressiva sampling) minskar det arbete du utfört med ungefärliga datum, när Application Insights försöker kompensera för visualisering av data för dataminskning för volymen. Dessutom påverkas diagnostiska upplevelsen negativt, eftersom några sällan misslyckade eller långsamma förfrågningar kan samlas in ut.

*Vad händer om jag konfigurera för hög samplingsprocenten?*

* Konfigurera för hög samplingsprocenten (inte aggressiva tillräckligt) resulterar i en otillräcklig minskning i mängden telemetri som samlas in. Du kan fortfarande uppleva dataförlust för telemetri som rör begränsning och kostnaden för att använda Application Insights kan vara högre än du planerat på grund av överförbrukningskostnader.

*På vilka plattformar kan jag använda sampling?*

* Inmatningssampling kan ske automatiskt för någon telemetri över en viss volym om SDK inte presterar sampling. Detta skulle fungera, till exempel om du använder en äldre version av ASP.NET SDK eller tidigare version av Java SDK(1.0.10 or before).
* Om du använder ASP.NET SDK-versioner 2.0.0 och senare (finns i Azure eller på en egen server), får du Adaptiv sampling som standard, men du kan växla till en fast räntesats enligt beskrivningen ovan. Med fast räntesats sampling synkroniseras automatiskt i webbläsaren SDK ska samlas in relaterade händelser. 
* Om du använder Java SDK version 2.0.1 eller senare kan du konfigurera ApplicationInsights.xml att aktivera Sampling med fast pris. Sampling är inaktiverat som standard. Med fast räntesats sampling synkroniseras automatiskt i webbläsaren SDK ska samlas in relaterade händelser.

*Det finns vissa sällsynta händelser som du alltid vill se. Hur får jag dem tidigare sampling modulen?*

* Initiera en separat instans av TelemetryClient med en ny TelemetryConfiguration (inte standard är aktiva). Använda den för att skicka din sällsynta händelser.

## <a name="next-steps"></a>Nästa steg
* [Filtrering](../azure-monitor/app/api-filtering-sampling.md) kan ge mer strikt kontroll över din SDK skickar.

