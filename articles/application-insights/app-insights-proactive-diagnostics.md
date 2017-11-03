---
title: "Identifiering i Azure Application Insights för smartkort | Microsoft Docs"
description: "Application Insights utför automatisk djupgående analys av din app telemetri och varnar dig om potentiella problem."
services: application-insights
documentationcenter: windows
author: rakefetj
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: c15db8451cc1c5380bcf4a29c5e722d09c0a603b
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="smart-detection-in-application-insights"></a>Smart identifiering i Application Insights
 Smart identifiering varnar automatiskt dig för eventuella prestandaproblem i ditt webbprogram. Proaktiv analys av telemetri som appen skickar till utförs [Programinsikter](app-insights-overview.md). Om det finns en plötslig ökning av fel eller onormala mönster i klient- eller prestanda, får du en avisering. Den här funktionen krävs ingen konfiguration. Den fungerar om programmet skickar tillräckligt med telemetri.

Du kan komma åt varningar Smart av både från e-postmeddelanden som du tar emot och från bladet Smart identifiering.

## <a name="review-your-smart-detections"></a>Granska din Smart identifieringar
Du kan identifiera identifieringar på två sätt:

* **Du får ett e-postmeddelande** från Application Insights. Här är ett typexempel:
  
    ![E-postavisering](./media/app-insights-proactive-diagnostics/03.png)
  
    Klicka på ett stort för att öppna detalj i portalen.
* **Panelen Smart identifiering** på din app översikt bladet visar antalet nya aviseringar. Klicka på panelen om du vill se en lista över senaste aviseringarna.

![Visa senaste identifieringar](./media/app-insights-proactive-diagnostics/04.png)

Välj en avisering om du vill visa information.

## <a name="what-problems-are-detected"></a>Vilka problem har identifierats?
Det finns tre typer av identifiering:

* [Smartkort identifiering - fel avvikelser](app-insights-proactive-failure-diagnostics.md). Vi använder maskininlärning att ange förväntat antal misslyckade begäranden för din app korrelerar med belastning och andra faktorer. Om fel går utanför det förväntade kuvertet kan skicka vi en avisering.
* [Smartkort identifiering - Prestandaavvikelser](app-insights-proactive-performance-diagnostics.md). Du får meddelanden om svarstid för en åtgärd eller beroende varaktighet långsammare jämfört med tidigare baslinje eller om vi identifiera ett avvikande mönster i svarstid eller sidinläsningstiden.   
* [Smartkort identifiering - problem med Azure Cloud Service](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Du får aviseringar om din app finns i Azure Cloud Services och en rollinstans har startfel, ofta återvinning eller runtime kraschar.

(Hjälplänkar i varje meddelande ta dig till artiklarna.)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
Dessa verktyg för Nätverksdiagnostik hjälpa dig att inspektera telemetri från din app:

* [Mått explorer](app-insights-metrics-explorer.md)
* [Sök explorer](app-insights-diagnostic-search.md)
* [Analytics - frågespråket kraftfulla](app-insights-analytics-tour.md)

Smart identifiering är helt automatisk. Men kanske du vill ställa in några fler aviseringar?

* [Manuellt konfigurerade mått aviseringar](app-insights-alerts.md)
* [Tillgänglighetstester för webbprogram](app-insights-monitor-web-app-availability.md) 

