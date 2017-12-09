---
title: "Identifiering - försämring i spårningen allvarlighetsgrad förhållandet i Azure Application Insights för smartkort | Microsoft Docs"
description: "Övervaka programspårningar med Azure Application Insights för ovanliga mönster i spårningstelemetri."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 29ed195dadb7230df425d6d981a0a482e09ee79f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Försämring i spårningen allvarlighetsgrad förhållandet (förhandsgranskning)

Spår används ofta i program som de kan berätta för vad som händer i bakgrunden. Om något går fel, ange spårningar avgörande insyn i sekvens av händelser som leder till ett oönskat tillstånd. Spår är vanligen Ostrukturerade, finns men det en sak som concretely kan hämtas från dem – deras allvarlighetsgrad. I ett program stabilt tillstånd vi förväntar förhållandet mellan ”fungerande” spår (*Info* och *utförlig*) och ”felaktig” spårningar (*varning*, *fel* och *kritisk*) förblir stabil. Förutsättningen är att ”felaktig” spårningar kan inträffa regelbundet till viss mån på grund av en mängd skäl (tillfälligt nätverk utfärdar exempelvis). Men när verkliga problem börjar växande visar det vanligtvis som en relativ andelen ”felaktig” spårningar vs ”fungerande” spår. Identifiering av program insikter Smart automatiskt analyserar spårningar som loggats av ditt program och varna dig om ovanliga mönster i din spårningstelemetri allvarlighetsgrad.

Den här funktionen kräver några särskilda inställningar än konfigurera spårningsloggning för din app (se hur du konfigurerar en trace log-lyssnare för [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) eller [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Det är aktivt när din app genererar tillräckligt med undantagstelemetri om.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Du kan hämta den här typen av meddelande om förhållandet mellan ”fungerande” spår (spår som loggas med en nivå av *Info* eller *utförlig*) och ”felaktig” spårningar (spår som loggas med en nivå av *varning*, * Fel, eller *allvarligt fel*) är att försämras under en viss dag, jämfört med en baslinje för beräknad under de senaste sju dagarna.

## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt problem?
Nej, ett meddelande innebär inte att appen verkligen har problem. Även om en försämring i förhållandet mellan ”fungerande” och ”felaktig” spårningar kan tyda på ett problem med programmet kan ändringen i förhållande vara skadliga. Till exempel kanske ökningen på grund av ett nytt flöde i program som sänder mer ”felaktig” spårningar än befintliga flöden).

## <a name="how-do-i-fix-it"></a>Hur kan jag göra?
Aviseringarna inkluderar diagnostisk information som stöd i processen för diagnostik:
1. **Prioritering.** Meddelandet visas hur många åtgärder som påverkas. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattningen.** Problemet som påverkar all trafik, eller bara vissa åtgärden Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Du kan använda de relaterade objekt och rapporter länka till extra information som hjälper dig att ytterligare felsökning.


