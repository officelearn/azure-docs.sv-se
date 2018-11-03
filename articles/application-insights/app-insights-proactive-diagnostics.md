---
title: Smart identifiering i Azure Application Insights | Microsoft Docs
description: Application Insights utför automatisk djupanalys av apptelemetrin och varnar dig om potentiella problem.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: 0083c157eab489943f94ed1453c66a5c8d2f291a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50960318"
---
# <a name="smart-detection-in-application-insights"></a>Smart identifiering i Application Insights
 Smart identifiering varnar automatiskt dig om potentiella prestandaproblem i ditt webbprogram. Den utför proaktiv analys av telemetrin som appen skickar till [Application Insights](app-insights-overview.md). Om det finns en plötslig i Felfrekvens eller onormala mönster i klient- eller prestanda, får du en avisering. Ingen konfiguration krävs för den här funktionen. Det fungerar om ditt program skickar så mycket telemetri.

Du kan komma åt Smart identifiering aviseringar både från e-postmeddelanden som du får och från bladet Smart identifiering.

## <a name="review-your-smart-detections"></a>Granska dina smarta identifieringar
Du kan identifiera identifieringar på två sätt:

* **Du får ett e-postmeddelande** från Application Insights. Här är ett typexempel:
  
    ![E-postavisering](./media/app-insights-proactive-diagnostics/03.png)
  
    Klicka på en stor knapp för att öppna detalj i portalen.
* **Smart identifiering panelen** på appens översikt bladet visar antalet nya varningar. Klicka på panelen om du vill se en lista över de senaste aviseringarna.

![Visa senaste identifieringar](./media/app-insights-proactive-diagnostics/04.png)

Välj en avisering om du vill visa dess information.

## <a name="what-problems-are-detected"></a>Vilka problem har identifierats?
Det finns tre typer av identifiering:

* [Smart identifiering – Felavvikelser](app-insights-proactive-failure-diagnostics.md). Vi använder maskininlärning att ange förväntade frekvensen för misslyckade begäranden för din app, korrelera med belastning och andra faktorer. Om misslyckade går utanför det förväntade kuvertet kan skicka vi en avisering.
* [Smart identifiering – Prestandaavvikelser](app-insights-proactive-performance-diagnostics.md). Du får meddelanden om svarstid för en åtgärd eller beroende varaktighet långsammare jämfört med historisk baslinje eller om vi identifierar ett avvikande mönster i svarstid eller sidinläsningstiden.   
* [Smart identifiering – Azure Cloud Service problem](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Du får aviseringar om din app finns i Azure Cloud Services och en rollinstans har startfel, vanligt återkommande återanvändning eller runtime-krascher.

(Hjälplänkar i varje meddelande tar dig till artiklarna.)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
Dessa diagnostiska verktyg hjälper dig att inspektera telemetrin från din app:

* [Metric explorer](app-insights-metrics-explorer.md)
* [Sökutforskaren](app-insights-diagnostic-search.md)
* [Analytics – kraftfullt frågespråk](../log-analytics/query-language/get-started-analytics-portal.md)

Smart identifiering är helt automatisk. Men kanske du vill ställa in några fler aviseringar?

* [Manuellt konfigurerade måttaviseringar](app-insights-alerts.md)
* [Webbtester för tillgänglighet](app-insights-monitor-web-app-availability.md) 

