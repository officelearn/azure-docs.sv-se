---
title: "Konfigurera webbappsanalyser för ASP.NET med Application Insights | Microsoft Docs"
description: "Konfigurera prestanda-, tillgänglighets- och användningsanalyser för din lokala eller Azure-baserade ASP.NET-webbplats."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/15/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 1960fa0e17ea9aa0ca15bc3ecb8ce5b1f6630a60
ms.contentlocale: sv-se
ms.lasthandoff: 08/02/2017

---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Konfigurera Application Insights för din ASP.NET-webbplats

Den här proceduren konfigurerar din ASP.NET-webbapp för att skicka telemetri till tjänsten [Azure Application Insights](app-insights-overview.md). Den fungerar för ASP.NET-appar som finns i din IIS-server eller i molnet. Du får tillgång till diagram och ett kraftfullt frågespråket som hjälper dig att förstå hur din app presterar och hur människor använder den, plus automatiska aviseringar om fel eller prestandaproblem. Många utvecklare tycker att de här funktionerna är mycket bra som de är, men du kan också utöka och anpassa telemetrin om du behöver.

Installationen kräver bara några klick i Visual Studio. Du har möjlighet att undvika kostnader genom att begränsa mängden telemetri. På så sätt kan du experimentera och felsöka eller övervaka en plats som har få användare. Om du sedan vill gå vidare och övervaka din produktionsplats, är det lätt att höja gränsen vid ett senare tillfälle.

## <a name="before-you-start"></a>Innan du börjar
Du behöver:

* Visual Studio 2013 Update 3 eller senare. Senare versioner är att föredra.
* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com).

Det finns andra artiklar som du kan läsa om du är intresserad av följande:

* [Instrumentering av en webbapp under körning](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> Steg 1: Lägg till Application Insights SDK

Högerklicka på ditt webbappsprojekt i Solution Explorer och välj **Lägg till** > **Application Insights Telemetry...** eller **Konfigurera Application Insights**.

![Skärmbild av Solution Explorer med Lägg till Application Insights Telemetry markerat](./media/app-insights-asp-net/appinsights-03-addExisting.png)

(I Visual Studio 2015 finns det också ett alternativ för att lägga till Application Insights i dialogrutan Nytt projekt.)

Fortsätt till konfigurationssidan för Application Insights:

![Skärmbild av sidan Register your app with Application Insights (Registrera din app med Application Insights)](./media/app-insights-asp-net/visual-studio-register-dialog.png)

**a.** Välj det konto och din prenumeration som du använder för att få åtkomst till Azure.

**b.** Markera resursen i Azure där du vill visa data från din app. Vanligtvis:

* Använd en [enkel resurs för olika komponenter](app-insights-monitor-multi-role-apps.md) i ett enda program. 
* Skapa separata resurser för orelaterade program.
 
Om du vill ange resursgruppen eller den plats där dina data är lagrade klickar du på **Konfigurera inställningar**. Resursgrupper används för att styra dataåtkomst. Om du exempelvis har flera appar som ingår i samma system kan du placera deras Application Insights-data i samma resursgrupp.

**c.** Ange en begränsning för den fria datavolymen för att undvika avgifter. Application Insights är kostnadsfritt upp till en viss telemetrinivå. När resursen har skapats kan du ändra ditt val i portalen genom att öppna **Funktioner och prissättning**,  > **Datavolymhantering** > **Dagligt volymtak**.

**d.** Klicka på **Registrera** för att fortsätta att konfigurera Application Insights för din webbapp. Telemetri kommer att skickas till [Azure Portal](https://portal.azure.com), både under felsökningen och när du har publicerat din app.

**e.** Om du inte vill skicka telemetri till portalen medan du felsöker, lägger du bara till Application Insights SDK i din app men konfigurerar inte en resurs i portalen. Du kommer att kunna visa telemetri i Visual Studio när du felsöker. Du kan senare återvända till den här konfigurationssidan eller vänta tills du har distribuerat appen och [aktivera telemetri vid körtid](app-insights-monitor-performance-live-website-now.md).


## <a name="run"></a> Steg 2: Kör appen
Kör din app med F5. Öppna olika sidor för att skapa viss telemetri.

Du ser hur många händelser som har loggats i Visual Studio.

![Skärmbild av Visual Studio. Knappen Application Insights visas under felsökning.](./media/app-insights-asp-net/54.png)

## <a name="step-3-see-your-telemetry"></a>Steg 3: Visa din telemetri
Du kan se din telemetri antingen i Visual Studio eller i Application Insights-webbportalen. Sök telemetri i Visual Studio för att felsöka din app. Övervaka prestanda och användning i webbportalen när systemet är aktivt. 

### <a name="see-your-telemetry-in-visual-studio"></a>Visa din telemetri i Visual Studio

Öppna fönstret Application Insights i Visual Studio. Klicka antingen på **Application Insights** eller högerklicka på projektet i Solution Explorer, välj **Application Insights** och klicka sedan på alternativet för att **söka i realtidstelemetri**.

I Visual Studio Application Insights-sökfönstret visas vyn **Data från felsökningssessionen** för telemetri som genereras i din app på serversidan. Experimentera med filtren och klicka på en händelse om du vill visa mer information.

![Skärmbild av data från felsökningssessionsvyn i fönstret Application Insights.](./media/app-insights-asp-net/55.png)

> [!NOTE]
> Om du inte ser några data kontrollerar du att tidsintervallet är korrekt och klickar på sökikonen.

[Lär dig mer om Application Insights-verktygen i Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Visa telemetri i webbportalen

Du kan även visa telemetrin i Application Insights-webbportalen (såvida du inte väljer att installera enbart SDK:n). Portalen innehåller fler diagram, analysverktyg och komponentöverskridande vyer än Visual Studio. Portalen visar även aviseringar.

Öppna Application Insights-resursen. Logga antingen in på [Azure Portal](https://portal.azure.com/) och hitta den där, eller högerklicka på projektet i Visual Studio så kommer du dit.

![Skärmbild av Visual Studio som visar hur du öppnar Application Insights-portalen](./media/app-insights-asp-net/appinsights-04-openPortal.png)

> [!NOTE]
> Om ett åtkomstfel visas kan det bero på att du har fler än en uppsättning Microsoft-autentiseringsuppgifter och att du kanske är inloggad med fel uppsättning. Logga ut och logga in igen i portalen.

Portalen öppnas i en vy över telemetrin från appen.

![Skärmbild av Application Insights-översiktssidan](./media/app-insights-asp-net/66.png)

Klicka på valfri ikon eller valfritt diagram i portalen för att visa mer information.

[Läs mer om hur du använder Application Insights på Azure Portal](app-insights-dashboards.md).

## <a name="step-4-publish-your-app"></a>Steg 4: Publicera appen
Publicera din app på din IIS-server eller i Azure. Bevaka [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) och se om allt fungerar som det ska.

Du ser din telemetri byggas upp i Application Insights-portalen, där du kan övervaka mått, söka i telemetrin och konfigurera [instrumentpaneler](app-insights-dashboards.md). Du kan också använda det kraftfulla [Log Analytics-frågespråket](https://docs.loganalytics.io/) för att analysera användning och prestanda eller söka efter specifika händelser.

Du kan också fortsätta att analysera din telemetri i [Visual Studio](app-insights-visual-studio.md) med verktyg som diagnossökning och [trender](app-insights-visual-studio-trends.md).

> [!NOTE]
> Om din app skickar så mycket telemetri att det närmar sig [begränsningsgränserna](app-insights-pricing.md#limits-summary) aktiveras [sampling](app-insights-sampling.md) automatiskt. Sampling minskar mängden telemetri som skickas från din app, samtidigt som korrelerade informationen bevaras i diagnossyfte.
>
>

## <a name="land"></a> Då var allt klart

Grattis! Du har installerat Application Insights-paketet i din app och konfigurerat det för att skicka telemetri till Application Insights-tjänsten på Azure.

![Diagram över flödet av telemetri](./media/app-insights-asp-net/01-scheme.png)

Azure-resursen som tar emot din apps telemetri identifieras med en *instrumenteringsnyckel*. Du hittar den här nyckeln i filen ApplicationInsights.config.config.


## <a name="upgrade-to-future-sdk-versions"></a>Uppgradera till framtida SDK-versioner
Om du vill uppgradera till en [ny SDK-version](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases) öppnar du **NuGet-pakethanteraren** igen och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.Web** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av den innan du uppgraderar. Sammanfogar sedan dina ändringar i den nya versionen.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg

### <a name="more-telemetry"></a>Mer telemetri

* **[Webbläsare och webbsidesinläsning](app-insights-javascript.md)**  – Infoga ett kodfragment på dina webbsidor.
* **[Få mer detaljerad beroende- och undantagsövervakning](app-insights-monitor-performance-live-website-now.md)**  – Installera Status Monitor på servern.
* **[Koda anpassade händelser](app-insights-api-custom-events-metrics.md)** för att räkna, ta tid på eller mäta användaråtgärder.
* **[Hämta loggdata](app-insights-asp-net-trace-logs.md)**  – Korrelera loggdata med din telemetri.

### <a name="analysis"></a>Analys

* **[Arbeta med Application Insights i Visual Studio](app-insights-visual-studio.md)**<br/>Innehåller information om att felsöka med telemetri, köra diagnostiksökningar och gå igenom koden.
* **[Arbeta med Application Insights-portalen](app-insights-dashboards.md)**<br/> Innehåller information om instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, realtidsmappning av beroenden för din app och telemetriexport.
* **[Analytics](app-insights-analytics-tour.md)** – Kraftfullt frågespråk.

### <a name="alerts"></a>Aviseringar

* [Tillgänglighetstester](app-insights-monitor-web-app-availability.md): Skapa tester som kan användas för att kontrollera att webbplatsen visas på webben.
* [Smart diagnostik](app-insights-proactive-diagnostics.md): De här testerna körs automatiskt, så du behöver inte göra något för att konfigurera dem. De berättar om din app har ett ovanligt antal misslyckade begäranden.
* [Måttaviseringar](app-insights-alerts.md): Används för att varna dig om ett mått överskrider ett tröskelvärde. Du kan ställa in dem för anpassade mätningar som du kodar i din app.

### <a name="automation"></a>Automation

* [Automatisera skapandet av en Application Insights-resurs](app-insights-powershell.md)

