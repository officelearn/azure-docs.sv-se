---
title: "Hur gör jag... i Azure Application Insights | Microsoft Docs"
description: "Vanliga frågor och svar i Application Insights."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 48b2b644-92e4-44c3-bc14-068f1bbedd22
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: bwren
ms.openlocfilehash: ef63e06c0621753e0a706d6efb709b943e38ee42
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="how-do-i--in-application-insights"></a>Hur kan jag ... i Application Insights?
## <a name="get-an-email-when-"></a>Få ett e-postmeddelande när...
### <a name="email-if-my-site-goes-down"></a>E-post om min plats kraschar
Ange en [tillgänglighet webbtest](app-insights-monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-post om min plats är överbelastad.
Ange en [avisering](app-insights-alerts.md) på **serversvarstid**. Ett tröskelvärde mellan 1 och 2 sekunder ska fungera.

![](./media/app-insights-how-do-i/030-server.png)

Din app kan också visa loggar belastning genom att returnera felkoder. Ange en varning på **misslyckade begäranden**.

Om du vill ange en varning på **undantag**, du kan behöva göra [vissa ytterligare inställningar](app-insights-asp-net-exceptions.md) för att se data.

### <a name="email-on-exceptions"></a>E-post på undantag
1. [Konfigurera undantagsövervakning](app-insights-asp-net-exceptions.md)
2. [Ställ in en varning](app-insights-alerts.md) undantaget räkna mått

### <a name="email-on-an-event-in-my-app"></a>E-post på en händelse i min app
Anta att du vill få ett e-postmeddelande när en viss händelse inträffar. Application Insights inte ger den här funktionen direkt, men den kan [skicka en avisering när en måttet överskrider ett tröskelvärde](app-insights-alerts.md).

Aviseringar kan ställas in på [anpassade mått](app-insights-api-custom-events-metrics.md#trackmetric), men inte anpassade händelser. Skriva kod för att öka ett mått när händelsen inträffar:

    telemetry.TrackMetric("Alarm", 10);

Eller:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Du måste skicka ett lågt värde när du funderar på att aviseringen ska ha avslutat eftersom aviseringar har två tillstånd:

    telemetry.TrackMetric("Alarm", 0.5);

Skapa ett diagram i [mått explorer](app-insights-metrics-explorer.md) att se din larm:

![](./media/app-insights-how-do-i/010-alarm.png)

Ange en avisering eller om måttet går över ett mid värde under en kort period:

![](./media/app-insights-how-do-i/020-threshold.png)

Ange ett genomsnitt perioden som minst.

Du får e-postmeddelanden när måttet går över såväl under tröskeln.

Vissa saker att tänka på:

* En avisering har två lägen (”varning” och ”felfri”). Tillståndet utvärderas bara när ett mått tas emot.
* Ett e-postmeddelande skickas endast när tillståndet ändras. Detta är varför du behöver skicka både hög och låg-värde mått.
* Om du vill utvärdera aviseringen tas medelvärdet av mottagna värden över föregående period. Detta sker varje gång ett mått tas emot, så e-postmeddelanden skickas oftare än den angivna perioden.
* Eftersom e-postmeddelanden skickas både ”varning” och ”felfri”, kanske du vill överväga nytt tänker din one-shot händelse som ett villkor för två tillstånd. Till exempel i stället för en ”jobbet har slutförts” händelsen har en ”jobb pågår” villkor, där du får e-post i början och slutet av ett jobb.

### <a name="set-up-alerts-automatically"></a>Ställa in aviseringar automatiskt
[Använd PowerShell för att skapa nya aviseringar](app-insights-alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Använd PowerShell för att hantera Application Insights
* [Skapa nya resurser](app-insights-powershell-script-create-resource.md)
* [Skapa nya aviseringar](app-insights-alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Separata telemetri från olika versioner

* Flera roller i en app: använda en enda Application Insights-resurs och filtrera på cloud_Rolename. [Läs mer](app-insights-monitor-multi-role-apps.md)
* Avgränsa utveckling och test-versioner: olika Application Insights-resurser. Hämta instrumentation nycklarna från web.config. [Läs mer](app-insights-separate-resources.md)
* Rapportering skapa versioner: lägga till en egenskap med ett telemetri initieraren. [Läs mer](app-insights-separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Övervaka backend-servrar och -program
[Modulen för Windows Server SDK](app-insights-windows-desktop.md).

## <a name="visualize-data"></a>Visualisera data
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Instrumentpanel med mått för flera appar
* I [mått Explorer](app-insights-metrics-explorer.md), anpassa ditt diagram och spara den som en favorit. Fäst det på Azure instrumentpanelen.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Instrumentpanel med data från andra källor och Application Insights
* [Exportera telemetri till Power BI](app-insights-export-power-bi.md).

Eller

* Använda SharePoint som instrumentpanelen visar data i SharePoint-webbdelar. [Använd den löpande exporten och Stream Analytics för att exportera till SQL](app-insights-code-sample-export-sql-stream-analytics.md).  Använda PowerView för att undersöka databasen och skapa en SharePoint-webbdel för PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrera ut anonyma och autentiserade användare
Om dina användare loggar in, kan du ange den [autentiserat användar-id](app-insights-api-custom-events-metrics.md#authenticated-users). (Det inte sker automatiskt.)

Sedan kan du:

* Söka på särskilda användar-ID

![](./media/app-insights-how-do-i/110-search.png)

* Filtrera mått till anonyma och autentiserade användare

![](./media/app-insights-how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Ändra egenskapsnamn eller värden
Skapa en [filter](app-insights-api-filtering-sampling.md#filtering). På så sätt kan du ändra eller filtrera telemetri innan den skickas från din app till Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista över specifika användare och deras användning
Om du bara vill [Sök efter specifika användare](#search-specific-users), du kan ange den [autentiserat användar-id](app-insights-api-custom-events-metrics.md#authenticated-users).

Om du vill ha en lista över användare med data, till exempel vilka sidor du de titta på eller hur ofta de loggar in, har du två alternativ:

* [Ange autentiserat användar-id](app-insights-api-custom-events-metrics.md#authenticated-users), [exportera till en databas](app-insights-code-sample-export-sql-stream-analytics.md) och använda lämpliga verktyg för att analysera dina användardata.
* Om du har mindre antal användare kan skicka anpassade händelser eller statistik, och använda data av intresse som måttnamnet värde eller händelse och ange användar-id som en egenskap. Ersätt standard JavaScript trackPageView anropet för att analysera sidvisningar. Använd en telemetri initieraren att lägga till användar-id all telemetri för server för att analysera serversidan telemetri. Därefter kan du filtrera och segment mått och sökningar på det användar-id.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Minska trafiken från min app till Application Insights
* I [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), inaktivera alla moduler som du inte behöver dessa prestandaräknaren insamlaren.
* Använd [provtagning och filtrering](app-insights-api-filtering-sampling.md) på SDK.
* Begränsa antalet Ajax-anrop som rapporterats för alla sidor i dina webbsidor. Skriptet kodutdrag efter `instrumentationKey:...` , infoga: `,maxAjaxCallsPerView:3` (eller ett lämpligt antal).
* Om du använder [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric), compute aggregering av batchar av måttvärden innan du skickar resultatet. Det finns en överlagring av TrackMetric() som tillhandahåller för som.

Lär dig mer om [priser och kvoter](app-insights-pricing.md).

## <a name="disable-telemetry"></a>Inaktivera telemetri
Att **dynamiskt stoppa och starta** insamling och vidarebefordran av telemetri från servern:

```

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```



Att **inaktivera valda standard insamlare** – till exempel prestandaräknare, HTTP-begäranden eller beroenden - ta bort eller kommentera ut relevanta rader i [ApplicationInsights.config](app-insights-api-custom-events-metrics.md). Du kan göra detta, till exempel om du vill skicka TrackRequest data.

## <a name="view-system-performance-counters"></a>Visa prestandaräknare för system
Bland de mätvärden som du kan visa i metrics explorer är en uppsättning system prestandaräknare. Det finns en fördefinierad bladet med titeln **servrar** som visar flera.

![Öppna Application Insights-resursen och klicka på servrar](./media/app-insights-how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Om du ser inga prestandaräknardata
* **IIS-servern** på din egen dator eller på en virtuell dator. [Installera statusövervakaren](app-insights-monitor-performance-live-website-now.md).
* **Webbplatsen för Azure** -vi stöder inte prestandaräknare ännu. Det finns flera mått som du kan få som en del av Kontrollpanelen webbplatsen för Azure.
* **UNIX-server** - [installera collectd](app-insights-java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Visa mer prestandaräknare
* Första, [lägga till ett nytt diagram](app-insights-metrics-explorer.md) och se om räknaren finns i grundläggande som vi erbjuder.
* Om inte, [lägga till räknaren uppsättningen som samlas in av prestandaräknarmodulen](app-insights-performance-counters.md).
