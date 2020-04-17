---
title: Hur gör jag ... i Azure Application Insights | Microsoft-dokument
description: Vanliga frågor och svar i Application Insights.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 8d4b1e79c48b14ed7dce756468e4c48d633c3f04
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536870"
---
# <a name="how-do-i--in-application-insights"></a>Hur kan jag ... i Application Insights?
## <a name="get-an-email-when-"></a>Få ett mail när ...
### <a name="email-if-my-site-goes-down"></a>E-post om min webbplats går ner
Ange ett [webbtest för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-post om min webbplats är överbelastad
Ange en [avisering](../../azure-monitor/app/alerts.md) på **serverns svarstid**. En tröskel mellan 1 och 2 sekunder bör fungera.

![](./media/how-do-i/030-server.png)

Din app kan också visa tecken på påfrestning genom att returnera felkoder. Ange en avisering för **misslyckade begäranden**.

Om du vill ange en avisering på **serverundantag**kan du behöva göra [ytterligare installationer](../../azure-monitor/app/asp-net-exceptions.md) för att kunna se data.

### <a name="email-on-exceptions"></a>E-post om undantag
1. [Ställ in undantagsövervakning](../../azure-monitor/app/asp-net-exceptions.md)
2. [Ange en avisering](../../azure-monitor/app/alerts.md) för måttet Antal undantag

### <a name="email-on-an-event-in-my-app"></a>E-post på ett evenemang i min app
Anta att du vill få ett e-postmeddelande när en viss händelse inträffar. Application Insights tillhandahåller inte den här funktionen direkt, men den kan [skicka en avisering när ett mått överskrider ett tröskelvärde](../../azure-monitor/app/alerts.md).

Aviseringar kan ställas in på [anpassade mått](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric), men inte anpassade händelser. Skriv en kod för att öka ett mått när händelsen inträffar:

    telemetry.TrackMetric("Alarm", 10);

eller:

    var measurements = new Dictionary<string,double>();
    measurements ["Alarm"] = 10;
    telemetry.TrackEvent("status", null, measurements);

Eftersom aviseringar har två tillstånd måste du skicka ett lågt värde när du anser att aviseringen har avslutats:

    telemetry.TrackMetric("Alarm", 0.5);

Skapa ett diagram i [måttutforskaren](../../azure-monitor/platform/metrics-charts.md) för att se ditt alarm:

![](./media/how-do-i/010-alarm.png)

Ställ nu in en varning för brand när måttet går över ett mellanvärde under en kort period:

![](./media/how-do-i/020-threshold.png)

Ange medelvärdesperioden till ett minimum.

Du får e-postmeddelanden både när måttet går över och under tröskelvärdet.

Några saker att tänka på:

* En avisering har två tillstånd ("alert" och "felfri"). Tillståndet utvärderas endast när ett mått tas emot.
* Ett e-postmeddelande skickas endast när tillståndet ändras. Det är därför du måste skicka både högt och lågt värde mått.
* För att utvärdera aviseringen tas medelvärdet av de mottagna värdena under föregående period. Detta inträffar varje gång ett mått tas emot, så att e-postmeddelanden kan skickas oftare än den period du anger.
* Eftersom e-postmeddelanden skickas både på "alert" och "friska", kanske du vill överväga att tänka om din one-shot händelse som en två-tillstånd villkor. I stället för en "slutförd jobb"-händelse har du till exempel ett villkor för "pågående jobb", där du får e-postmeddelanden i början och slutet av ett jobb.

### <a name="set-up-alerts-automatically"></a>Ställ in aviseringar automatiskt
[Använda PowerShell för att skapa nya aviseringar](../../azure-monitor/app/alerts.md#automation)

## <a name="use-powershell-to-manage-application-insights"></a>Använda PowerShell för att hantera programinsikter
* [Skapa nya resurser](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Skapa nya aviseringar](../../azure-monitor/app/alerts.md#automation)

## <a name="separate-telemetry-from-different-versions"></a>Separat telemetri från olika versioner

* Flera roller i en app: Använd en enda Application Insights-resurs och filtrera på [cloud_Rolename](../../azure-monitor/app/app-map.md).
* Separera utvecklings-, test- och utgivningsversioner: Använd olika Application Insights-resurser. Plocka upp instrumenteringsnycklarna från web.config. [Läs mer](../../azure-monitor/app/separate-resources.md)
* Rapportera versioner av byggversioner: Lägg till en egenskap med hjälp av en telemetriinitierare. [Läs mer](../../azure-monitor/app/separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Övervaka serverdserver och skrivbordsappar
[Använd Windows Server SDK-modulen](../../azure-monitor/app/windows-desktop.md).

## <a name="visualize-data"></a>Visualisera data
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Instrumentpanel med mått från flera appar
* I [Metric Explorer](../../azure-monitor/platform/metrics-charts.md)anpassar du diagrammet och sparar det som en favorit. Fäst den på Azure-instrumentpanelen.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Instrumentpanel med data från andra källor och Application Insights
* [Exportera telemetri till Power BI](../../azure-monitor/app/export-power-bi.md ).

Eller

* Använd SharePoint som instrumentpanel och visa data i SharePoint-webbdelar. [Använd kontinuerlig export och Stream Analytics för att exportera till SQL](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md).  Använd PowerView för att undersöka databasen och skapa en SharePoint-webbdel för PowerView.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Filtrera bort anonyma eller autentiserade användare
Om användarna loggar in kan du ange det [autentiserade användar-ID:t](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users). (Det sker inte automatiskt.)

Sedan kan du:

* Sök på specifika användar-ID:er

![](./media/how-do-i/110-search.png)

* Filtrera mått till anonyma eller autentiserade användare

![](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Ändra egenskapsnamn eller värden
Skapa ett [filter](../../azure-monitor/app/api-filtering-sampling.md#filtering). På så sätt kan du ändra eller filtrera telemetri innan den skickas från din app till Application Insights.

## <a name="list-specific-users-and-their-usage"></a>Lista specifika användare och deras användning
Om du bara vill [söka efter specifika användare](#search-specific-users)kan du ange det [autentiserade användar-ID: t](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users).

Om du vill ha en lista över användare med data, till exempel vilka sidor de tittar på eller hur ofta de loggar in, har du två alternativ:

* [Ange autentiserat användar-ID,](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) [exportera till en databas](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) och använd lämpliga verktyg för att analysera dina användardata där.
* Om du bara har ett litet antal användare skickar du anpassade händelser eller mått med hjälp av data av intresse som måttvärde eller händelsenamn och anger användar-ID som en egenskap. Om du vill analysera sidvisningar ersätter du det vanliga JavaScript trackPageView-anropet. Om du vill analysera telemetri på serversidan använder du en telemetriinitierare för att lägga till användar-ID i all servertelemetri. Du kan sedan filtrera och segmentera mått och sökningar på användar-ID.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Minska trafiken från min app till Application Insights
* I [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)inaktiverar du alla moduler som du inte behöver, till exempel prestandaräknarens insamlare.
* Använd [Sampling och filtrering](../../azure-monitor/app/api-filtering-sampling.md) på SDK.
* På dina webbsidor, Begränsa antalet Ajax samtal rapporteras för varje sidvisning. Infoga: `,maxAjaxCallsPerView:3` (eller ett `instrumentationKey:...` lämpligt tal) i skriptutdraget efter.
* Om du använder [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric)beräknar du summan av batchar med måttvärden innan du skickar resultatet. Det finns en överbelastning av TrackMetric() som ger för det.

Läs mer om [priser och kvoter](../../azure-monitor/app/pricing.md).

## <a name="disable-telemetry"></a>Inaktivera telemetri
Så här stoppar och startar du insamlingen och överföringen av telemetri **dynamiskt** från servern:

### <a name="aspnet-classic-applications"></a>ASP.NET klassiska program

```csharp
    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Andra applikationer
Det rekommenderas inte `TelemetryConfiguration.Active` att använda singleton på konsol eller ASP.NET Core-program.
om du `TelemetryConfiguration` skapade instans `DisableTelemetry` `true`själv - ställ in på .

För ASP.NET Core-program kan `TelemetryConfiguration` du komma åt instans med [ASP.NET Core beroendeinjektion](/aspnet/core/fundamentals/dependency-injection/). Mer information finns i [ApplicationInsights för ASP.NET Core-programartikel.](../../azure-monitor/app/asp-net-core.md)

## <a name="disable-selected-standard-collectors"></a>Inaktivera valda standardinsamlare
Du kan inaktivera standardinsamlare (till exempel prestandaräknare, HTTP-begäranden eller beroenden)

* **ASP.NET program** - Ta bort eller kommentera de relevanta raderna i [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)
* **ASP.NET Core-program** – Följ konfigurationsalternativ för telemetrimoduler i [ApplicationInsights ASP.NET Core](../../azure-monitor/app/asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Visa systemprestandaräknare
Bland de mått som du kan visa i mått explorer är en uppsättning systemprestandaräknare. Det finns ett fördefinierat blad med titeln **Servrar** som visar flera av dem.

![Öppna enheten Application Insights och klicka på Servrar](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Om du inte ser några prestandaräknaredata
* **IIS-server** på din egen dator eller på en virtuell dator. [Installera statusövervakare](../../azure-monitor/app/monitor-performance-live-website-now.md).
* **Azure-webbplats** – vi stöder inte prestandaräknare ännu. Det finns flera mått som du kan få som en standarddel av Kontrollpanelen för Webbplatsen för Azure.
* **Unix-server** - [Installera samlad](../../azure-monitor/app/java-collectd.md)

### <a name="to-display-more-performance-counters"></a>Så här visar du fler prestandaräknare
* Lägg [först till ett nytt diagram](../../azure-monitor/platform/metrics-charts.md) och se om räknaren finns i den grundläggande uppsättning som vi erbjuder.
* Om inte, [lägg till räknaren i den uppsättning som samlas in av prestandaräknaren modulen](../../azure-monitor/app/performance-counters.md).
