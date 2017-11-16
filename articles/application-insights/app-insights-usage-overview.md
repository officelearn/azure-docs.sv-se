---
title: "Användningsanalys med Azure Application Insights | Microsoft docs"
description: "Förstå dina användare och hur de fungerar med din app."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 4a6647e30657a6d2d076cd254069d96f99a0aa60
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="usage-analysis-with-application-insights"></a>Användningsanalys med Application Insights

Vilka funktioner för webb- eller mobilapp som är mest populär? Dina användare nå sina mål med din app? De släppa vid särskilda tidpunkter och de kom tillbaka senare?  [Azure Application Insights](app-insights-overview.md) hjälper dig att få kraftfulla insikter om hur personer använder din app. Varje gång du uppdaterar din app kan bedöma du hur bra fungerar för användare. Med denna kunskap kan du datadrivna beslut om din nästa utvecklingscykler.

## <a name="send-telemetry-from-your-app"></a>Skicka telemetri från din app

Bästa möjliga upplevelse hämtas genom att installera Application Insights både i appen serverkoden och på webbsidor. Klient- och serverkomponenter för din app skicka telemetri tillbaka till Azure-portalen för analys.

1. **Serverkod:** installera modulen lämpliga för din [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), eller [andra](app-insights-platforms.md) app.

    * *Inte vill installera serverkod? Bara [skapa en Azure Application Insights-resurs](app-insights-create-new-resource.md).*

2. **Koden webbsidan:** öppna den [Azure-portalen](https://portal.azure.com), öppna Application Insights-resurs för din app och öppna sedan **komma igång > övervaka och diagnostisera klientsidan**. 

    ![Kopiera skriptet till chefen för webbsidan master.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Koden för mobilappen:** använda appen Center SDK för att samla in händelser från din app och skicka kopior av dessa händelser till Application Insights för analys av [efter den här guiden](app-insights-mobile-center-quickstart.md).

4. **Hämta telemetri:** köra projektet i felsökningsläge i några minuter och Sök efter resulterar i bladet översikt i Application Insights.

    Publicera en app för att övervaka prestanda för din app och ta reda på vad användarna gör med din app.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Användar- och session-ID inkluderas i din telemetri
Application Insights kräver ett sätt att identifiera dem för att spåra användare över tid. Verktyget händelser är det enda verktyg för användning som inte kräver ett användar-ID eller ett sessions-ID.

Börja skicka användar- och sessions-ID med hjälp av [processen](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Utforska användning demografi och statistik
Ta reda om personer använder din app, vilka sidor som de är mest intresserad av, där användarna finns, vilka webbläsare och operativsystem som de använder. 

Rapporter för användare och sessioner filtrera dina data efter sidor eller anpassade händelser och segmentera dem efter egenskaper som plats, miljö och sida. Du kan också lägga till egna filter.

![Användare](./media/app-insights-usage-overview/users.png)  

Insikter till höger peka ut intressanta mönster i datauppsättningen.  

* Den **användare** rapporten räknar antalet unika användare som har åtkomst till dina sidor i din valda tidsperioder. Användare ska räknas genom att använda cookies för web apps. Om någon har åtkomst till din plats med olika webbläsare eller klientdatorer eller rensar sina cookies, och sedan ska räknas mer än en gång.
* Den **sessioner** rapporten antalet användarsessioner som har åtkomst till webbplatsen. En session är en period av aktivitet av en användare eller avslutas med en period av inaktivitet av mer än en halvtimme.

[Mer information om verktyg för användare, sessioner och händelser](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Sidvisningar

Klicka på via panelen sidvisningar få en detaljerad analys av de mest populära sidorna i bladet användning:

![Översikt över-bladet klickar du på sidan vyer diagrammet](./media/app-insights-usage-overview/05-games.png)

Exemplet ovan är från en webbplats för spel. Diagram kan se vi omedelbart:

* Användning har förbättrats i den senaste veckan. Kanske fundera över sökmotoroptimering?
* Tennis är den mest populära spel sidan. Nu ska vi fokusera på ytterligare förbättringar i den här sidan.
* I genomsnitt gå användare Tennis cirka tre gånger per vecka. (Det finns ungefär tre gånger så mycket sessioner än användare.)
* De flesta användare besöker du webbplatsen under Arbetsvecka USA och i arbetstid. Vi bör kanske ge knappen ”snabb Dölj” på webbsidan.
* Den [anteckningar](app-insights-annotations.md) visa diagrammet när nya versioner av webbplatsen har distribuerats. Ingen av de senaste distributionerna hade en märkbar effekt på användning.

Vad händer om du vill undersöka trafiken till webbplatsen i detalj som delas av en anpassad egenskap som ska skickas på webbplatsen i dess sidan Visa telemetri?

1. Öppna den **händelser** verktyg i menyn Application Insights-resurs. Det här verktyget kan du analysera hur många sidvisningar och anpassade händelser skickades från din app, baserat på en mängd olika alternativ för filtrering, cohorting och segmentering.
2. I listrutan ”som används för” Välj ”alla sidvisningen”.
3. Välj en egenskap som du vill dela dina sidan Visa telemetri i listrutan ”delning av”.

## <a name="retention---how-many-users-come-back"></a>Innehållen – hur många användare försöka igen?

Kvarhållning hjälper dig att förstå hur ofta användarna återgå till att använda appen, baserat på kohorter av användare som utförde vissa företag åtgärden under en viss tidsperiod. 

- Förstå vilka specifika funktioner orsaka användare att komma tillbaka mer än andra 
- Formuläret hypoteser baserat på verkliga användardata 
- Avgöra om kvarhållning är ett problem i produkten 

![Kvarhållning](./media/app-insights-usage-overview/retention.png) 

Kvarhållning kontroller överst kan du definiera specifika händelser och tidsintervallet för att beräkna kvarhållning. Diagrammet i mitten ger en bild av övergripande kvarhållning procentandelen av det angivna tidsintervallet. Längst ned i diagrammet representerar enskilda kvarhållning i en viss tidsperiod. Den här detaljnivån tillåter dig att förstå vad användarna gör och vad kan påverka returnerar användare på en mer detaljerad granularitet.  

[Mer information om verktyget kvarhållning](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Anpassade affärshändelser

Det är praktiskt att infoga rader med kod anpassade händelser för att få en förståelse för vad användarna göra med din app. Dessa händelser kan spåra allt från detaljerad användaråtgärder, till exempel att klicka på specifika knapparna till större affärshändelser, till exempel inköp eller vinna spel. 

Men i vissa fall sidvisningar representerar användbar händelser, det är inte sant i allmänhet. En användare kan öppna en produktsidan utan inköp av produkten. 

Med specifika händelser, kan du skapa diagram användarnas pågår via webbplatsen. Du kan ta reda på inställningar för olika alternativ, och där de släppa ut eller har problem. Med denna kunskap kan fatta du välgrundade beslut om prioriteringarna i eftersläpning för utveckling.

Händelser kan loggas från klientsidan av appen:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Eller från serversidan:

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Du kan koppla egenskapsvärden till dessa händelser så att du kan filtrera eller dela upp händelser när du inspektera dem i portalen. Dessutom kan är en standarduppsättning egenskaper kopplad till varje händelse, till exempel anonyma användar-ID, vilket gör att du kan spåra sekvensen av aktiviteter för en enskild användare.

Lär dig mer om [anpassade händelser](app-insights-api-custom-events-metrics.md#trackevent) och [egenskaper](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Rapportanvändarna händelser

I Verktyg för användare, sessioner och händelser du segmentera och undersök anpassade händelser efter användarnamn, händelse och egenskaper.
![Användare](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Design telemetri med appen

När du utformar varje funktion i din app kan du överväga hur du kommer att mäta dess framgång med dina användare. Bestäm vilka affärshändelser som du behöver registrera och koden spårnings-anrop för dessa händelser i din app från början.

## <a name="a--b-testing"></a>EN | B-testning
Om du inte vet vilken variant av en funktion blir bättre, släpp båda, vilket gör varje tillgängliga för olika användare. Mäter framgången för varje och gå sedan till en enhetlig version.

För den här tekniken kopplar du distinkta värden till all telemetri som skickas av varje version av appen. Du kan göra det genom att definiera egenskaperna i den aktiva TelemetryContext. Dessa standardegenskaperna läggs till varje telemetri meddelande som programmet skickar – inte bara din egna meddelanden, men även standard telemetri.

Filtrera i Application Insights-portalen och dela dina data på egenskapsvärden för att jämföra olika versioner.

Gör [ställa in en telemetri initieraren](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

I web app initieraren, till exempel Global.asax.cs:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Alla nya TelemetryClients lägger automatiskt till det angivna egenskapsvärdet. Enskilda telemetriska händelser kan åsidosätta standardvärden.

## <a name="next-steps"></a>Nästa steg
   - [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
   - [Trattar](usage-funnels.md)
   - [Kvarhållning](app-insights-usage-retention.md)
   - [Användarflöden](app-insights-usage-flows.md)
   - [Arbetsböcker](app-insights-usage-workbooks.md)
   - [Lägg till användarkontext](app-insights-usage-send-user-context.md)
