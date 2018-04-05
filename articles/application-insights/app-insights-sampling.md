---
title: Telemetri provtagning i Azure Application Insights | Microsoft Docs
description: Så här håller telemetrivolym under kontroll.
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin
ms.openlocfilehash: d0614e2eae0f60068e69b7a4687fc62fbe082c64
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="sampling-in-application-insights"></a>Sampling i Application Insights


Sampling är en funktion i [Azure Application Insights](app-insights-overview.md). Det är det rekommenderade sättet att minska trafiken telemetri och lagring, samtidigt som en statistiskt korrekt analys av programdata. Filtret markerar objekt som är relaterade, så att du kan bläddra mellan objekten när du gör diagnostiska undersökningar.
När mått Antal presenteras i portalen, är de renormalized hänsyn till samplingsfrekvensen för att minimera någon effekt på statistik.

Samplingsfrekvensen minskar kostnaderna för trafik och data och hjälper dig att undvika begränsning.

## <a name="in-brief"></a>Kort sagt:
* Sampling behåller 1 i *n* registrerar och ignorerar resten. Det kan till exempel lagra 1: 5 händelser, en samplingsfrekvensen 20%. 
* Sampling sker automatiskt om programmet skickar mycket telemetri i ASP.NET web server apps.
* Du kan också ange provtagning manuellt, antingen i portalen på användningen och beräknade kostnader för sidan. eller i SDK för ASP.NET i .config-fil. eller i Java SDK i filen ApplicationInsights.xml också minska nätverkstrafiken.
* Om du loggar anpassade händelser och du vill kontrollera att en uppsättning händelser är antingen bevaras eller tas bort tillsammans, se till att de har samma åtgärds-ID-värde.
* Sampling divisorn *n* rapporteras i varje post i egenskapen `itemCount`, som i sökningen visas under namnet ”begäran antal” eller ”händelseantal”. När provtagning inte är i drift, `itemCount==1`.
* Om du skriver Analytics-frågor, bör du [ta hänsyn till provtagning](app-insights-analytics-tour.md#counting-sampled-data). I synnerhet i stället för bara räknar poster, du bör använda `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Typer av provtagning
Det finns tre alternativ provtagningsmetoder:

* **Anpassningsbar provtagning** justeras automatiskt telemetrivolym som skickas från SDK i din ASP.NET-app. Från och med SDK v 2.0.0-beta3 är detta standard urvalsmetoden. Anpassningsbar provtagning är för närvarande endast tillgängligt för ASP.NET serversidan telemetri. 
* **Fast räntesats provtagning** minskar mängden telemetri som skickas från båda ASP.NET eller Java-servern och från användarnas webbläsare. Anges. Klienten och servern ska synkronisera sina provtagning så att, i sökningen som du kan navigera mellan relaterade sidvisningar och förfrågningar.
* **Införandet provtagning** fungerar i Azure-portalen. Den tar bort vissa av telemetri som tas emot från din app på en samplingsfrekvensen som du anger. Det minskar inte telemetri trafik som skickas från din app, men hjälper dig att hålla inom den månatliga kvoten. Den största fördelen med införandet provtagning är att du kan ange samplingsfrekvensen utan att omdistribuera din app och det fungerar enhetligt för alla servrar och klienter. 

Om Adaptiv eller fast hastighet provtagning är i drift, har införandet provtagning inaktiverats.

## <a name="ingestion-sampling"></a>Införandet provtagning
Det här formuläret för provtagning fungerar på den plats där telemetri från webbservern, webbläsare och enheter når tjänstslutpunkten Application Insights. Även om det inte minska telemetri-trafik som skickas från din app, minskar mängden bearbetas och lagras (och debiteras för) av Application Insights.

Använd den här typen av samplingsfrekvensen om din app ofta färdas över månatliga kvoten och du inte har möjlighet att använda någon av SDK-baserade typer för provtagning. 

Ange samplingsfrekvensen i användnings- och uppskattade kostnaderna sidan:

![I bladet program Översikt Klicka på inställningar, kvot, prover, välj sedan en samplingsfrekvensen, och klicka på Uppdatera.](./media/app-insights-sampling/04.png)

Precis som andra typer av provtagning behåller algoritmen relaterad telemetri objekt. Till exempel när du undersöks telemetri i sökning, ska du kunna hitta begäran som rör en viss undantag. Måttet räknar, till exempel förfrågningar och undantag hastighet behålls på rätt sätt.

Datapunkter som ignoreras av provtagning är inte tillgängliga i alla Application Insights-funktioner som [löpande Export](app-insights-export-telemetry.md).

Införandet provtagning fungerar inte när SDK-baserade anpassningsbara eller fasta hastighet provtagning är i drift. Observera att anpassningsbar provtagning är aktiverad som standard när ASP.NET SDK är aktiverad i Visual Studio eller med hjälp av Status Monitor och införandet provtagning är inaktiverad. Om samplingsfrekvensen på SDK är mindre än 100%, ignoreras införandet samplingsfrekvensen som du anger.

> [!WARNING]
> Värdet som visas på panelen anger det värde som du anger för införandet provtagning. Den representera inte faktiska samplingsfrekvensen om SDK provtagning är i drift.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>Anpassningsbar provtagning på din webbserver
Anpassningsbar provtagning är tillgänglig för Application Insights SDK för ASP.NET v 2.0.0-beta3 och senare och är aktiverad som standard. 

Anpassningsbar provtagning påverkar telemetrivolym som skickas från ditt webbprogram för server till Application Insights-tjänsteslutpunkt. Volymen justeras automatiskt om du vill behålla inom en angiven maximala mängden trafik.

Det fungerar inte på låg mängder telemetri, så en app i felsökning eller en webbplats med låg belastning påverkas inte.

För att uppnå målvolymen några av de genererade telemetrin ignoreras. Men precis som andra typer av provtagning algoritmen behåller relaterad telemetri objekt. Till exempel när du undersöks telemetri i sökning, ska du kunna hitta begäran som rör en viss undantag. 

Måttet räknas som förfrågningar och undantag hastighet justeras för att kompensera för samplingsfrekvensen, så att de visar ungefär rätt värden i måttet Explorer.

### <a name="update-nuget-packages"></a>Uppdatera NuGet-paket ###

Uppdatera ditt projekt NuGet-paket till senast *förhandsversionen* version av Application Insights. Högerklicka på projektet i Solution Explorer i Visual Studio, välja hantera NuGet-paket, kontrollera **inkludera förhandsversion** och Sök efter Microsoft.ApplicationInsights.Web. 

### <a name="configuring-adaptive-sampling"></a>Konfigurera anpassningsbar provtagning ###

I [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), kan du justera flera parametrar i den `AdaptiveSamplingTelemetryProcessor` nod. De siffror som visas är standardvärden:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Mål-hastigheten som algoritmen anpassningsbar syftar för **på varje server-värd**. Om ditt webbprogram kör på flera värdar, minska värdet så att den finns kvar i din mål mängden trafik på Application Insights-portalen.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Tidsintervall som är aktuellt antal telemetri ny utvärdering. Utvärderingen utförs som en glidande medelvärde. Du kanske vill förkorta intervallet om din telemetri kan tänkas plötslig belastning.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    När provtagning procentandel ändras, hur snart när vi får lägre samplingsprocenten igen för att samla in mindre data.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    När provtagning procentandel ändras, hur strax efter vi tillåts att öka samplingsprocenten igen för att samla in mer data.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Provtagning procentandel varierar, är vad det lägsta värdet som det är tillåtet att ställa in.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Provtagning procentandel varierar, är vad det högsta värdet som det är tillåtet att ställa in.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    Vikten tilldelas det senaste värdet i beräkningen av glidande medelvärde. Använd ett värde som är lika med eller mindre än 1. Lägre värden gör algoritmen reagera mindre plötsliga ändringar.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Värdet som tilldelas när appen har startats. Inte minska detta när du felsöker. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    En semikolonavgränsad lista över typer som du inte vill att sampla. Identifiera typer är: beroende, händelse, undantag, PageView, begäran, spårning. Alla instanser av de angivna typerna överförs. de typer som inte har angetts samplas.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    En semikolonavgränsad lista över typer som du vill ska samlas in. Identifiera typer är: beroende, händelse, undantag, PageView, begäran, spårning. De angivna typerna samplas; alla instanser av de andra typerna skickas alltid.


**Stänga av** anpassningsbar provtagning, ta bort noden AdaptiveSamplingTelemetryProcessor från applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternativ: Konfigurera anpassningsbar provtagning i koden
I stället för att ange parametern provtagning i .config-filen kan ange du programmässigt dessa värden. På så sätt kan du ange ett återanropsfunktion som anropas när samplingsfrekvensen är ny utvärdering. Du kan använda detta, till exempel att ta reda på vilka samplingsfrekvensen används.

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

([Lär dig mer om telemetri processorer](app-insights-api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Samplingsfrekvensen för webbsidor med JavaScript
Du kan konfigurera webbsidor för fast räntesats provtagning från alla servrar. 

När du [konfigurera webbsidorna för Application Insights](app-insights-javascript.md), ändra JavaScript-kodfragment som du får från Application Insights-portalen. (I ASP.NET appar sammandraget som vanligtvis är med i _Layout.cshtml.)  Infoga en rad som `samplingPercentage: 10,` innan instrumentation nyckeln:

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

Samplingsprocenten, välja i procent som ligger nära 100/N, där N är ett heltal.  Samlar för närvarande stöd inte för andra värden.

Om du även aktivera fast räntesats provtagning på servern synkroniserar klienter och server så att, i sökningen som du kan navigera mellan relaterade sidvisningar och förfrågningar.

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>Fast räntesats samplingsfrekvensen för ASP.NET och Java-webbplatser
Fasta samplingsfrekvensen minskar den trafik som skickas från webbservern och webbläsare. Till skillnad från anpassningsbar samplingsfrekvensen minskar den telemetri till en fast kostnad som du valt. Det synkroniserar även klienten och servern provtagning så att relaterade objekt bevaras – exempelvis när du tittar på vyn sida i sökningen du kan hitta relaterade begäran.

Algoritmen provtagning behåller relaterade objekt. För varje HTTP-begäran är händelse, begäran och dess relaterade händelser antingen ignoreras eller överförs tillsammans. 

I Metrics Explorer multipliceras priser som begäran och undantag räknas med en faktor att kompensera för samplingsfrekvensen, så att de är ungefär korrekt.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Konfigurera fast räntesats provtagning i ASP.NET ###

1. **Uppdatera ditt projekt NuGet-paket** till senast *förhandsversionen* version av Application Insights. Högerklicka på projektet i Solution Explorer i Visual Studio, välja hantera NuGet-paket, kontrollera **inkludera förhandsversion** och Sök efter Microsoft.ApplicationInsights.Web. 
2. **Inaktivera anpassningsbar provtagning**: I [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), ta bort eller kommentera ut den `AdaptiveSamplingTelemetryProcessor` nod.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **Aktivera fast räntesats provtagning-modulen.** Lägg till den här fragment till [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Konfigurera fast räntesats provtagning i JAVA ###

1. Hämta och konfigurera ditt webbprogram med senaste [application insights java SDK](app-insights-java-get-started.md)

2. **Aktivera fast räntesats provtagning modulen** genom att lägga till följande kodavsnitt ApplicationInsights.xml fil.

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuilrInProcessors>
    <TelemetryProcessors/>
```

3. Du kan inkludera eller exkludera specifika typer av telemetri från med hjälp av följande taggar i Processor-taggen ”FixedRateSamplingTelemetryProcessor”
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
Telemetri-typer som kan inkluderas eller uteslutas från provtagning: beroende, händelse, undantag, PageView, begäran och spårning.

> [!NOTE]
> Samplingsprocenten, välja i procent som ligger nära 100/N, där N är ett heltal.  Samlar för närvarande stöd inte för andra värden.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternativ: aktivera fast räntesats provtagning i serverkoden
I stället för att ange parametern provtagning i .config-filen kan ange du programmässigt dessa värden. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Lär dig mer om telemetri processorer](app-insights-api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>När du ska använda provtagning?
Anpassningsbar provtagning aktiveras automatiskt om du använder SDK för ASP.NET version 2.0.0-beta3 eller senare. Du kan aktivera införandet provtagning att Application Insights som exempel insamlade data oavsett vilken version av du använder SDK.

Som standard aktiveras ingen provtagning i Java SDK. Den stöder för närvarande, endast fast hastighet provtagning. Anpassningsbar provtagning stöds inte i Java SDK.

I allmänhet för de flesta små och medelstora storlek program behöver du inte provtagning. Den mest användbara diagnostisk information och mest korrekta statistik erhålls genom att samla in data på alla användaraktiviteter. 

De huvudsakliga fördelarna med samplingsfrekvensen är:

* Application Insights service således (”begränsas”) datapunkter när appen skickar en mycket hög andel telemetri kort sagt tidsintervall. 
* Att hålla inom den [kvot](app-insights-pricing.md) datapunkter för din prisnivå. 
* För att minska nätverkstrafiken för insamling av telemetri. 

### <a name="which-type-of-sampling-should-i-use"></a>Vilken typ av provtagning ska jag använda?
**Använd införandet provtagning om:**

* Ofta gå igenom den månatliga kvoten av telemetri.
* Du använder en version av SDK: N som inte stöder provtagning – till exempel ASP.NET-versioner tidigare än 2.
* Du får mycket telemetri från användarnas webbläsare.

**Använd fast räntesats provtagning om:**

* Du använder Application Insights SDK för ASP.NET web services version 2.0.0 eller senare eller Java SDK v2.0.1 eller senare, och
* Du vill synkroniserade provtagning mellan klienten och servern, så att när du vill veta händelser i [Sök](app-insights-diagnostic-search.md), du kan navigera mellan relaterade händelser på klienten och servern, till exempel sidvisningar och HTTP-begäranden.
* Du är säker på av lämplig samplingsprocenten för din app. Det bör vara tillräckligt högt för att få exakta mått, men under hastigheten som överskrider din prisnivå kvot och bandbreddsbegränsning gränser. 

**Använda anpassningsbar provtagning:**

Om villkoren för att använda andra former av provtagning inte tillämpas, rekommenderar vi anpassningsbar provtagning. Detta är aktiverat som standard i ASP.NET server SDK version 2.0.0-beta3 eller senare. Det minskar inte trafik tills en fast räntesats har nåtts, därför låg Använd platser påverkas inte.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Hur vet jag om sampling är i drift?
För att identifiera den faktiska samplingsfrekvensen oavsett om den har tillämpats, använda en [Analytics query](app-insights-analytics.md) sådana här:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Bevaras i varje post, `itemCount` anger antal ursprungliga poster som representerar, lika med 1 + antalet tidigare borttagna poster. 

## <a name="how-does-sampling-work"></a>Hur fungerar provtagning?
Fast räntesats provtagning funktion i SDK i ASP.NET-versioner från 2.0.0 och Java SDK version 2.0.1 och och senare. Anpassningsbar provtagning är en funktion av SDK i ASP.NET-versioner från 2.0.0 och senare. Införandet provtagning är en funktion i Application Insights-tjänsten och kan vara i drift om SDK inte utför provtagning. 

Algoritmen provtagning bestämmer vilka telemetri objekt att släppa och vilka som ska sparas (om den är i SDK eller Application Insights-tjänsten). Sampling beslutet baseras på flera regler som syftar till att bevara alla relaterade datapunkter intakta, underhålla Avbrottsfritt diagnostik i Application Insights som är tillämplig och tillförlitlig även med en minskad datauppsättning. Till exempel om din app skickar ytterligare telemetri objekt (till exempel undantag och spårningar som loggats från denna begäran) för en misslyckad begäran, provtagning inte delar upp denna begäran och andra telemetri. Den fortsätter eller utelämnar dem samtidigt. När du tittar på begäran-information i Application Insights kan du därför alltid se begäran tillsammans med dess associerade telemetri-objekt. 

För program som definierar ”användare” (det vill säga de vanligaste webbprogram), provtagning beslut baserat på hash för användar-id, vilket innebär att all telemetri för en viss användare är antingen bevaras eller tas bort. För typerna av program som inte definierar användare (till exempel webbtjänster) baserat provtagning beslutet på åtgärds-id för begäran. Slutligen för telemetri objekten som varken har användaren eller åtgärden-id som angetts (till exempel telemetri objekt som rapporterats från asynkron trådar med ingen kontext för http) provtagning bara samlar in en del av telemetri varje typ av objekt. 

När presenterar telemetri för dig, justeras Application Insights-tjänsten mätvärdena av samma samplingsprocenten som användes vid tidpunkten för insamling, för att kompensera för saknade datapunkter. När du tittar på telemetri i Application Insights, därför kan ser användarna statistiskt korrekt approximeringar som ligger mycket nära reellt tal.

Korrektheten i uppskattning beror till stor del på konfigurerade samplingsprocenten. Dessutom ökar precisionen för program som hanterar stora mängder vanligtvis liknande begäranden från många olika användare. Å andra sidan för program som inte fungerar med en stor belastning behövs provtagning inte dessa program kan skicka all telemetri vanligtvis när du befinner dig inom kvoten, utan att orsaka förlust av data från begränsning. 

> [!WARNING]
> Application Insights inte sample mått och sessioner telemetri typer. Minskad precisionen kan vara hög främmande för dessa typer av telemetri.
> 

### <a name="adaptive-sampling"></a>Anpassningsbar provtagning
Anpassningsbar provtagning lägger till en komponent som övervakar den aktuella överföringshastigheten från SDK och justerar samplingsprocenten att hålla sig inom den högsta hastigheten för målet. Justeringen beräknas med jämna mellanrum och baseras på ett glidande medelvärde för utgående överföringshastigheten.

## <a name="sampling-and-the-javascript-sdk"></a>Sampling och JavaScript SDK
Klientsidan (JavaScript) SDK deltar i fast räntesats provtagning tillsammans med SDK för serversidan. Sidorna instrumenterade bara skicka telemetri för klientsidan från samma användare som gjorts på serversidan beslutet ”exempel i”. Den här logiken är utformad för att upprätthålla integriteten hos användarsession över - och server-klienten. Därför från ett visst telemetri objekt i Application Insights hittar du alla andra telemetri objekt för den här användaren eller sessionen. 

*Klient- och serversidan telemetri Visa inte samordnas exempel som beskrivs ovan.*

* Kontrollera att du har aktiverat fast räntesats provtagning både servern och klienten.
* Kontrollera att SDK-version 2.0 eller senare.
* Kontrollera att du anger samma samplingsprocenten i både klienten och servern.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
*Varför inte provtagning en enkel ”samla in X procent av varje typ av telemetri”?*

* När den här metoden för provtagning skulle ge en mycket hög exakta mått approximeringar, skulle brytas möjlighet att korrelera diagnostikdata per användare, session och begäran som är viktiga för diagnostik. Därför provtagning fungerar bättre med ”samla in alla telemetri artiklar för X procent av app-användare” eller ”samla in all telemetri för X procent av begäranden som app” logik. För de telemetri objekt som inte är associerade med begäranden (till exempel asynkron behandling i bakgrunden) faller tillbaka är en ”samla in X procent av alla objekt för varje typ av telemetri”. 

*Samplingsprocenten kan förändras över tid?*

* Ja, ändras anpassningsbar provtagning gradvis samplingsprocenten baserat på den för närvarande observerade telemetrivolym.

*Om jag använder fast räntesats provtagning hur vet jag vilka provtagning procentandel fungerar bäst för min app?*

* Ett sätt att börja med anpassningsbar provtagning, ta reda på vad ge ett omdöme reglerar på (se ovan frågan) och sedan växla till en fast räntesats prov med denna kurs. 
  
    Annars kan ha att gissa. Analysera ditt nuvarande bruk som telemetri i Application Insights, se begränsning som inträffar och beräkna mängden insamlade telemetri. Dessa tre indata, tillsammans med din valda prisnivå förslag på hur mycket kanske du vill minska mängden insamlade telemetri. Dock kan en ökning av antalet användare eller några andra SKIFT i mängden telemetri ogiltig uppskattningen.

*Vad händer om jag konfigurera samplingsprocenten för lågt?*

* Låg samplingsprocenten (over-aggressive provtagning) minskar riktighet approximeringar, när Application Insights försöker kompensera visualiseringen av data för att minska för volymen av data. Dessutom kan diagnostiska upplevelse påverkas negativt, eftersom vissa av de sällan feltillstånd eller långsam begärandena prov ut.

*Vad händer om jag konfigurera samplingsprocenten för hög?*

* Konfigurera för hög samplingsprocenten (inte aggressivt tillräckligt) resulterar i en otillräcklig minskning av insamlade telemetrivolym. Du kan fortfarande dataförluster telemetri rör begränsning och kostnaden för att använda Application Insights kan vara högre än du planerat överförbrukning avgifter.

*På vilka plattformar kan du använda provtagning?*

* Införandet sampling kan ske automatiskt för alla telemetri över en viss volym om SDK inte utför provtagning. Detta fungerar, till exempel om du använder en äldre version av ASP.NET SDK eller tidigare version av Java-SDK(1.0.10 or before).
* Om du använder SDK för ASP.NET-versioner 2.0.0 och senare (finns i Azure eller på din server), får du anpassningsbar provtagning som standard, men du kan växla till en fast räntesats enligt beskrivningen ovan. Med fast räntesats provtagning synkroniseras automatiskt i webbläsaren SDK som exempel relaterade händelser. 
* Om du använder Java SDK version 2.0.1 eller senare kan du konfigurera ApplicationInsights.xml aktivera fast hastighet provtagning. Sampling är inaktiverat som standard. Med fast räntesats provtagning synkroniseras automatiskt i webbläsaren SDK som exempel relaterade händelser.

*Det finns vissa ovanliga händelser som du alltid vill se. Hur kan jag få dem tidigare provtagning modulen?*

* Initiera en separat instans av TelemetryClient med en ny TelemetryConfiguration (inte standard aktiva). Använda den för att skicka din ovanliga händelser.

## <a name="next-steps"></a>Nästa steg
* [Filtrering](app-insights-api-filtering-sampling.md) kan ge mer strikt kontroll över din SDK skickar.

