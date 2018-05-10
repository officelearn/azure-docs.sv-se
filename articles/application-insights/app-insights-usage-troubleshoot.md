---
title: Felsöka användningsanalys i Azure Application Insights
description: Felsökningsguide för - och analysera användning av plats och program med Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin;daviste
ms.openlocfilehash: 654b99085c406f13fe95476457234761bf840422
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Felsöka användningsanalys i Application Insights
Har frågor om den [användning analytics verktyg i Application Insights](app-insights-usage-overview.md): [användare, sessioner, händelser](app-insights-usage-segmentation.md), [skorstenar](usage-funnels.md), [användaren flödar](app-insights-usage-flows.md), [Kvarhållning](app-insights-usage-retention.md), eller kohorter? Här är några svar.

## <a name="counting-users"></a>Räkna användare
**Användningsanalys verktyg visar att min app har en användarsession, men jag vet mitt program har många användare-sessioner. Hur kan jag åtgärda dessa fel antal?**

Alla händelser för telemetri i Application Insights har en [anonym användar-ID](application-insights-data-model-context.md) och en [sessions-ID](application-insights-data-model-context.md) som två av deras egenskaper som standard. Som standard räkna alla verktyg som användning analytics användare och sessioner baserat på dessa ID: N. Om dessa standardegenskaper inte som fylls i med unika ID: N för varje användare och sessionen för din app, visas ett felaktigt antal användare och sessioner i användning analytics verktyg.

Om du övervaka en webbapp, den enklaste lösningen är att lägga till den [Application Insights JavaScript SDK](app-insights-javascript.md) till appen och se till att skriptet fragment har lästs in på varje sida som du vill övervaka. JavaScript SDK automatiskt genererar anonym användare och sessions-ID. och sedan fylls telemetriska händelser med dessa ID: N som skickas de från din app.

Om du övervaka en webbtjänst (utan användargränssnitt) [skapa en telemetri initieraren som fyller på egenskaperna för anonyma användare-ID och session-ID](app-insights-usage-send-user-context.md) enligt din tjänst begrepp unika användare och sessioner.

Om din app skickar [autentiserat användar-ID](app-insights-api-custom-events-metrics.md#authenticated-users), kan du räkna utifrån autentiserat användar-ID i verktyget användare. Välj i listrutan ”Visa”, ”Autentiserade användare”.

Verktyg för användning analytics stöds inte för närvarande cykliska användare eller sessioner baserat på andra egenskaper än anonym användar-ID, autentiserat användar-ID eller sessions-ID.

## <a name="naming-events"></a>Namnge händelser
**Min app har tusentals olika sida och anpassade händelsenamn. Det är svårt att skilja mellan dem, och användning analytics verktyg ofta sluta att svara. Hur kan jag åtgärda problemen namngivning?**

Vyn sida och anpassade händelsenamn används under användning analytics verktyg. Namnge händelser väl är viktigt att hämta värdet från dessa verktyg. Målet är en avvägning mellan har för få, alltför allmänt namn (”knappen klickade”) och som har för många, alltför specifika namn (”knappen Redigera klickat på http://www.contoso.com/index”).

Om du vill göra ändringar i vyn sida och anpassade händelsenamn skickar din app, som du behöver ändra appens källkod och distribuera om. **All telemetri data i Application Insights lagras i 90 dagar och kan inte tas bort**, så du ändrar händelsenamn tar 90 dagar att fullständigt manifest. I 90 dagar efter att du ändrat namn, ska både gamla och nya händelsenamn visas i din telemetri, så justera frågor och kommunicera inom ditt team i enlighet med detta.

Om din app skickar för många vynamn för sidan, kontrollerar du om dessa sidan Visa namn har angetts manuellt i koden eller om de är skickas automatiskt av Application Insights JavaScript SDK:

* Om sidan vynamn manuellt har angetts i kod med hjälp av den [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), ändra namnet till mindre specifika. Undvik vanliga misstag som om URL: en i vyn sida. Använd i stället URL-parametern i den `trackPageView` API. Flytta annan information från sidan vynamn till anpassade egenskaper.

* Om Application Insights JavaScript SDK skickar automatiskt sidan vynamn, kan du ändra dina sidor titlar eller växla till skickar sidan vynamn. SDK skickar den [rubrik](https://developer.mozilla.org/docs/Web/HTML/Element/title) på varje sida som visa sidnamn, som standard. Du kan ändra din rubriker för att vara mer allmän, men Tänk också på SEO och andra konsekvenser som den här ändringen kan ha. Att manuellt ange vyn sida namn med den `trackPageView` API åsidosätter automatiskt insamlade namn, så att du kan skicka mer allmänna namn i telemetri utan att ändra rubriker.   

Om din app skickar för många anpassade händelsenamn, ändra namnet i koden ska vara mindre specifikt. Igen och undvika att sätta webbadresser och andra per sida eller dynamisk information i anpassade händelsenamn direkt. I stället flytta dessa uppgifter till anpassade egenskaper på den anpassade händelsen med den `trackEvent` API. Till exempel i stället för `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, föreslår vi ungefär `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Nästa steg

* [Översikt över användning analytics](app-insights-usage-overview.md)

## <a name="get-help"></a>Få hjälp
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

