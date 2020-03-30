---
title: Använda Sök i Azure Application Insights | Microsoft-dokument
description: Sök och filtrera rå telemetri som skickas av din webbapp.
ms.topic: conceptual
ms.date: 07/30/2019
ms.openlocfilehash: 8039a55784f63030f330d6c1e2061e99b8b63bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275989"
---
# <a name="using-search-in-application-insights"></a>Använda Sök i programinsikter

Sök är en funktion [i Application Insights](../../azure-monitor/app/app-insights-overview.md) som du använder för att hitta och utforska enskilda telemetriobjekt, till exempel sidvisningar, undantag eller webbförfrågningar. Och du kan visa loggspårningar och händelser som du har kodat.

(Mer komplexa frågor över dina data använder du [Analytics](../../azure-monitor/log-query/get-started-portal.md).)

## <a name="where-do-you-see-search"></a>Var ser du Sök?

### <a name="in-the-azure-portal"></a>I Azure-portalen

Du kan öppna diagnostiksökning från fliken Översikt över programinsikter i ditt program (finns i det övre fältet) eller under undersökning till vänster.

![Fliken Sök](./media/diagnostic-search/view-custom-events.png)

Gå till listrutan Händelsetyper för att se en lista över telemetriobjekt- serverförfrågningar, sidvisningar, anpassade händelser som du har kodat och så vidare. Högst upp i resultatlistan finns ett sammanfattande diagram som visar antalet händelser över tid.

Klicka ut från rullgardinsmenyn eller Uppdatera för att få nya händelser.

### <a name="in-visual-studio"></a>I Visual Studio 2013

I Visual Studio finns också ett sökfönster för application insights. Det är mest användbart för att visa telemetrihändelser som genereras av programmet som du felsöker. Men det kan också visa de händelser som samlats in från din publicerade app på Azure-portalen.

Öppna sökfönstret i Visual Studio:

![Sök efter programinsikter i Visual Studio](./media/diagnostic-search/32.png)

Sökfönstret har funktioner som liknar webbportalen:

![Sökfönstret i Visual Studio Application Insights](./media/diagnostic-search/34.png)

Fliken Spåra funktion är tillgänglig när du öppnar en begäran eller en sidvy. En "operation" är en sekvens av händelser som är associerade med en enda begäran eller sidvy. Till exempel kan beroendeanrop, undantag, spårningsloggar och anpassade händelser vara en del av en enda åtgärd. På fliken Spåra funktion visas tidsinställningarna och varaktigheten för dessa händelser grafiskt i förhållande till begäran eller sidvyn.

## <a name="inspect-individual-items"></a>Granska enskilda objekt

Markera ett telemetriobjekt om du vill visa nyckelfält och relaterade objekt.

![Skärmbild av en enskild beroendebegäran](./media/diagnostic-search/telemetry-item.png)

Detta kommer att starta vyn heltäckande transaktionsinformation.

## <a name="filter-event-types"></a>Filtrera händelsetyper

Öppna listrutan Händelsetyper och välj de händelsetyper som du vill visa. (Om du senare vill återställa filtren klickar du på Återställ.)

Händelsetyperna är:

* **Spåra** - [diagnostikloggar](../../azure-monitor/app/asp-net-trace-logs.md) inklusive TrackTrace, log4Net, NLog och System.Diagnostic.Trace-anrop.
* **Begäran** - HTTP-begäranden som tas emot av serverprogrammet, inklusive sidor, skript, bilder, formatfiler och data. Dessa händelser används för att skapa översiktsdiagram för begäran och svar.
* **Sidvisning** - [telemetri skickas av webbklienten](../../azure-monitor/app/javascript.md), som används för att skapa sidvyrapporter.
* **Anpassad händelse** - Om du infogade samtal till TrackEvent() för att [övervaka användningen](../../azure-monitor/app/api-custom-events-metrics.md)kan du söka efter dem här.
* **Undantag** - Undantag undantag [på servern](../../azure-monitor/app/asp-net-exceptions.md)och de som du loggar med TrackException().
* **Beroende**[Samtal från serverprogrammet](../../azure-monitor/app/asp-net-dependencies.md) till andra tjänster som REST-API:er eller databaser och AJAX-anrop från [klientkoden](../../azure-monitor/app/javascript.md). - 
* **Tillgänglighet** - Resultat av [tillgänglighetstester](../../azure-monitor/app/monitor-web-app-availability.md).

## <a name="filter-on-property-values"></a>Filtrera på egenskapsvärden

Du kan filtrera händelser på värdena för deras egenskaper. Vilka egenskaper som är tillgängliga beror på vilka händelsetyper du har valt. Klicka på filterikonen ![Ikonen Filter](./media/diagnostic-search/filter-icon.png) för att starta.

Att välja inga värden för en viss egenskap har samma effekt som att välja alla värden. Den stänger av filtrering på den egenskapen.

Observera att antalet till höger om filtervärdena visar hur många förekomster det finns i den aktuella filtrerade uppsättningen.

## <a name="find-events-with-the-same-property"></a>Hitta händelser med samma egenskap

Om du vill söka efter alla objekt med samma egenskapsvärde skriver du det antingen i sökfältet eller klickar på kryssrutan när du tittar igenom egenskaperna på filterfliken.

![Klicka på kryssrutan för en egenskap på filterfliken](./media/diagnostic-search/filter-property.png)

## <a name="search-the-data"></a>Sök efter data

> [!NOTE]
> Om du vill skriva mer komplexa frågor öppnar [**du Loggar (Analytics)**](../../azure-monitor/log-query/get-started-portal.md) högst upp i sökbladet.
>

Du kan söka efter termer i något av egenskapsvärdena. Detta är användbart om du har skrivit [anpassade händelser](../../azure-monitor/app/api-custom-events-metrics.md) med egenskapsvärden.

Du kanske vill ange ett tidsintervall, eftersom sökningar över ett kortare intervall är snabbare.

![Öppna diagnostiksökning](./media/diagnostic-search/search-property.png)

Sök efter fullständiga ord, inte delsträngar. Använd citattecken för att omge specialtecken.

| String | *Hittades inte* | Hittade |
| --- | --- | --- |
| HomeController.Om |`home`<br/>`controller`<br/>`out` | `homecontroller`<br/>`about`<br/>`"homecontroller.about"`|
|USA|`Uni`<br/>`ted`|`united`<br/>`states`<br/>`united AND states`<br/>`"united states"`

Här är de sökuttryck som du kan använda:

| Exempelfråga | Verkan |
| --- | --- |
| `apple` |Hitta alla händelser i tidsintervallet vars fält innehåller ordet "äpple" |
| `apple AND banana` <br/>`apple banana` |Hitta händelser som innehåller båda orden. Använd kapital "OCH", inte "och". <br/>Kort form. |
| `apple OR banana` |Hitta händelser som innehåller något av orden. Använd "ELLER", inte "eller". |
| `apple NOT banana` |Hitta händelser som innehåller ett ord men inte det andra. |

## <a name="sampling"></a>Samling

Om din app genererar en stor mängd telemetri (och du använder ASP.NET SDK version 2.0.0-beta3 eller senare), minskar den adaptiva samplingsmodulen automatiskt volymen som skickas till portalen genom att bara skicka en representativ bråkdel av händelserna. Händelser som är relaterade till samma begäran väljs eller avmarkeras som en grupp, så att du kan navigera mellan relaterade händelser.

[Lär dig mer om insamling](../../azure-monitor/app/sampling.md).

## <a name="create-work-item"></a>Skapa arbetsuppgift

Du kan skapa ett fel i GitHub eller Azure DevOps med information från alla telemetriobjekt.

Gå till detaljvyn för transaktion från slutpunkt till slutpunkt genom att klicka på ett telemetriobjekt och välj sedan **Skapa arbetsobjekt**.

![Klicka på Nytt arbetsobjekt, redigera fälten och klicka sedan på OK.](./media/diagnostic-search/work-item.png)

Första gången du gör detta ombeds du att konfigurera en länk till din Azure DevOps-organisation och -projekt.

(Du kan också konfigurera länken på fliken Arbetsobjekt.)

## <a name="send-more-telemetry-to-application-insights"></a>Skicka mer telemetri till Application Insights

Förutom den färdiga telemetrin som skickas av Application Insights SDK kan du:

* Fånga loggspårningar från ditt favoritloggningsramverk i [.NET](../../azure-monitor/app/asp-net-trace-logs.md) eller [Java](../../azure-monitor/app/java-trace-logs.md). Det innebär att du kan söka igenom dina loggspårningar och korrelera dem med sidvisningar, undantag och andra händelser.
* [Skriv kod](../../azure-monitor/app/api-custom-events-metrics.md) för att skicka anpassade händelser, sidvisningar och undantag.

[Lär dig hur du skickar loggar och anpassad telemetri till Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).

## <a name="q--a"></a><a name="questions"></a>Q & A

### <a name="how-much-data-is-retained"></a><a name="limits"></a>Hur mycket data lagras?

Se [sammanfattningen Gränser](../../azure-monitor/app/pricing.md#limits-summary).

### <a name="how-can-i-see-post-data-in-my-server-requests"></a>Hur kan jag se POST-data i mina serverförfrågningar?

Vi loggar inte POST-data automatiskt, men du kan använda [TrackTrace eller logga samtal](../../azure-monitor/app/asp-net-trace-logs.md). Placera POST-data i meddelandeparametern. Du kan inte filtrera på meddelandet på samma sätt som du kan filtrera på egenskaper, men storleksgränsen är längre.

## <a name="next-steps"></a><a name="add"></a>Nästa steg

* [Skriva komplexa frågor i Analytics](../../azure-monitor/log-query/get-started-portal.md)
* [Skicka loggar och anpassad telemetri till Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
* [Ställa in tillgänglighets- och svarstiderstester](../../azure-monitor/app/monitor-web-app-availability.md)
* [Troubleshooting](../../azure-monitor/app/troubleshoot-faq.md) (Felsökning)
