---
title: Smart identifiering – prestandaförsämring i spårningen allvarlighetsgrad förhållande i Azure Application Insights | Microsoft Docs
description: Övervaka programspårningar med Azure Application Insights för onormala mönster i spårningstelemetri.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: 41e972145d2404e46b62521c7d40d997be74a7f9
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648813"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Försämring i spårningen allvarlighetsgrad-förhållande (förhandsversion)

Spårningar används mycket i program, eftersom de hjälper uppgift att berätta vad som händer i bakgrunden. Om något går fel, ange spårningar viktig insyn i sekvens av händelser som leder till oönskade tillstånd. Spårningar är vanligtvis Ostrukturerade, finns men det en sak som concretely kan hämtas från dem – deras allvarlighetsgrad. I ett programs stabilt tillstånd, skulle vi räknar med förhållandet mellan ”good” spår (*Info* och *utförlig*) och ”dåliga” spårningar (*varning*, *fel*, och *kritisk*) är stabil. Antas att ”dåliga” spårningar kan inträffa med jämna mellanrum till viss mån på grund av en mängd skäl (tillfälliga nätverket utfärdar exempelvis). Men när verkliga problem börjar växer, det manifest vanligtvis som en ökning av relativa andelen ”dåliga” spårningar vs ”good” spårningar. Smart identifiering för Application Insights automatiskt analyserar spårningar som loggats av ditt program och kan varna dig om onormala mönster i allvarlighetsgrad av telemetrin spårningen.

Den här funktionen kräver några särskilda inställningar, förutom att konfigurera spårningsloggning för din app (se hur du konfigurerar en trace log-lyssnare för [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) eller [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Den är aktiv när din app genererar mycket undantagstelemetri.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Du kan få den här typen av meddelande om förhållandet mellan ”good” spår (spårningar som loggas med en nivå av *Info* eller *utförlig*) och ”dåliga” spårningar (spårningar som loggas med en nivå av *varning*, * Fel, eller *oåterkalleligt fel*) är att försämras under en viss dag, jämfört med grundregel beräknad under de senaste sju dagarna.

## <a name="does-my-app-definitely-have-a-problem"></a>Min app definitivt finns det ett problem?
Nej, ett meddelande innebär inte att din app definitivt finns ett fel. Även om förhållandet mellan ”good” och ”dåliga” spårningar försämrade kan tyda på ett problem med programmet, kan den här ändringen i förhållande vara ofarliga. Till exempel kanske ökningen på grund av ett nytt flöde i program som sänder mer ”dåliga” spårningar än befintliga flöden).

## <a name="how-do-i-fix-it"></a>Hur jag för att åtgärda det?
Aviseringarna inkluderar diagnostisk information som stöd i diagnostikprocessen för:
1. **Prioritering.** Aviseringen visar hur många åtgärder som påverkas. Detta kan du tilldela en prioritet till problemet.
2. **Omfattningen.** Är problemet påverkar all trafik, eller enbart på vissa åtgärden? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Du kan använda de relaterade objekt och rapporter som länkar till kompletterande information som hjälper dig att ytterligare diagnostisera problemet.


