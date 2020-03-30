---
title: Användningsanalys med Azure Application Insights | Microsoft-dokument
description: Förstå dina användare och vad de gör med din app.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e964b1b5b9d5500f2d9f24ed765299389e6dbbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283964"
---
# <a name="usage-analysis-with-application-insights"></a>Användningsanalys med Application Insights

Vilka funktioner i din webb- eller mobilapp är mest populära? Uppnår användarna sina mål med din app? Hoppar de av vid vissa tillfällen, och återvänder de senare?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) hjälper dig att få kraftfulla insikter om hur användarna använder din app. Varje gång du uppdaterar appen kan du bedöma hur bra den fungerar för användarna. Med denna kunskap kan du fatta datadrivna beslut om dina nästa utvecklingscykler.

## <a name="send-telemetry-from-your-app"></a>Skicka telemetri från appen

Den bästa upplevelsen erhålls genom att installera Application Insights både i appserverkoden och på dina webbsidor. Klient- och serverkomponenterna i din app skickar telemetri tillbaka till Azure-portalen för analys.

1. **Serverkod:** Installera lämplig modul för [din ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md)eller [annan](../../azure-monitor/app/platforms.md) app.

    * *Vill du inte installera serverkod? Skapa bara [en Azure Application Insights-resurs](../../azure-monitor/app/create-new-resource.md ).*

2. **Webbsidas kod:** Lägg till följande skript på webbsidan innan stängningen ``</head>``. Ersätt instrumenteringsnyckeln med rätt värde för application insights-resursen:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Mer avancerade konfigurationer för övervakning av webbplatser finns i [referensartikeln för JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

3. **Kod för mobilapp:** Använd App Center SDK för att samla in händelser från din app och skicka sedan kopior av dessa händelser till Application Insights för analys genom [att följa den här guiden](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Hämta telemetri:** Kör projektet i felsökningsläge i några minuter och leta sedan efter resultat i bladet Översikt i Application Insights.

    Publicera appen för att övervaka appens prestanda och ta reda på vad användarna gör med din app.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Inkludera användar- och sessions-ID i din telemetri
För att spåra användare över tid kräver Application Insights ett sätt att identifiera dem. Händelseverktyget är det enda användningsverktyget som inte kräver ett användar-ID eller ett sessions-ID.

Börja skicka användar- och sessions-ID:er med [den här processen](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Utforska användningsdemografi och statistik
Ta reda på när användarna använder din app, vilka sidor de är mest intresserade av, var användarna finns, vilka webbläsare och operativsystem de använder. 

Rapporterna Användare och sessioner filtrerar dina data efter sidor eller anpassade händelser och segmenterar dem efter egenskaper som plats, miljö och sida. Du kan också lägga till egna filter.

![Användare](./media/usage-overview/users.png)  

Insikter till höger pekar på intressanta mönster i uppsättningen data.  

* I rapporten **Användare** räknas antalet unika användare som kommer åt dina sidor inom de valda tidsperioderna. För webbappar räknas användarna med hjälp av cookies. Om någon kommer åt din webbplats med olika webbläsare eller klientdatorer, eller rensar sina cookies, kommer de att räknas mer än en gång.
* I rapporten **Sessioner** räknas antalet användarsessioner som kommer åt din webbplats. En session är en period av aktivitet av en användare, avslutas med en period av inaktivitet på mer än en halvtimme.

[Mer om verktygen Användare, Sessioner och Händelser](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Retention - hur många användare kommer tillbaka?

Kvarhållning hjälper dig att förstå hur ofta användarna återvänder för att använda sin app, baserat på kohorter av användare som utförde vissa affärsåtgärder under en viss tids bucket. 

- Förstå vilka specifika funktioner som gör att användarna kommer tillbaka mer än andra 
- Formulärhypoteser baserade på verkliga användardata 
- Ta reda på om kvarhållning är ett problem i produkten 

![Kvarhållning](./media/usage-overview/retention.png) 

Med kvarhållningskontrollerna överst kan du definiera specifika händelser och tidsintervall för att beräkna kvarhållning. Diagrammet i mitten ger en visuell representation av den totala kvarhållningsprocenten med det angivna tidsintervallet. Diagrammet längst ned representerar individuell kvarhållning under en viss tidsperiod. Med den här detaljnivån kan du förstå vad användarna gör och vad som kan påverka återkommande användare på en mer detaljerad granularitet.  

[Mer om kvarhållningsverktyget](usage-retention.md)

## <a name="custom-business-events"></a>Anpassade affärshändelser

För att få en klar förståelse för vad användarna gör med din app är det användbart att infoga kodrader för att logga anpassade händelser. Dessa händelser kan spåra allt från detaljerade användaråtgärder som att klicka på specifika knappar, till mer betydande affärshändelser som att göra ett köp eller vinna ett spel. 

Även om sidvisningar i vissa fall kan representera användbara händelser är det inte sant i allmänhet. En användare kan öppna en produktsida utan att köpa produkten. 

Med specifika affärshändelser kan du kartlägga användarnas framsteg via din webbplats. Du kan ta reda på deras preferenser för olika alternativ, och där de hoppar av eller har svårigheter. Med denna kunskap kan du fatta välgrundade beslut om prioriteringarna i din utvecklingseftersläpning.

Händelser kan loggas från appens klientsida:

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

Du kan koppla egenskapsvärden till dessa händelser så att du kan filtrera eller dela upp händelserna när du inspekterar dem i portalen. Dessutom är en standarduppsättning egenskaper kopplad till varje händelse, till exempel anonymt användar-ID, som gör att du kan spåra sekvensen av aktiviteter för en enskild användare.

Läs mer om [anpassade händelser](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) och [egenskaper](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Skiva och tärningar händelser

I verktygen Användare, Sessioner och Händelser kan du segmentera och tärna anpassade händelser efter användare, händelsenamn och egenskaper.
![Användare](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Designa telemetrin med appen

När du utformar varje funktion i appen bör du fundera över hur du ska mäta dess framgång med användarna. Bestäm vilka affärshändelser du behöver registrera och koda spårningssamtalen för dessa händelser i din app från början.

## <a name="a--b-testing"></a>A | B Testning
Om du inte vet vilken variant av en funktion som kommer att bli mer framgångsrik, släpp dem båda, vilket gör var och en tillgänglig för olika användare. Mät framgången för varje och flytta sedan till en enhetlig version.

För den här tekniken kopplar du distinkta egenskapsvärden till all telemetri som skickas av varje version av appen. Du kan göra det genom att definiera egenskaper i den aktiva TelemetryContext. Dessa standardegenskaper läggs till i varje telemetrimeddelande som programmet skickar - inte bara dina anpassade meddelanden, men även standardtelemetrin.

I application insights-portalen filtrerar och delar du upp data på egenskapsvärdena för att jämföra de olika versionerna.

Det gör du genom [att ställa in en telemetriinitierare:](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

**ASP.NET-appar**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

I webbappinitieraren, till exempel Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core-appar**

> [!NOTE]
> Att lägga `ApplicationInsights.config` till `TelemetryConfiguration.Active` initializer med eller använda är inte giltigt för ASP.NET Core-program. 

För [ASP.NET Core-program](asp-net-core.md#adding-telemetryinitializers) görs `TelemetryInitializer` det genom att lägga till en ny genom att lägga till den i behållaren för beroendeinsprutning, som visas nedan. Detta görs `ConfigureServices` med metoden `Startup.cs` för din klass.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Alla nya TelemetryClients lägger automatiskt till det egenskapsvärde som du anger. Enskilda telemetrihändelser kan åsidosätta standardvärdena.

## <a name="next-steps"></a>Nästa steg
   - [Användare, sessioner, händelser](usage-segmentation.md)
   - [Trattar](usage-funnels.md)
   - [Kvarhållning](usage-retention.md)
   - [Användarflöden](usage-flows.md)
   - [Arbetsböcker](../../azure-monitor/app/usage-workbooks.md)
   - [Lägga till användarkontext](usage-send-user-context.md)
