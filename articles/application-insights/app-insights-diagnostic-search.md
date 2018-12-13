---
title: Använda sökning i Azure Application Insights | Microsoft Docs
description: Sök och filtrera rådata telemetri som skickas av din webbapp.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mbullwin
ms.openlocfilehash: c595695f3643522d36b53091656c3655f83a9345
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877844"
---
# <a name="using-search-in-application-insights"></a>Använda sökning i Application Insights
Search är en funktion i [Application Insights](app-insights-overview.md) att du använder för att hitta och utforska enskilda telemetri-objekt, till exempel sidvisningar, undantag, eller webb-begäranden. Och du kan visa loggspårningar och händelser som du har kodat.

(För mer komplexa frågor över dina data använder [Analytics](../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Där ser du Search?

### <a name="in-the-azure-portal"></a>I Azure portal

Du kan öppna diagnostiksökning uttryckligen från bladet översikt över Application Insights i ditt program:

![Öppna diagnostiksökning](./media/app-insights-diagnostic-search/001.png)

![Skärmbild av diagnostiksökning diagram](./media/app-insights-diagnostic-search/002.png)

Diagnostiksökning brödtext är en lista över telemetri objekt – serverbegäranden, sidan vyer, anpassade händelser som du har kodat och så vidare. Överst i listan är en sammanfattning av diagram som visar antalet händelser över tid.

Klicka på Uppdatera för att få nya händelser.

### <a name="in-visual-studio"></a>I Visual Studio 2013

I Visual Studio finns även en Application Insights-sökfönstret. Det är mest användbara för att visa telemetrihändelser som genereras av programmet som du felsöker. Men det kan också visa händelser som samlas in från din publicerade app på Azure-portalen.

Öppna fönstret i Visual Studio:

![Visual Studio öppna Application Insights-sökning](./media/app-insights-diagnostic-search/32.png)

Sökfönstret har funktioner som liknar webbportalen:

![Visual Studio Application Insights-sökfönstret](./media/app-insights-diagnostic-search/34.png)

Fliken Spåra åtgärden är tillgänglig när du öppnar en begäran eller en Sidvisning. Åtgärden för ett '' är en sekvens av händelser som är kopplad till en enda förfrågan eller sidvisning. Beroendeanrop, undantag, spårningsloggar och anpassade händelser kan exempelvis vara en del av en enda åtgärd. Fliken Spåra åtgärden visar grafiskt tidpunkten och varaktighet av dessa händelser i förhållande till förfrågan eller sidvisning. 

## <a name="inspect-individual-items"></a>Granska enskilda objekt

Välj alla telemetriobjekt för att se nyckelfält och relaterade objekt.

![Skärmbild av en begäran om enskilda beroende](./media/app-insights-diagnostic-search/003.png)

Detaljer för slutpunkt till slutpunkt transaktion startas:

![Skärmbild av detaljer för slutpunkt till slutpunkt-transaktion.](./media/app-insights-diagnostic-search/004.png)

## <a name="filter-event-types"></a>Filtrera händelsetyper
Öppna bladet Filter och välj händelsetyper som du vill se. (Om du senare vill återställa filtren som du har öppnat bladet, klickar du på Återställ.)

![Välj Filter och välj telemetrityper](./media/app-insights-diagnostic-search/02-filter-req.png)

Händelsetyper är:

* **Spårningen** - [diagnostikloggar](app-insights-asp-net-trace-logs.md) inklusive TrackTrace, log4Net, NLog och System.Diagnostic.Trace anrop.
* **Begär** -HTTP-begäranden som tas emot av serverprogrammet, inklusive sidor, skript, bilder, filer och data. De här händelserna används för att skapa begäran och svaret översiktsdiagrammet.
* **Sidvisning** - [telemetri som skickas av webbklienten](app-insights-javascript.md)används för att skapa sidan Visa rapporter. 
* **Anpassad händelse** – om du har infogat anrop till TrackEvent() för att [övervaka användning](app-insights-api-custom-events-metrics.md), du kan söka efter den här.
* **Undantag** – utan felhantering [undantag i servern](app-insights-asp-net-exceptions.md), och de som du loggar med TrackException().
* **Beroende** - [anrop från serverprogrammet](app-insights-asp-net-dependencies.md) till andra tjänster som REST API: er eller databaser och AJAX-anrop från din [klientkod](app-insights-javascript.md).
* **Tillgänglighet** -resultatet av [tillgänglighetstester](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrera på egenskapsvärden
Du kan filtrera händelser på värdena i deras egenskaper. Tillgängliga egenskaper är beroende av händelsetyper som du har valt. 

Till exempel välja ut begäranden med en specifik svarskod. 

![Expandera en egenskap och välj ett värde](./media/app-insights-diagnostic-search/03-response500.png)

Välja inga värden för en viss egenskap har samma effekt som att välja alla värden. Det växlar av filtreringen på egenskapen.

### <a name="narrow-your-search"></a>Begränsa sökningen
Observera att antalet till höger om filtervärdena som visar hur många förekomster det tillhör den aktuella filtrera uppsättningen. 

Det är tydligt att begära de ”rpthuvud/anställda” resulterar i de flesta av de '500-fel i det här exemplet:

![Expandera en egenskap och välj ett värde](./media/app-insights-diagnostic-search/04-failingReq.png)

## <a name="find-events-with-the-same-property"></a>Söka efter händelser med samma egenskap
Hitta alla objekt med samma egenskapsvärde:

![Högerklicka på en egenskap](./media/app-insights-diagnostic-search/12-samevalue.png)

## <a name="search-the-data"></a>Söka efter data

> [!NOTE]
> Om du vill skriva mer komplexa frågor, öppna [ **Analytics** ](../azure-monitor/log-query/get-started-portal.md) högst upp på bladet Search.
> 

Du kan söka efter termer i egenskapen-värden. Detta är särskilt användbart om du har skrivit [anpassade händelser](app-insights-api-custom-events-metrics.md) med egenskapsvärden. 

Du kanske vill ange ett adressintervall som sökningar över ett kortare intervall är snabbare. 

![Öppna diagnostiksökning](./media/app-insights-diagnostic-search/appinsights-311search.png)

En sökning efter fullständiga ord, inte delsträngar. Använd citattecken om du vill ange specialtecken.

| sträng | är *inte* genom | men dessa gör att hitta |
| --- | --- | --- |
| HomeController.About |hem<br/>domänkontrollant<br/>ut | homecontroller<br/>om<br/>”homecontroller.about”|
|USA|UNI<br/>valda|USA<br/>tillstånd<br/>USA och<br/>”USA”

Här följer sökuttryck som du kan använda:

| Exempelfråga | Verkan |
| --- | --- |
| `apple` |Hitta alla händelser i tidsintervallet vars fält innehåller ordet ”äpple” |
| `apple AND banana` <br/>`apple banana` |Hitta händelser som innehåller båda orden. Använda kapital ”och” inte ”och”. <br/>Kortformat. |
| `apple OR banana` |Söka efter händelser som innehåller antingen word. Använda ”eller”, inte ”eller”. |
| `apple NOT banana` |Söka efter händelser som innehåller ett ord, men inte på den andra. |

## <a name="sampling"></a>Samling
Om din app genererar mycket telemetri (och du använder ASP.NET SDK version 2.0.0-beta3 eller senare), den anpassningsbara insamlingsmodulen automatiskt minskar den mängd som skickas till portalen genom att skicka en representativ del av händelserna. Dock händelser som är relaterade till samma begäran markerat eller avmarkerat som en grupp, så att du kan navigera mellan relaterade händelser. 

[Lär dig mer om insamling](app-insights-sampling.md).

## <a name="create-work-item"></a>Skapa arbetsobjekt
Du kan skapa en bugg i GitHub eller Azure DevOps med information från alla telemetriobjekt. 

![Klicka på nytt arbetsobjekt och Fyll i fälten.](./media/app-insights-diagnostic-search/42.png)

Första gången du gör detta uppmanas att konfigurera en länk till din Azure DevOps-organisation och dina projekt.

![Fyll URL: en för din Azure DevOps-tjänsterna och projektnamnet och klicka på auktorisera](./media/app-insights-diagnostic-search/41.png)

(Du kan också konfigurera länken på bladet arbetsobjekt.)

## <a name="send-more-telemetry-to-application-insights"></a>Skicka mer telemetri till Application Insights
Förutom out-of the box telemetri som skickas av Application Insights SDK, kan du:

* Samla in loggspårningar från önskat loggningsramverk i [.NET](app-insights-asp-net-trace-logs.md) eller [Java](app-insights-java-trace-logs.md). Det innebär att du kan söka igenom dina loggspårningar och korrelera dem med sidvisningar, undantag och andra händelser. 
* [Skriva kod](app-insights-api-custom-events-metrics.md) att skicka anpassade händelser och sidvisningar undantag. 

[Lär dig att skicka loggar och anpassad telemetri till Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>FRÅGOR OCH SVAR
### <a name="limits"></a>Hur mycket data finns kvar?

Se den [gränser sammanfattning](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hur kan jag se postdata i min serverbegäranden?
Vi loggas inte att skicka data automatiskt, men du kan använda [TrackTrace eller log anrop](app-insights-asp-net-trace-logs.md). Placera postdatan i parametern meddelande. Du kan inte filtrera på meddelandet på samma sätt som du kan filtrera efter egenskaper, men storleksgränsen är längre.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Nästa steg
* [Skriva komplexa frågor i Analytics](../azure-monitor/log-query/get-started-portal.md)
* [Skicka loggar och anpassad telemetri till Application Insights](app-insights-asp-net-trace-logs.md)
* [Konfigurera tillgänglighet och svarstider tester](app-insights-monitor-web-app-availability.md)
* [Felsökning](app-insights-troubleshoot-faq.md)
