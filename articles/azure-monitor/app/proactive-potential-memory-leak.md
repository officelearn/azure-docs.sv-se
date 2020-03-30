---
title: Identifiera minnesläcka - Smart identifiering av Azure Application Insights
description: Övervaka program med Azure Application Insights för potentiella minnesläckor.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 85d138518dfb1313a810657016e9fe3143887b6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671706"
---
# <a name="memory-leak-detection-preview"></a>Identifiering av minnesläckage (förhandsgranskning)

Application Insights analyserar automatiskt minnesförbrukningen för varje process i ditt program och kan varna dig om potentiella minnesläckor eller ökad minnesförbrukning.

Den här funktionen kräver ingen särskild konfiguration, förutom [att konfigurera prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) för din app. Den är aktiv när appen genererar tillräckligt med minnesprestandaräknare telemetri (till exempel Privata byte).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När skulle jag få den här typen av meddelande om smart identifiering?
Ett typiskt meddelande kommer att följa en konsekvent ökning av minnesförbrukningen under en lång tidsperiod, i en eller flera processer och/eller en eller flera datorer, som ingår i ditt program. Machine learning-algoritmer används för att identifiera ökad minnesförbrukning som matchar mönstret för en minnesläcka.

## <a name="does-my-app-really-have-a-problem"></a>Har min app verkligen ett problem?
Nej, ett meddelande betyder inte att din app definitivt har ett problem. Även om minnesläcka mönster vanligtvis indikerar ett programproblem, kan dessa mönster vara typiska för din specifika process, eller kan ha en naturlig affärsmotivering och kan ignoreras.

## <a name="how-do-i-fix-it"></a>Vad kan jag göra?
Meddelandena innehåller diagnostikinformation som stöd i diagnostikanalysprocessen:
1. **Triage.** Meddelandet visar hur mycket minne ökar (i GB) och det tidsintervall som minnet har ökat i. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattning.** Hur många maskiner uppvisade minnesläckmönstret? Hur många undantag utlöstes under den potentiella minnesläckan? Denna information kan erhållas från anmälan.
3. **Diagnostisera.** Identifieringen innehåller minnesläckmönstret, som visar minnesförbrukning av processen över tiden. Du kan också använda relaterade objekt och rapporter som länkar till stödjande information för att ytterligare diagnostisera problemet.
