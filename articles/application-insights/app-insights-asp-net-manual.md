<properties
    pageTitle="Övervaka din ASP.NET-app genom att lägga till Application Insights SDK | Microsoft Azure"
    description="Analysera användningen, tillgängligheten och prestanda i din lokala program eller Microsoft Azure-webbapp med Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/19/2016"
    ms.author="awills"/>


# Övervaka din ASP.NET-app genom att lägga till Application Insights SDK

*Application Insights finns endast som förhandsversion.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


Med Visual Studio Application Insights kan du övervaka ditt liveprogram, [identifiera och diagnostisera prestandaproblem och undantag][detect] och [se hur din app används][knowUsers]. Tjänsten kan användas med en mängd olika typer av program. Den kan användas med appar som finns på dina lokala IIS-servrar eller på virtuella datorer i Azure, samt för Azure-webbappar.



![Exempel på prestandaövervakningsdiagram](./media/app-insights-asp-net-manual/10-perf.png)

*Se även:*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [Enhetsappar och Java-servrar][platforms]

#### Innan du börjar

Med många programtyper kan [Visual Studio lägga till Application Insights för din app](#ide) nästan utan att du märker det. Men eftersom du läser det här för att få en bättre förståelse för vad som händer tar vi dig igenom stegen manuellt.


Du behöver:

* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com).
* Visual Studio 2013 eller senare.

## <a name="add"></a>Skapa en Application Insights-resurs

Logga in på [Azure-portalen][portal] och skapa en ny Application Insights-resurs. Välj ASP.NET som programtypen.

![Klicka på Nytt, Application Insights](./media/app-insights-asp-net-manual/01-new-asp.png)

En [resurs][roles] i Azure är en instans av en tjänst. Det är i den här resursen som telemetrin från din app analyseras och visas.

Valet av programtyp anger standardinnehållet i resursbladen och egenskaperna som visas i [Metrics Explorer][metrics].

#### Kopiera instrumenteringsnyckeln

Nyckeln identifierar resursen och du ska snart installera den i SDK för att dirigera data till resursen.

![Klicka på Egenskaper, markera nyckeln och tryck på CTRL + C](./media/app-insights-asp-net-manual/02-props-asp.png)

De steg som du precis har utfört för att skapa en ny resurs är ett bra sätt att börja övervaka program. Nu kan du börja skicka data till den.

## <a name="sdk"></a> Installera SDK i ditt program

Hur du installerar och konfigurerar Application Insights SDK varierar beroende på vilken plattform du arbetar på. Det är enkelt för ASP.NET-appar.

1. Redigera NuGet-paketet för ditt webbapprojekt i Visual Studio.

    ![Högerklicka på projektet och välj Hantera Nuget-paket.](./media/app-insights-asp-net-manual/03-nuget.png)

2. Installera Application Insights SDK för Web Apps.

    ![Sök efter ”Application Insights”](./media/app-insights-asp-net-manual/04-ai-nuget.png)

3. Redigera ApplicationInsights.config (som lades till av installationsprogrammet för NuGet). Infoga detta precis före sluttaggen:

    `<InstrumentationKey>` *instrumenteringsnyckeln som du kopierade* `</InstrumentationKey>`

    (Du kan också [ange nyckeln genom att skriva kod][apikey] i din app.)

#### Så här uppgraderar du till framtida SDK-versioner

Då och då ger vi ut en ny version av SDK.

Om du vill uppgradera till en [ny SDK-version](app-insights-release-notes-dotnet.md) öppnar du NuGet-pakethanteraren igen och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.Web** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av filen innan du uppgraderar och sammanfogar sedan dina ändringar i den nya versionen.


## <a name="run"></a> Köra projektet

Använd **F5** när du vill köra programmet och testa det: öppna olika sidor för att generera telemetri.

I Visual Studio ser du antalet händelser som har skickats.

![](./media/app-insights-asp-net-manual/appinsights-09eventcount.png)

## <a name="monitor"></a> Visa telemetrin

Gå tillbaka till [Azure-portalen][portal] och bläddra till Application Insights-resursen.


Leta efter data i översiktsdiagrammet. Först ser du bara en eller två punkter. Till exempel:

![Klicka dig vidare till mer data](./media/app-insights-asp-net-manual/12-first-perf.png)

Klicka dig vidare i diagrammet om du vill visa mer detaljerade mätvärden. [Lär dig mer om mätvärden.][perf]

#### Ser du inga data?

* Använd programmet och öppna olika sidor så att det genererar telemetri.
* Öppna panelen [Sök][diagnostic] för att visa enskilda händelser. Ibland tar det lite längre tid för händelser att passera genom pipelinen för mätvärden.
* Vänta några sekunder och klicka på **Uppdatera**. Diagrammen uppdateras automatiskt med jämna mellanrum, men du kan uppdatera dem manuellt om du väntar på att vissa data ska visas.
* Mer information finns i [Felsökning][qna].

## Publicera appen

Distribuera ditt program till IIS eller Azure och se hur data ackumuleras.

![Använda Visual Studio för att publicera din app](./media/app-insights-asp-net-manual/15-publish.png)

När du kör i felsökningsläge skickas telemetrin genom pipelinen och du bör se data inom några sekunder. När du distribuerar appen i en publiceringskonfiguration ackumuleras data långsammare.

#### Visas inga data efter att du har publicerat till servern?

Öppna dessa portar för utgående trafik i serverns brandvägg:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Har du problem på build-servern?

Mer information finns i [det här felsökningsavsnittet](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Om din app genererar mycket telemetri (och du använder ASP.NET SDK version 2.0.0-beta3 eller senare) minskar den anpassningsbara insamlingsmodulen automatiskt den mängd som skickas till portalen genom att bara skicka en representativ del av händelserna. Händelser som är relaterade till samma begäran markeras eller avmarkeras dock som en grupp, så att du kan navigera mellan relaterade händelser. 
> [Lär dig mer om insamling](app-insights-sampling.md).


## Lägga till beroendespårning (och IIS-prestandaräknare)

SDK behöver lite hjälp för att få åtkomst till vissa data. I synnerhet krävs detta ytterligare steg för att automatiskt mäta anrop från din app till databaser, REST-API:er och andra externa komponenter. Dessa beroendemätvärden kan vara ovärderliga för att hjälpa dig att diagnostisera prestandaproblem.

Om du kör på en egen IIS-server gör det här steget också att systemets prestandaräknare visas i [Metrics Explorer](app-insights-metrics-explorer.md).

#### Om appen körs på din IIS-server

Logga in på servern med administratörsrättigheter och installera [Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

Du kan behöva [öppna ytterligare utgående portar i brandväggen](app-insights-monitor-performance-live-website-now.md#troubleshooting).

Det här steget aktiverar även [rapportering av prestandaräknare](app-insights-web-monitor-performance.md#system-performance-counters), till exempel för processor-, minnes- och nätverksanvändning.

#### Om din app är en Azure-webbapp

Lägg till Application Insights-tillägget på kontrollpanelen för din Azure-webbapp.

![I webbappen går du till Inställningar, Tillägg, Lägg till, Application Insights](./media/app-insights-asp-net-manual/05-extend.png)


#### Om det är ett Azure-molntjänstprojekt

[Lägg till skript till webb- och arbetarroller](app-insights-cloudservices.md).



## Lägga till övervakning på klientsidan

Du har installerat SDK som skickar telemetridata från programmets server (serverdel). Nu kan du lägga till övervakning på klientsidan. På så sätt kan du få information om användare, sessioner, sidvyer och eventuella undantag eller krascher i webbläsaren. Du kan även skriva kod om du vill spåra hur användarna använder din app, ända ner på klick- och tangenttryckningsnivå.


Lägg till ett JavaScript-kodfragment på varje sida. Hämta koden från din Application Insights-resurs:

![Öppna Snabbstart i din webbapp och klicka på Hämta kod för att övervaka webbplatser.](./media/app-insights-asp-net-manual/02-monitor-web-page.png)

Observera att koden innehåller instrumenteringsnyckeln som identifierar din programresurs.

[Läs mer om spårning av webbsidor.](app-insights-web-track-usage.md)


## Spåra programversion

Kontrollera att `buildinfo.config` genereras av MSBuild-processen. I filen .csproj lägger du till:  

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

När Application Insights-webbmodulen har fått versionsinformationen läggs **programversionen** automatiskt till som en egenskap för alla telemetriobjekt. Det gör att du kan filtrera baserat på version när du utför [diagnostiksökningar][diagnostic] eller när du [undersöker mätvärden][metrics]. 

Observera dock att build-versionsnumret endast genereras av MS Build, inte av utvecklarversionen i Visual Studio.

## Slutföra installationen

Det finns ytterligare några saker som du kan göra för att få en heltäckande bild av ditt program:

* [Konfigurera webbtester][availability] för att försäkra dig om att programmet är aktivt och att det svarar.
* [Samla in loggspårningar][netlogs] från önskat loggningsramverk.
* [Spåra anpassade händelser och mätvärden][api] på klienten eller servern eller på båda och lär dig mer om hur ditt program används.

## <a name="ide"></a> Det automatiserade sättet

Som vi sa i början av den här artikeln ska vi visa hur du kan skapa en Application Insights-resurs manuellt och sedan installera SDK. Vi tror att det är bra att förstå båda två delarna av proceduren. Men för ASP.NET-appar (och många andra) finns det en ännu snabbare automatisk metod.

Du behöver [Visual Studio](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013 Update 3 eller senare) och ett konto i [Microsoft Azure](http://azure.com).

#### Om det är ett nytt projekt …

När du skapar ett nytt projekt i Visual Studio kontrollerar du att **Lägg till Application Insights** är markerat.


![Skapa ett ASP.NET-projekt](./media/app-insights-asp-net-manual/appinsights-01-vsnewp1.png)

Visual Studio skapar en resurs i Application Insights, lägger till SDK till projektet och placerar nyckeln i `.config`-filen.

Om projektet har webbsidor läggs även [JavaScript SDK][client] till på huvudwebbsidan.

#### … eller, om det är ett befintligt projekt

Högerklicka på projektet i Solution Explorer och välj **Lägg till Application Insights**.

![Välj Lägg till Application Insights](./media/app-insights-asp-net-manual/appinsights-03-addExisting.png)

Visual Studio skapar en resurs i Application Insights, lägger till SDK till projektet och placerar nyckeln i `.config`-filen.

I detta fall läggs inte [JavaScript SDK][client] till för dina webbsidor – vi rekommenderar att du gör det som ett nästa steg.

#### Installationsalternativ

Om det här är första gången uppmanas du att logga in eller att registrera dig för Microsoft Azure Preview. 

Om appen ingår i ett större program kan du använda **Konfigurera inställningar** och lägga till den i samma resursgrupp som de övriga komponenterna.

*Visas inget alternativ för Application Insights? Kontrollera att du använder Visual Studio 2013 Update 3 eller senare och att Application Insights Tools är aktiverat i Tillägg och uppdateringar.*

#### Öppna Application Insights från ditt projekt

![Högerklicka på projektet och öppna Azure-portalen](./media/app-insights-asp-net-manual/appinsights-04-openPortal.png)




## <a name="video"></a>Video

> [AZURE.VIDEO getting-started-with-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md



<!--HONumber=jun16_HO2-->


