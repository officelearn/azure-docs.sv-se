---
title: Användningsanalys med Azure Application Insights | Microsoft docs
description: Förstå dina användare och vad de gör med din app.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: bc80bfacf6896161fae03b9ed93f5fe22e13ada2
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725292"
---
# <a name="usage-analysis-with-application-insights"></a>Användningsanalys med Application Insights

Vilka funktioner för webb- eller mobilappen som är mest populära? Användarna att uppnå sina mål med din app? De försvinner vid specifika tidpunkter och de komma tillbaka senare?  [Azure Application Insights](app-insights-overview.md) hjälper dig att få kraftfulla insikter om hur personer använder din app. Varje gång du uppdaterar din app kan du utvärdera hur väl den fungerar för användare. Med denna kunskap kan göra du datadrivna beslut om din nästa utvecklingscykler.

## <a name="send-telemetry-from-your-app"></a>Skicka telemetri från din app

Den bästa upplevelsen hämtas genom att installera Application Insights både i serverkoden app och i dina webbsidor. Klient- och komponenterna i din app skickar telemetri till Azure-portalen för analys.

1. **Serverkoden:** installera modulen lämpliga för din [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-overview.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), eller [andra](app-insights-platforms.md) app.

    * *Vill du installera serverkoden inte? Bara [skapa en Azure Application Insights-resurs](app-insights-create-new-resource.md).*

2. **Webbsidans kod:** öppna den [Azure-portalen](https://portal.azure.com), öppna Application Insights-resurs för din app och öppna sedan **komma igång > övervaka och diagnostisera klientsidan**. 

    ![Kopiera skriptet till chefen för din huvudwebbsidan.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Mobil Appkod:** använda App Center SDK för att samla in händelser från din app och sedan skicka kopior av dessa händelser till Application Insights för analys av [följer den här guiden](app-insights-mobile-center-quickstart.md).

4. **Hämta telemetri:** köra projektet i felsökningsläge i några minuter och sedan leta efter resultat i bladet översikt i Application Insights.

    Publicera din app för att övervaka appens prestanda och ta reda på vad användarna gör med din app.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Ange ID för användaren och session i din telemetri
Om du vill spåra användare över tid, kräver ett sätt att identifiera dem i Application Insights. Händelseverktyget är det enda verktyg för användning som inte kräver ett användar-ID eller ett sessions-ID.

Börja skicka användar- och sessions-ID med hjälp av [den här processen](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Utforska demografi användning och statistik
Ta reda på när din app används, vilka sidor som de är mest intresserad av, där användarna befinner sig, vilka webbläsare och operativsystem som de använder. 

Rapporter för användare och sessioner filtrera dina data genom sidor eller anpassade händelser och segmentera dem efter egenskaper, till exempel plats, miljö och sidan. Du kan också lägga till egna filter.

![Användare](./media/app-insights-usage-overview/users.png)  

Insikter till höger påpeka intressanta mönster i datauppsättningen.  

* Den **användare** rapporten räknar antalet unika användare som har åtkomst till sidorna i din valda tidsperioder. För web apps räknas användare genom att använda cookies. Om någon får åtkomst till din webbplats med olika webbläsare eller klientdatorer eller tar bort sina cookies, och sedan de kommer att räknas mer än en gång.
* Den **sessioner** rapporten räknar antalet användarsessioner som har åtkomst till din webbplats. En session är en viss aktivitet av en användare som avslutades av en period av inaktivitet av mer än en halvtimme.

[Mer om verktyg för användare, sessioner och händelser](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Sidvisning

Klicka vidare sidvisningar panelen för att få en detaljerad analys av dina populäraste sidorna från bladet användning:

![Från bladet översikt klickar du på sidan vyer diagrammet](./media/app-insights-usage-overview/05-games.png)

I exemplet ovan är från en webbplats för spel. Diagram, kan vi direkt se:

* Användning inte har förbättrats under den senaste veckan. Kanske är vi dags att fundera över sökmotoroptimering?
* Tennis är den mest populära spel sidan. Nu ska vi fokusera på att ytterligare förbättringar i den här sidan.
* I genomsnitt användare gå till sidan Tennis cirka tre gånger per vecka. (Det finns cirka tre gånger så mycket sessioner än användare.)
* De flesta användare finns på webbplatsen under USA fungerande vecka och i arbetstid. Kanske ska vi tillhandahåller en ”snabb Dölj”-knapp på webbsidan.
* Den [anteckningar](app-insights-annotations.md) på diagrammet visar när nya versioner av webbplatsen har distribuerats. Ingen av de senaste distributionerna hade en märkbar effekt på användning.

Vad händer om du vill undersöka trafiken till webbplatsen i detalj, som att dela upp av en anpassad egenskap webbplatsen skickar i sin sidvystelemetri?

1. Öppna den **händelser** verktyg i Application Insights-resurs-menyn. Det här verktyget kan du analysera hur många sidvisningar och anpassade händelser har skickats från din app, baserat på en mängd olika alternativ för filtrering, cohorting och segmentering.
2. Välj ”valfri Sidvisning” i listrutan ”som använt”.
3. Välj en egenskap som du vill dela din telemetri för sidvisningar i listrutan ”dela med”.

## <a name="retention---how-many-users-come-back"></a>Kvarhållning – hur många användare kommer tillbaka?

Kvarhållning hjälper dig att förstå hur ofta användarna Återvänder för att använda sin app, baserat på kohorter med användare som utförde vissa företag åtgärd under en viss tid bucket. 

- Förstå vilka specifika funktioner att användare ska komma tillbaka mer än andra 
- Formuläret hypoteser baserat på verkliga användardata 
- Avgöra om kvarhållning av säkerhetskopior är ett problem i din produkt 

![Kvarhållning](./media/app-insights-usage-overview/retention.png) 

Kvarhållning-kontrollerna längst upp kan du definiera specifika händelser och tidsintervall för att beräkna kvarhållning. Diagrammet i mitten ger en visuell representation av den övergripande kvarhållning i procent av det angivna tidsintervallet. Diagrammet längst ned representerar enskilda kvarhållning i en viss tidsperiod. Den här detaljnivån tillåter dig att förstå vad användarna gör och vad kan påverka returnerande användare på en mer detaljerad granularitet.  

[Mer om verktyget kvarhållning](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Anpassade affärshändelser

För att få en förståelse för vad användarna gör med din app, är det praktiskt att infoga rader med kod för att logga in anpassade händelser. Dessa händelser kan spåra allt från detaljerad användaråtgärder som att klicka på specifika knappar för att mer betydande affärshändelser, till exempel inköp eller vunnit ett spel. 

Men i vissa fall kan sidvisningar representera användbara händelser, det är inte sant i allmänhet. En användare kan öppna en produktsidan utan att köpa produkten. 

Med specifika händelser, kan du skapa diagram användarnas förloppet via webbplatsen. Du kan ta reda på deras preferenser för olika alternativ, och om de försvinner ut eller har problem. Med den här kunskapen kan du fatta välgrundade beslut om prioriteringarna i utveckling uppgiftslistan.

Händelser kan loggas från klientsidan av appen:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Eller från serversidan:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Du kan koppla egenskapsvärden till dessa händelser så att du kan filtrera eller dela upp händelser när du inspektera dem i portalen. Dessutom kan är en standarduppsättning egenskaper kopplat till varje händelse, till exempel anonyma användar-ID, vilket gör att du kan spåra sekvensen av aktiviteter för en enskild användare.

Läs mer om [anpassade händelser](app-insights-api-custom-events-metrics.md#trackevent) och [egenskaper](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Rapportanvändarna händelser

Du kan ta ett utsnitt och segmentera anpassade händelser efter användarnamn, event och egenskaper i Verktyg för användare, sessioner och händelser.
![Användare](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Utforma telemetri med appen

När du utformar varje funktion i din app, kan du överväga hur du kommer att mäta sin framgång med dina användare. Bestäm vilka affärshändelser som du behöver registrera och kod spårnings-anrop för dessa händelser i din app från början.

## <a name="a--b-testing"></a>A | B-testning
Om du inte vet vilken variant av en funktion ska bli mer framgångsrika, släpp lagringsintensiva gör varje är tillgängliga för olika användare. Mät framgången för varje och sedan flytta till en enhetlig version.

För den här tekniken koppla distinkta egenskapsvärden till all telemetri som skickas av varje version av din app. Du kan göra det genom att definiera egenskaperna i den aktiva TelemetryContext. Dessa standardegenskaperna läggs till i alla telemetrimeddelanden som programmet skickar – inte bara din anpassade meddelanden, men även standard telemetri.

Filtrera och dela dina data på egenskapsvärden för att jämföra olika versioner i Application Insights-portalen.

Gör [ställa in en telemetri-initierare](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

```csharp


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

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Alla nya TelemetryClients Lägg automatiskt till egenskapsvärdet som du anger. Enskilda telemetrihändelser kan åsidosätta standardvärdena.

## <a name="next-steps"></a>Nästa steg
   - [Användare, sessioner, händelser](app-insights-usage-segmentation.md)
   - [Trattar](usage-funnels.md)
   - [Kvarhållning](app-insights-usage-retention.md)
   - [Användarflöden](app-insights-usage-flows.md)
   - [Arbetsböcker](app-insights-usage-workbooks.md)
   - [Lägg till användarkontext](app-insights-usage-send-user-context.md)
