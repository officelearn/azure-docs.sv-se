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
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mbullwin
ms.openlocfilehash: 8ee2dea364253d871d5624242d15d8a81ab6f08f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465900"
---
# <a name="smart-detection-in-application-insights"></a>Smart identifiering i Application Insights
 Smart identifiering varnar automatiskt dig om potentiella prestandaproblem och avvikelser i fel i ditt webbprogram. Den utför proaktiv analys av telemetrin som appen skickar till [Application Insights](../../azure-monitor/app/app-insights-overview.md). Om det finns en plötslig i Felfrekvens eller onormala mönster i klient- eller prestanda, får du en avisering. Ingen konfiguration krävs för den här funktionen. Det fungerar om ditt program skickar så mycket telemetri.

Du kan komma åt identifieringar som utfärdats av Smart identifiering både från e-postmeddelanden som du får och från bladet Smart identifiering.

## <a name="review-your-smart-detections"></a>Granska dina smarta identifieringar
Du kan identifiera identifieringar på två sätt:

* **Du får ett e-postmeddelande** från Application Insights. Här är ett typexempel:
  
    ![E-postavisering](./media/proactive-diagnostics/03.png)
  
    Klicka på en stor knapp för att öppna detalj i portalen.
* **Bladet Smart identifiering** i Application Insights. Välj **Smart identifiering** under den **Undersök** menyn för att se en lista över de senaste identifieringar.

![Visa senaste identifieringar](./media/proactive-diagnostics/04.png)

Välj en identifiering för att visa information om.

## <a name="what-problems-are-detected"></a>Vilka problem har identifierats?
Smart identifiering upptäcker och meddelas om olika problem, till exempel:

* [Smart identifiering – Felavvikelser](../../azure-monitor/app/proactive-failure-diagnostics.md). Vi använder maskininlärning att ange förväntade frekvensen för misslyckade begäranden för din app, korrelera med belastning och andra faktorer. Om misslyckade går utanför det förväntade kuvertet kan skicka vi en avisering.
* [Smart identifiering – Prestandaavvikelser](../../azure-monitor/app/proactive-performance-diagnostics.md). Du får meddelanden om svarstid för en åtgärd eller beroende varaktighet långsammare jämfört med historisk baslinje eller om vi identifierar ett avvikande mönster i svarstid eller sidinläsningstiden.   
* Allmän försämringar och problem, som [spåra nedsatt](https://docs.microsoft.com/azure/azure-monitor/app/proactive-trace-severity), [minnesläcka](https://docs.microsoft.com/azure/azure-monitor/app/proactive-potential-memory-leak), [Abnormal ökad volym för undantag](https://docs.microsoft.com/azure/azure-monitor/app/proactive-exception-volume) och [Security antimönster](https://docs.microsoft.com/azure/azure-monitor/app/proactive-application-security-detection-pack).

(Hjälplänkar i varje meddelande tar dig till artiklarna.)

## <a name="smart-detection-email-notifications"></a>Smart identifiering av e-postaviseringar

Alla regler för Smart identifiering, förutom regler som markerats som _förhandsversion_, konfigureras som standard skickar e-postmeddelanden när identifieringar hittas.

Konfigurera e-postmeddelanden för en viss regel för Smart identifiering kan göras genom att öppna Smart identifiering **inställningar** bladet och välja regeln, vilket öppnar den **Redigera regeln** bladet.

Du kan också ändra konfigurationen med Azure Resource Manager-mallar. [Se regler för smart identifiering hantera Application Insights med hjälp av Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config) för mer information.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Nästa steg
Dessa diagnostiska verktyg hjälper dig att inspektera telemetrin från din app:

* [Metric explorer](../../azure-monitor/app/metrics-explorer.md)
* [Sökutforskaren](../../azure-monitor/app/diagnostic-search.md)
* [Analytics – kraftfullt frågespråk](../../azure-monitor/log-query/get-started-portal.md)

Smart identifiering är helt automatisk. Men kanske du vill ställa in några fler aviseringar?

* [Manuellt konfigurerade måttaviseringar](../../azure-monitor/app/alerts.md)
* [Webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md) 

