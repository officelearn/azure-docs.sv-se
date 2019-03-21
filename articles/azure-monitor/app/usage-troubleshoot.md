---
title: Felsöka användaren beteende analysverktyg i Azure Application Insights
description: Felsökningsguide för – analysera plats och appanvändning med Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: eabc47c2acb33d8c6ee03477b5e8c7783edebbb7
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258791"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Felsöka användaren beteende analysverktyg i Application Insights
Om du har frågor om den [användaren beteende analysverktyg i Application Insights](usage-overview.md): [Användare, sessioner, händelser](usage-segmentation.md), [Trattar](usage-funnels.md), [Användarflöden](usage-flows.md), [kvarhållning](usage-retention.md), eller kohorter? Här är några svar.

## <a name="counting-users"></a>Räkna användare
**Analysverktyg för användaren beteende visar att min app har en användarsession, men jag min app har många användare/sessioner. Hur kan jag åtgärda de här felaktiga antal?**

Alla händelser i Application Insights har en [anonym användar-ID](../../azure-monitor/app/data-model-context.md) och en [sessions-ID](../../azure-monitor/app/data-model-context.md) som två av deras standardegenskaper. Som standard räknas alla analysverktyg användning användare och sessioner baserat på dessa ID: N. Om dessa standardegenskaper inte uppdateras med unika ID: N för varje användare och sessioner av din app, visas ett felaktigt antal användare och sessioner i analysverktyg för användning.

Om du övervakar en webbapp, den enklaste lösningen är att lägga till den [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md) till din app och kontrollera att skriptet kodfragmentet har lästs in på varje sida som du vill övervaka. JavaScript SDK kan du automatiskt genererar anonym användare och sessioner ID: N och sedan fylls telemetrihändelser med dessa ID: N som de har skickats från din app.

Om du övervakar en webbtjänst (inget användargränssnitt) [skapa en telemetri-initierare som fyller på egenskaperna för anonyma användare-ID och session-ID](usage-send-user-context.md) enligt din tjänsts begrepp för unika användare och sessioner.

Om din app skickar [autentiserad användar-ID](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users), du kan räkna utifrån autentiserat användar-ID i verktyget användare. I listrutan ”Visa” Välj ”autentiserade användare”.

Analysverktyg för användaren beteende stöder för närvarande inte cykliska användare eller sessioner baserat på egenskaper än anonym användar-ID, autentiserat användar-ID eller sessions-ID.

## <a name="naming-events"></a>Namngivning av händelser
**Min app har tusentals olika Sidvisning och namn på anpassad händelse. Det är svårt att skilja mellan dem och analysverktyg för användaren beteende svarar ofta. Hur kan jag åtgärda problemen namngivning?**

Sidvy och anpassad händelsenamn används i analysverktyg som användaren beteende. Namnge händelser väl är viktigt att hämta värdet från dessa verktyg. Målet är en balans mellan har för få, alltför allmänt namn (”knappen klickade på”) och gör att för många, alltför specifika namn (”redigeringsknappen klickade på http:\//www.contoso.com/index”).

Om du vill göra ändringar i Sidvisning och anpassad händelsenamn som din app skickar, som du behöver ändra källkoden och distribuera om din app. **All telemetri i Application Insights lagras i 90 dagar och kan inte tas bort**, så att ändringar du gör i händelsenamn tar 90 dagar kan fullständigt visas. I 90 dagar när du har gjort ändringar, ska både gamla och nya händelsenamn visas i telemetrin, så justera frågor och kommunicerade dina team i enlighet med detta.

Om din app skickar för många vynamn för sidan, kontrollerar du om dessa sidan Visa namn anges manuellt i koden eller om de är skickas automatiskt av Application Insights JavaScript SDK:

* Om sidan vynamn manuellt anges i kod med hjälp av den [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), ändra namnet till mindre specifikt. Undvika vanliga misstag som placera URL: en namnet på Sidvisning. Använd i stället URL-parameter för den `trackPageView` API. Flytta annan information från vyn sidnamn i anpassade egenskaper.

* Om Application Insights JavaScript SDK skickar automatiskt visa sidnamn, du antingen ändra din sidor titlar eller växla till skickar han eller hon sidan vynamn. SDK: N skickar den [rubrik](https://developer.mozilla.org/docs/Web/HTML/Element/title) på varje sida som sidnamn för vyn, som standard. Du kan ändra din rubriker för att vara mer allmän, men Tänk också på SEO och andra konsekvenser som den här ändringen kan ha. Att manuellt ange Sidvisning namn med den `trackPageView` API åsidosätter automatiskt insamlade namn, så att du kan skicka mer allmänna namn i telemetri utan att ändra rubriker.   

Om din app skickar för många anpassade händelsenamn, ändrar du namnet i koden ska vara mindre specifikt. Igen, Undvik att placera URL: er och andra per sida och dynamisk information i anpassad händelsenamn direkt. I stället flytta dessa detaljer i anpassade egenskaper på den anpassade händelsen med den `trackEvent` API. Till exempel i stället för `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, föreslår vi att något som liknar `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Nästa steg

* [Översikt över användarens beteende analytics-verktyg](usage-overview.md)

## <a name="get-help"></a>Få hjälp
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)

