---
title: Felsöka verktyg för användaranalys – Azure Application Insights
description: Felsökningsguide - analysera webbplats- och appanvändning med Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 8d2e573f34895207a455838b5fc64f95560943d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670924"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Felsöka analysverktyg för användarbeteende i Application Insights
Har du frågor om [analysverktygen för användarbeteende i Programstatistik:](usage-overview.md) [Användare, Sessioner, Händelser,](usage-segmentation.md) [Trattar,](usage-funnels.md) [Användarflöden,](usage-flows.md) [Kvarhållning](usage-retention.md)eller Kohorter? Här är några svar.

## <a name="counting-users"></a>Räkna användare
**Analysverktygen för användarbeteende visar att min app har en användare/session, men jag vet att min app har många användare/sessioner. Hur kan jag åtgärda dessa felaktiga antal?**

Alla telemetrihändelser i Application Insights har ett [anonymt användar-ID](../../azure-monitor/app/data-model-context.md) och ett [sessions-ID](../../azure-monitor/app/data-model-context.md) som två av sina standardegenskaper. Som standard räknar alla användningsanalysverktyg användare och sessioner baserat på dessa ID:er. Om dessa standardegenskaper inte fylls med unika ID:er för varje användare och session i appen visas ett felaktigt antal användare och sessioner i användningsanalysverktygen.

Om du övervakar en webbapp är den enklaste lösningen att lägga till [Application Insights JavaScript SDK](../../azure-monitor/app/javascript.md) i appen och se till att skriptkodavsnittet läses in på varje sida som du vill övervaka. JavaScript SDK genererar automatiskt anonyma användar- och sessions-ID:n och fyller sedan telemetrihändelser med dessa ID:n när de skickas från din app.

Om du övervakar en webbtjänst (inget användargränssnitt) [skapar du en telemetriinitierare som fyller i de anonyma användar-ID- och sessions-ID-egenskaperna](usage-send-user-context.md) enligt tjänstens föreställningar om unika användare och sessioner.

Om din app skickar [autentiserade användar-ID:er](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users)kan du räkna baserat på autentiserade användar-ID:er i verktyget Användare. Välj "Autentiserade användare" i listrutan Visa.

Verktygen för analys av användarbeteende stöder för närvarande inte att räkna användare eller sessioner baserat på andra egenskaper än anonymt användar-ID, autentiserat användar-ID eller sessions-ID.

## <a name="naming-events"></a>Namnge händelser
**Min app har tusentals olika sidvy och anpassade händelsenamn. Det är svårt att skilja mellan dem, och användarbeteendeanalysverktygen svarar ofta inte. Hur kan jag åtgärda dessa namngivningsproblem?**

Sidvisning och anpassade händelsenamn används i hela verktygen för analys av användarbeteenden. Namnge händelser väl är avgörande för att få värde från dessa verktyg. Målet är en balans mellan att ha för få, alltför generiska namn ("Knapp klickade") och att\/ha för många, alltför specifika namn ("Redigera-knappen klickade på http: /www.contoso.com/index").

Om du vill göra ändringar i sidvyn och anpassade händelsenamn som appen skickar måste du ändra appens källkod och distribuera om. **Alla telemetridata i Application Insights lagras i 90 dagar och kan inte tas bort,** så ändringar du gör i händelsenamn tar 90 dagar att manifestera helt. Under 90 dagar efter att du har gjort namnändringar visas både gamla och nya händelsenamn i din telemetri, så justera frågor och kommunicera inom dina team, i enlighet därmed.

Om appen skickar för många sidvisningsnamn kontrollerar du om dessa sidvisningsnamn anges manuellt i kod eller om de skickas automatiskt av Programstatistiken JavaScript SDK:

* Om sidvisningsnamnen anges manuellt i kod med [ `trackPageView` api:et](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)ändrar du namnet så att det är mindre specifikt. Undvik vanliga misstag som att placera webbadressen i sidvyns namn. Använd i stället URL-parametern för API:et. `trackPageView` Flytta annan information från sidvynamnet till anpassade egenskaper.

* Om Programstatistiken JavaScript SDK automatiskt skickar sidvisningsnamn kan du antingen ändra sidornas rubriker eller växla till att manuellt skicka sidvisningsnamn. SDK skickar [som](https://developer.mozilla.org/docs/Web/HTML/Element/title) standard rubriken på varje sida som sidvisningsnamn. Du kan ändra dina titlar för att vara mer allmän, men vara uppmärksam på SEO och andra effekter denna förändring kan ha. Manuellt ange sidvisningsnamn `trackPageView` med API åsidosätter automatiskt insamlade namn, så att du kan skicka fler allmänna namn i telemetri utan att ändra sidrubriker.   

Om appen skickar för många anpassade händelsenamn ändrar du namnet i koden så att det blir mindre specifikt. Undvik återigen att placera webbadresser och annan information per sida eller dynamisk information i de anpassade händelsenamnen direkt. Flytta i stället dessa information till anpassade `trackEvent` egenskaper för den anpassade händelsen med API:et. Till exempel, `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`i stället för `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`, föreslår vi något i stil med .

## <a name="next-steps"></a>Nästa steg

* [Översikt över analysverktyg för användare](usage-overview.md)

## <a name="get-help"></a>Få hjälp
* [Stackspill](https://stackoverflow.com/questions/tagged/ms-application-insights)

