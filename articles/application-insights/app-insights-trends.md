<properties
    pageTitle="Analysera trender i Visual Studio | Microsoft Azure"
    description="Analysera, visualisera och utforska trender i Application Insights Telemetry i Visual Studio."
    services="application-insights"
    documentationCenter=".net"
    authors="numberbycolors"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/14/2016"
    ms.author="daviste"/>

# Analysera trender i Visual Studio

Verktyget Application Insights Trends hjälper dig att visualisera hur programmets viktiga telemetrihändelser ändras med tiden, så att du snabbt kan identifiera problem och inkonsekvenser. Med länkar till mer detaljerad diagnostiskinformation kan Trends hjälpa dig att förbättra dina apprestanda, spåra orsakerna till undantag och få insikter från anpassade händelser.

![Exempel på fönster i Trends](./media/app-insights-trends/app-insights-trends-hero-750.png)

> [AZURE.NOTE] Application Insights Trends är tillgängligt i Visual Studio 2015 Update 3 och senare, eller med [tillägget Developer Analytics Tools](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) version 5.209 och senare.

## Öppna Application Insights Trends

Du kan öppna fönstret Application Insights Trends på något av följande sätt:

* Från verktygsfältsknappen Application Insights väljer du **Explore Telemetry Trends** (Utforska telemetritrender).
* Från snabbmenyn för projektet väljer du **Application Insights > Explore Telemetry Trends** (Utforska telemetritrender).
* Från menyraden i Visual Studio väljer du **Visa > Andra fönster > Application Insights Trends**.

Du kan uppmanas att välja en resurs. I så fall klickar du på **Välj en resurs**, loggar in med en Azure-prenumeration och väljer sedan en Application Insights-resurs som du vill analysera telemetritrender för.

## Välj en trendanalys

![Meny över vanliga typer av trendanalyser](./media/app-insights-trends/app-insights-trends-1-750.png)

Sätt igång genom att välja någon av fem vanliga trendanalyser, som var och en analyserar data från de senaste 24 timmarna:

* **Undersök prestandaproblem med dina serverbegäranden**: Begäranden som görs till din tjänst, grupperade efter svarstider.
* **Analysera fel i dina serverbegäranden**: Begäranden som görs till din tjänst, grupperade efter HTTP-svarskod.
* **Undersök undantagen i ditt program**: Undantag från din tjänst, grupperade efter undantagstyp.
* **Kontrollera prestanda för ditt programs beroenden**: Tjänster som anropas av din tjänst, grupperade efter svarstider.
* **Granska dina anpassade händelser**: Anpassade händelser som du har konfigurerat för din tjänst, grupperade efter händelsetyp.

Dessa fördefinierade analyser är tillgängliga senare från knappen **View common types of telemetry analysis** (Visa vanliga typer av telemetrianalyser) i det övre vänstra hörnet i Trends-fönstret.

## Visualisera trender i ditt program

Application Insights Trends skapar en tidsserievisualisering från din apps telemetri. Varje tidsserievisualisering visar en typ av telemetri, grupperad efter en egenskap för den telemetrin, över en viss tidsperiod.

Du kanske till exempel vill visa serverbegäranden, grupperade efter det land som de kommer från, under de senaste 24 timmarna. I det här exemplet representerar varje bubbla i visualiseringen ett antal serverbegäranden för ett specifikt land under en timme.

Använd kontrollerna längst upp i fönstret för att välja vilka telemetrityper som visas. Börja med att välja de telemetrityper som du är intresserad av:

* **Typ av telemetri**: Serverbegäranden, undantag, beroenden eller anpassade händelser.
* **Tidsintervall**: Allt från de senaste 30 minuterna till de senaste 3 dagarna.
* **Gruppera efter**: Undantagstyp, problem-ID, land/region och mer.

Klicka på **Analyze Telemetry** (Analysera telemetri) för att köra frågan.

Så här navigerar du mellan bubblor i visualiseringen:

* Klicka för att välja en bubbla. När du gör det uppdateras filtren längst ned i fönstret och en sammanfattning över de händelser som inträffat under en viss tidsperiod visas.
* Dubbelklicka på en bubbla om du vill gå till sökverktyget och visa alla enskilda telemetrihändelser som inträffat under tidsperioden.
* Tryck på **Ctrl**-tangenten och klicka sedan på en bubbla om du vill avmarkera den i visualiseringen.

> [AZURE.TIP] Med Trends och sökverktyget kan du identifiera de telemetrihändelser som orsakar problem med tjänsten. Om dina kunder till exempel märker att din app är mindre känslig under en eftermiddag kan du analysera problemet med Trends. Analysera begäranden som har gjorts till din tjänst under de senaste timmarna, grupperade efter svarstid. Se efter om det finns ett ovanligt stort antal långsamma begäranden. Dubbelklicka sedan på den bubblan för att gå till sökverktyget, som filtrerar fram händelserna för dessa begäranden. Från sökverktyget kan du utforska innehållet i dessa begäranden och hitta den relaterade koden så att du kan lösa problemet.

## Filter för specifika trender

Identifiera mer specifika trender med filterkontrollerna längst ned i fönstret. Om du vill använda ett filter klickar du på filtrets namn. Du kan snabbt växla mellan olika filter för att identifiera trender som kan vara dolda i en viss dimension av telemetrin. Om du använder ett filter i en dimension, till exempel dimensionen Undantagstyp, går det fortfarande att klicka på filter i andra dimensioner även om de ser ut som de är nedtonade. Om du vill ta bort ett filter klickar du på det igen. Tryck på **Ctrl**-tangenten och klicka om du vill välja flera filter i samma dimension.

![Filter för trender](./media/app-insights-trends/TrendsFiltering-750.png)

Hur gör du om du vill använda flera filter?

1. Tillämpa det första filtret.
2. Klicka på knappen **Apply selected filters and query again** (Använd valda filter och fråga igen) vid namnet på dimensionen för ditt första filter. När du gör det skickas en ny fråga mot telemetrin för att begära händelser som matchar det första filtret.
3. Tillämpa ett andra filter.
4. Upprepa samma steg för att hitta trender i specifika deluppsättningar av telemetrin. Du kan till exempel fråga efter serverbegäranden med namnet ”GET Home/Index” som kommit från Tyskland och som tagit emot statuskoden 500.

Om du vill ta bort filtren klickar du på knappen **Remove selected filters and query again** (Ta bort valda filter och fråga igen) för dimensionen.

![Flera filter](./media/app-insights-trends/TrendsFiltering2-750.png)

## Identifiera avvikelser

Verktyget Trends kan märka ut bubblor med händelser som avviker från andra bubblor i samma tidsserie. Välj **Counts in time bucket (highlight anomalies)** (Antal i tidsenhet (markera avvikelser)) eller **Percentages in time bucket (highlight anomalies)** (Procent i tidsenhet (markera avvikelser)) i listrutan **Vytyp**. Röda bubblor representerar avvikelser.

Avvikelser definierar som bubblor med ett antal/procentvärde som överstiger 2,1 gånger standardavvikelsen för antalet/procentvärdet under de föregående två tidsperioderna (till exempel 48 timmar om du visar de senaste 24 timmarna).

![Färgade punkter representerar avvikelser](./media/app-insights-trends/TrendsAnomalies-750.png)

> [AZURE.TIP] Genom att avvikelserna markeras blir det lättare att hitta avvikande mätvärden i tidsserier med små bubblor som annars kan se ungefär lika stora ut.  

## <a name="next"></a>Nästa steg


[Arbeta med Application Insights i Visual Studio](app-insights-visual-studio.md): Sök i telemetri, visa data i CodeLens och konfigurera Application Insights – allt i Visual Studio.

[Lägg till mer data](app-insights-asp-net-more.md): Övervaka användningen, tillgängligheten, beroenden och undantag. Integrera spårningar från loggningsramverk. Skriv anpassad telemetri.

[Arbeta med Application Insights-portalen](app-insights-dashboards.md): Instrumentpaneler, kraftfulla diagnostik- och analysverktyg, aviseringar, en live-karta över programmets beroenden och telemetriexport.



<!--HONumber=sep16_HO1-->


