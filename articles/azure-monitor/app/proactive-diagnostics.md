---
title: Smart identifiering i Azure Application Insights | Microsoft-dokument
description: Application Insights utför automatisk djupanalys av din apptelemetri och varnar dig för potentiella problem.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: ff9f88e1d2e643d04c4417283420217e7d496caf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536802"
---
# <a name="smart-detection-in-application-insights"></a>Smart identifiering i Application Insights
 Smart identifiering varnar dig automatiskt för potentiella prestandaproblem och felavvikelser i webbprogrammet. Den utför proaktiv analys av telemetri som din app skickar till [Application Insights](../../azure-monitor/app/app-insights-overview.md). Om felfrekvensen plötsligt ökar eller onormala mönster i klient- eller serverprestanda får du en avisering. Den här funktionen behöver ingen konfiguration. Den fungerar om ditt program skickar tillräckligt med telemetri.

Du kan komma åt de identifieringar som utfärdats av Smart Detection både från de e-postmeddelanden du får och från bladet Smart Detection.

## <a name="review-your-smart-detections"></a>Granska dina smarta identifieringar
Du kan identifiera identifieringar på två sätt:

* **Du får ett e-postmeddelande** från Application Insights. Här är ett vanligt exempel:
  
    ![E-postavisering](./media/proactive-diagnostics/03.png)
  
    Klicka på den stora knappen för att öppna mer information i portalen.
* **Bladet Smart Detection** i Application Insights. Välj **Smart identifiering** under menyn **Undersök** om du vill visa en lista över de senaste identifieringarna.

![Visa de senaste identifieringarna](./media/proactive-diagnostics/04.png)

Välj en identifiering om du vill visa dess information.

## <a name="what-problems-are-detected"></a>Vilka problem upptäcks?
Smart identifiering identifierar och meddelar om en mängd olika problem, till exempel:

* [Smart identifiering - Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md). Vi använder maskininlärning för att ställa in den förväntade hastigheten för misslyckade begäranden för din app, korrelerar med belastning och andra faktorer. Om felfrekvensen går utanför det förväntade kuvertet skickar vi en avisering.
* [Smart identifiering - Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md). Du får aviseringar om svarstiden för en åtgärd eller beroendevaraktighet saktar ner jämfört med historisk originalplan eller om vi identifierar ett avvikande mönster i svarstiden eller sidans inläsningstid.   
* Allmänna nedbrytningar och problem, som [Spårningsförsämring](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [Minnesläcka,](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak) [Onormal ökning av undantagsvolym](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) och [säkerhetsantimönster](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Hjälplänkarna i varje meddelande tar dig till relevanta artiklar.)

## <a name="smart-detection-email-notifications"></a>E-postmeddelanden om smart identifiering

Alla regler för smart identifiering, förutom regler som markerats som _förhandsgranskning,_ är konfigurerade som standard för att skicka e-postmeddelanden när identifieringar hittas.

Konfigurera e-postmeddelanden för en viss smart identifieringsregel kan göras genom att öppna bladet Smarta **identifieringsinställningar** och välja regeln, som öppnar **regelbladet Redigera.**

Du kan också ändra konfigurationen med Azure Resource Manager-mallar. Mer information finns i [Hantera programstatistiks smarta identifieringsregler med hjälp av Azure Resource Manager-mallar.](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
De här diagnostikverktygen hjälper dig att granska telemetrin från din app:

* [Statistikutforskare](../../azure-monitor/platform/metrics-charts.md)
* [Sök explorer](../../azure-monitor/app/diagnostic-search.md)
* [Analytics - kraftfullt frågespråk](../../azure-monitor/log-query/get-started-portal.md)

Smart Detection är helt automatisk. Men du kanske vill sätta upp fler varningar?

* [Manuellt konfigurerade måttaviseringar](../../azure-monitor/app/alerts.md)
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md) 

