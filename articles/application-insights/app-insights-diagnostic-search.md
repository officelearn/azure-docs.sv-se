---
title: "Använda sökning i Azure Application Insights | Microsoft Docs"
description: "Sök och filtrera rådata telemetri som skickas av ditt webbprogram."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 2a437555-8043-45ec-937a-225c9bf0066b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: e2d12f807756b778a64920b12a66fba184a99844
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="using-search-in-application-insights"></a>Använda sökning i Application Insights
Sökningen är en funktion i [Programinsikter](app-insights-overview.md) att du använder för att söka efter och utforska enskilda telemetri objekt, till exempel sidvyer undantag, eller webbförfrågningar. Och du kan visa loggspårningar och händelser som du har kodats.

(Mer komplexa frågor över dina data, Använd [Analytics](app-insights-analytics-tour.md).)

## <a name="where-do-you-see-search"></a>Där kan du se sökningen?
### <a name="in-the-azure-portal"></a>I Azure-portalen
Du kan öppna diagnostiska sökning uttryckligen från bladet översikt över Application Insights i ditt program:

![Öppna diagnostiska sökning](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Det kan öppnas när du klickar på via några diagram och rutnät artiklar. I det här fallet ställs inför dess filter att fokusera på typ av objekt du valt. 

Till exempel på bladet översikt finns ett stapeldiagram begäranden som klassificerats av svarstid. Klicka dig igenom ett prestanda adressintervall som ska visa en lista med enskilda förfrågningar i tidsintervall som svar:

![Klicka dig igenom begäran prestanda](./media/app-insights-diagnostic-search/07-open-from-filters.png)

Huvuddelen av diagnostiska Search är en lista över telemetri objekt - serverbegäranden, sidan vyer, anpassade händelser som du har ett kodat och så vidare. Överst i listan är en sammanfattande diagram som visar antalet händelser över tid.

Klicka på Uppdatera för att få nya händelser.

### <a name="in-visual-studio"></a>I Visual Studio 2013

I Visual Studio finns också en Application Insights sökfönstret. Det är mest användbara för att visa telemetri händelser som genererats av det program som du felsöker. Men det kan också visa händelser som samlas in från din publicerade app på Azure-portalen.

Öppna fönstret Sök i Visual Studio:

![Visual Studio öppna Application Insights-sökning](./media/app-insights-diagnostic-search/32.png)

Fönstret har funktioner som liknar webbportalen:

![Visual Studio Application Insights sökfönstret](./media/app-insights-diagnostic-search/34.png)

Fliken Spåra åtgärden är tillgänglig när du öppnar en begäran eller vyn sida. Ett ' åtgärden ' är en sekvens av händelser som är associerad med en enda vy för begäran eller sidan. Till exempel kan beroendeanrop, undantag, loggar och anpassade händelser ingå i en enda åtgärd. Fliken Spåra åtgärden visar grafiskt tidsinställning och varaktighet för dessa händelser i förhållande till vyn begäran eller sidan. 

## <a name="inspect-individual-items"></a>Inspektera enskilda objekt
Välj en telemetri post för att se nyckelfält och relaterade objekt. Klicka på ”...” Om du vill se en fullständig uppsättning fält. 

![Klicka på nytt arbetsobjekt, redigera fälten och klicka sedan på OK.](./media/app-insights-diagnostic-search/10-detail.png)

## <a name="filter-event-types"></a>Filtrera händelsetyper
Öppna bladet Filter och välj händelsetyper som du vill se. (Om du senare vill du återställa filtren som du har öppnat bladet, klickar du på Återställ.)

![Välj Filter och välj telemetri typer](./media/app-insights-diagnostic-search/02-filter-req.png)

Händelsetyper är:

* **Spåra** - [diagnostikloggar](app-insights-asp-net-trace-logs.md) inklusive TrackTrace, log4Net, NLog och System.Diagnostic.Trace anrop.
* **Begära** -HTTP-begäranden tas emot av serverprogrammet, inklusive sidor, skript, bilder, filer och data. Dessa händelser används för att skapa förfrågan och svar översikt över diagram.
* **Vyn sida** - [telemetri som skickas av webbklienten](app-insights-javascript.md), som används för att skapa sidan Visa rapporter. 
* **Anpassad händelse** - om du har infogat anrop till trackevent () för att [övervaka användningen](app-insights-api-custom-events-metrics.md), kan du söka dem här.
* **Undantag** – utan felhantering [undantag i servern](app-insights-asp-net-exceptions.md), och de som du loggar in med hjälp av TrackException().
* **Beroende** - [anrop från serverprogrammet](app-insights-asp-net-dependencies.md) till andra tjänster som REST API: er eller databaser och AJAX-anrop från din [klientkod](app-insights-javascript.md).
* **Tillgänglighet** -resultaten av [tillgänglighetstester](app-insights-monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrera på egenskapsvärden
Du kan filtrera händelser på värdena i deras egenskaper. De tillgängliga egenskaperna är beroende av händelsetyper som du har valt. 

Till exempel välja ut begäranden med en specifik svarskod. 

![Expandera en egenskap och väljer ett värde](./media/app-insights-diagnostic-search/03-response500.png)

Om du väljer några värden för en viss egenskap har samma effekt som att välja alla värden. Den växlar av filtreringen för den egenskapen.

### <a name="narrow-your-search"></a>Begränsa sökningen
Observera att antalet till höger om filtervärdena visas hur många förekomster det finns i den aktuella filtrerade uppsättningen. 

Det är tydligt att begära 'Rpthuvud/anställda' resultat i de flesta av fel som ”500” i det här exemplet:

![Expandera en egenskap och väljer ett värde](./media/app-insights-diagnostic-search/04-failingReq.png)




## <a name="find-events-with-the-same-property"></a>Sök efter händelser med samma egenskap
Sök efter alla objekt med samma egenskapsvärde för:

![Högerklicka på en egenskap](./media/app-insights-diagnostic-search/12-samevalue.png)


## <a name="search-the-data"></a>Söka efter data

> [!NOTE]
> Om du vill skriva mer komplexa frågor, öppna [ **Analytics** ](app-insights-analytics-tour.md) högst upp på bladet sökning.
> 

Du kan söka efter villkoren i någon av egenskapsvärden. Detta är särskilt användbart om du har skrivit [anpassade händelser](app-insights-api-custom-events-metrics.md) med egenskapsvärden. 

Du kanske vill ange en tid som intervall, som söker över ett kortare intervall är snabbare. 

![Öppna diagnostiska sökning](./media/app-insights-diagnostic-search/appinsights-311search.png)

Sök efter fullständiga ord, inte delsträngar. Använd citattecken för att omsluta specialtecken.

| Sträng | är *inte* genom | men dessa hittar den |
| --- | --- | --- |
| HomeController.About |hem<br/>Domänkontrollant<br/>Ut | homecontroller<br/>Om<br/>”homecontroller.about”|
|USA|UNI<br/>Tomas|Förenade<br/>tillstånd<br/>Förenade och tillstånd<br/>”USA”

Här följer sökuttryck som du kan använda:

| Exempelfråga | Verkan |
| --- | --- |
| `apple` |Sök efter alla händelser i tidsintervall vars fält inkludera ordet ”apple” |
| `apple AND banana` |Sök efter händelser som innehåller båda orden. Använd kapital ”och”, inte ”och”. |
| `apple OR banana`<br/>`apple banana` |Sök efter händelser som innehåller antingen word. Använda ”eller”, inte ”eller”.<br/>Kort form. |
| `apple NOT banana` |Sök efter händelser som innehåller ett ord, men inte på den andra. |



## <a name="sampling"></a>Samling
Om din app genererar mycket telemetri (och du använder SDK för ASP.NET version 2.0.0-beta3 eller senare), modulen anpassningsbar samplingsfrekvensen minskar automatiskt den volym som skickas till portalen genom att skicka en representativ del av händelser. Dock är händelser som är relaterade till samma begäran markeras eller avmarkeras som en grupp, så att du kan navigera mellan relaterade händelser 

[Lär dig mer om insamling](app-insights-sampling.md).



## <a name="create-work-item"></a>Skapa arbetsobjekt
Du kan skapa ett programfel i GitHub eller Visual Studio Team Services med information från ett telemetri-objekt. 

![Klicka på nytt arbetsobjekt, redigera fälten och klicka sedan på OK.](./media/app-insights-diagnostic-search/42.png)

Första gången du göra detta, uppmanas du att konfigurera en länk till ditt Team Services-konto och projekt.

![Fyll URL Team Services-server och projektets namn och klicka på auktorisera](./media/app-insights-diagnostic-search/41.png)

(Du kan också konfigurera länken på bladet arbetsobjekt.)

## <a name="save-your-search"></a>Spara sökningen
När du har angett alla filter som du vill kan spara du sökningen som en favorit. Om du arbetar i ett organisationskonto kan välja du om du vill dela den med andra medlemmar.

![Klicka på favorit, ange namn och klicka på Spara](./media/app-insights-diagnostic-search/08-favorite-save.png)

Visa sökningen igen, **gå till bladet översikt** och öppna Favoriter:

![Panelen Favoriter](./media/app-insights-diagnostic-search/09-favorite-get.png)

Om du har sparat med relativa tidsintervall har den senaste informationen i bladet öppnas på nytt. Om du har sparat med absolut tidsintervall ser du samma data varje gång. (Om 'Relativa' är inte tillgänglig när du vill spara en favorit, klicka på tidsintervall i huvudet ange ett tidsintervall som inte är ett anpassat intervall)

## <a name="send-more-telemetry-to-application-insights"></a>Skicka telemetri om flera till Application Insights
Förutom out box telemetri som skickas av Application Insights SDK, kan du:

* Avbilda loggspårningar från dina Favoriter loggningsramverk i [.NET](app-insights-asp-net-trace-logs.md) eller [Java](app-insights-java-trace-logs.md). Detta innebär att du kan söka igenom din loggspårningar och korrelera dem med sidvisningar, undantag och andra händelser. 
* [Skriva kod](app-insights-api-custom-events-metrics.md) att skicka anpassade händelser, sidvisningar och undantag. 

[Lär dig att skicka loggar och anpassad telemetri till Application Insights](app-insights-asp-net-trace-logs.md).

## <a name="questions"></a>FRÅGOR OCH SVAR
### <a name="limits"></a>Hur mycket data finns kvar?

Finns det [gränser sammanfattning](app-insights-pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hur kan jag se postdata i min serverbegäranden?
Vi inte logga data efter automatiskt, men du kan använda [TrackTrace eller loggfil anrop](app-insights-asp-net-trace-logs.md). Placera efter data i parametern meddelandet. Det går inte att filtrera meddelandet på samma sätt som du kan filtrera efter egenskaper, men storleksgränsen är längre.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="add"></a>Nästa steg
* [Skriva komplexa frågor i Analytics](app-insights-analytics-tour.md)
* [Skicka loggar och anpassad telemetri till Application Insights](app-insights-asp-net-trace-logs.md)
* [Ställ in tillgänglighet och svarstider tester](app-insights-monitor-web-app-availability.md)
* [Felsökning](app-insights-troubleshoot-faq.md)
