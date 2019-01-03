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
ms.openlocfilehash: 48432b4a415c0c544c4a64444c89cf818fa00e1f
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53972436"
---
# <a name="usage-analysis-with-application-insights"></a>Användningsanalys med Application Insights

Vilka funktioner för webb- eller mobilappen som är mest populära? Användarna att uppnå sina mål med din app? De försvinner vid specifika tidpunkter och de komma tillbaka senare?  [Azure Application Insights](app-insights-overview.md) hjälper dig att få kraftfulla insikter om hur personer använder din app. Varje gång du uppdaterar din app kan du utvärdera hur väl den fungerar för användare. Med denna kunskap kan göra du datadrivna beslut om din nästa utvecklingscykler.

## <a name="send-telemetry-from-your-app"></a>Skicka telemetri från din app

Den bästa upplevelsen hämtas genom att installera Application Insights både i serverkoden app och i dina webbsidor. Klient- och komponenterna i din app skickar telemetri till Azure-portalen för analys.

1. **Kod:** Installera modulen lämpliga för din [ASP.NET](../azure-monitor/app/asp-net.md), [Azure](app-insights-overview.md), [Java](../azure-monitor/app/java-get-started.md), [Node.js](app-insights-nodejs.md), eller [andra](app-insights-platforms.md) App.

    * *Vill du installera serverkoden inte? Bara [skapa en Azure Application Insights-resurs](app-insights-create-new-resource.md).*

2. **Webbsidans kod:** Lägga till följande skript på din webbsida före avslutande ``</head>``. Ersätt instrumenteringsnyckeln med lämpligt värde för Application Insights-resursen:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    Om du vill lära dig mer om avancerade konfigurationer för att övervaka webbplatser kan du läsa [JavaScript SDK API-referensen](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

3. **Kod för mobilapp:** Använda App Center SDK för att samla in händelser från din app och sedan skicka kopior av dessa händelser till Application Insights för analys av [följer den här guiden](app-insights-mobile-center-quickstart.md).

4. **Hämta telemetri:** Köra projektet i felsökningsläge i några minuter och sedan leta efter resultat i bladet översikt i Application Insights.

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

Läs mer om [anpassade händelser](../azure-monitor/app/api-custom-events-metrics.md#trackevent) och [egenskaper](../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Rapportanvändarna händelser

Du kan ta ett utsnitt och segmentera anpassade händelser efter användarnamn, event och egenskaper i Verktyg för användare, sessioner och händelser.
![Användare](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Utforma telemetri med appen

När du utformar varje funktion i din app, kan du överväga hur du kommer att mäta sin framgång med dina användare. Bestäm vilka affärshändelser som du behöver registrera och kod spårnings-anrop för dessa händelser i din app från början.

## <a name="a--b-testing"></a>A | B-testning
Om du inte vet vilken variant av en funktion ska bli mer framgångsrika, släpp lagringsintensiva gör varje är tillgängliga för olika användare. Mät framgången för varje och sedan flytta till en enhetlig version.

För den här tekniken koppla distinkta egenskapsvärden till all telemetri som skickas av varje version av din app. Du kan göra det genom att definiera egenskaperna i den aktiva TelemetryContext. Dessa standardegenskaperna läggs till i alla telemetrimeddelanden som programmet skickar – inte bara din anpassade meddelanden, men även standard telemetri.

Filtrera och dela dina data på egenskapsvärden för att jämföra olika versioner i Application Insights-portalen.

Gör [ställa in en telemetri-initierare](../azure-monitor/app/api-filtering-sampling.md##add-properties-itelemetryinitializer):

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
