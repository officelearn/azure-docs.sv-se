---
title: "Konfigurera webbappsanalyser för ASP.NET med Application Insights | Microsoft Docs"
description: "Konfigurera prestanda-, tillgänglighets- och användningsanalyser för din lokala eller Azure-baserade ASP.NET-webbplats."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d0eee3c0-b328-448f-8123-f478052751db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/19/2018
ms.author: mbullwin
ms.openlocfilehash: 198cd5164ce3840503aca359767638e135f164bc
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="set-up-application-insights-for-your-aspnet-website"></a>Konfigurera Application Insights för din ASP.NET-webbplats

Den här proceduren konfigurerar din ASP.NET-webbapp för att skicka telemetri till tjänsten [Azure Application Insights](app-insights-overview.md). Den fungerar för ASP.NET-appar som antingen ligger på din egen IIS-server lokalt eller i molnet. Du får tillgång till diagram och ett kraftfullt frågespråket som hjälper dig att förstå hur din app presterar och hur människor använder den, plus automatiska aviseringar om fel eller prestandaproblem. Många utvecklare tycker att de här funktionerna är mycket bra som de är, men du kan också utöka och anpassa telemetrin om du behöver.

Installationen kräver bara några klick i Visual Studio. Du har möjlighet att undvika kostnader genom att begränsa mängden telemetri. På så sätt kan du experimentera och felsöka eller övervaka en plats som har få användare. Om du sedan vill gå vidare och övervaka din produktionsplats, är det lätt att höja gränsen vid ett senare tillfälle.

## <a name="prerequisites"></a>Nödvändiga komponenter
När du ska lägga till Application Insights för din ASP.NET-webbplats måste du göra följande:

- Installera [Visual Studio 2017 för Windows](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
    - ASP.NET och webbutveckling
    - Azure Development

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="ide"></a> Steg 1: Lägg till Application Insights SDK

Högerklicka på webbappens namn i Solution Explorer och välj **Configure Application Insights** (Konfigurera Application Insights)

![Skärmbild av Solution Explorer med Configure Application Insights markerat](./media/app-insights-asp-net/0001-configure-application-insights.png)

(Beroende på Application Insights SDK-version kan du uppmanas att uppgradera till den senaste versionen av SDK:t. Om det händer väljer du **Uppdatera SDK**.)

![Skärmbild: En ny version av Microsoft Application Insights SDK är tillgänglig. Update SDK är markerat](./media/app-insights-asp-net/0002-update-sdk.png)

Konfigurationsskärmen för Application Insights:

Välj **Start Free** (Starta kostnadsfritt).

![Skärmbild av sidan Register your app with Application Insights (Registrera din app med Application Insights)](./media/app-insights-asp-net/0004-start-free.png)

Om du vill ange resursgruppen eller den plats där dina data är lagrade klickar du på **Konfigurera inställningar**. Resursgrupper används för att styra dataåtkomst. Om du exempelvis har flera appar som ingår i samma system kan du placera deras Application Insights-data i samma resursgrupp.

 Välj **Registrera**. 

![Skärmbild av sidan Register your app with Application Insights (Registrera din app med Application Insights)](./media/app-insights-asp-net/0005-register-ed.png)

 Telemetri kommer att skickas till [Azure Portal](https://portal.azure.com), både under felsökningen och när du har publicerat din app.
> [!NOTE]
> Om du inte vill skicka telemetri till portalen medan du felsöker, lägger du bara till Application Insights SDK i din app men konfigurerar inte en resurs i portalen. Du kan se telemetrin i Visual Studio medan du felsöker. Du kan senare återvända till den här konfigurationssidan eller vänta tills du har distribuerat appen och [aktivera telemetri vid körtid](app-insights-monitor-performance-live-website-now.md).

## <a name="run"></a> Steg 2: Kör appen
Kör din app med F5. Öppna olika sidor för att skapa viss telemetri.

Du ser hur många händelser som har loggats i Visual Studio.

![Skärmbild av Visual Studio. Knappen Application Insights visas under felsökning.](./media/app-insights-asp-net/0006-Events.png)

## <a name="step-3-see-your-telemetry"></a>Steg 3: Visa din telemetri
Du kan se din telemetri antingen i Visual Studio eller i Application Insights-webbportalen. Sök telemetri i Visual Studio för att felsöka din app. Övervaka prestanda och användning i webbportalen när systemet är aktivt. 

### <a name="see-your-telemetry-in-visual-studio"></a>Visa din telemetri i Visual Studio

Visa Application Insights-data i Visual Studio.  Välj **Solution Explorer** > **Anslutna tjänster** > högerklicka på **Application Insights** och klicka sedan på **Search Live Telemetry** (Sök livetelemetri).

I Visual Studio Application Insights-sökfönstret visas data från programmet för telemetri som genereras på serversidan för appen. Experimentera med filtren och klicka på en händelse om du vill visa mer information.

![Skärmbild av data från felsökningssessionsvyn i fönstret Application Insights.](./media/app-insights-asp-net/55.png)

> [!Tip]
> Om du inte ser några data kontrollerar du att tidsintervallet är korrekt och klickar på sökikonen.

[Lär dig mer om Application Insights-verktygen i Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### <a name="see-telemetry-in-web-portal"></a>Visa telemetri i webbportalen

Du kan även visa telemetrin i Application Insights-webbportalen (såvida du inte väljer att installera enbart SDK:n). Portalen innehåller fler diagram, analysverktyg och komponentöverskridande vyer än Visual Studio. Portalen visar även aviseringar.

Öppna Application Insights-resursen. Logga antingen in på [Azure-portalen](https://portal.azure.com/) och sök efter den där, eller välj **Solution Explorer** > **Anslutna tjänster** > högerklicka på **Application Insights** > **Open Application Insights Portal** (Öppna Application Insights-portalen) för att komma dit.

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
Om du vill uppgradera till en [ny SDK-version](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases) öppnar du **NuGet-pakethanteraren** och filtrerar på installerade paket. Markera **Microsoft.ApplicationInsights.Web** och välj **Uppgradera**.

Om du har gjort anpassningar i ApplicationInsights.config sparar du en kopia av den innan du uppgraderar. Sammanfogar sedan dina ändringar i den nya versionen.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Nästa steg

Det finns andra artiklar som du kan läsa om du är intresserad av följande:

* [Instrumentering av en webbapp under körning](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

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
