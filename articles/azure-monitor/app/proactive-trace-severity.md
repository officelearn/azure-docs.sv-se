---
title: Nedbrytning i spår allvarlighetsgrad - Azure Application Insights
description: Övervaka programspårningar med Azure Application Insights för ovanliga mönster i spårningstelemetri med smart identifiering .
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 30bdd30ac9c49bb79a3c48bae8149ec761756dd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671689"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Nedbrytning i spår allvarlighetsgrad (förhandsgranskning)

Spår används ofta i applikationer, eftersom de hjälper till att berätta historien om vad som händer bakom kulisserna. När saker går fel, spår ger avgörande insyn i händelseförloppet som leder till oönskade tillstånd. Medan spår är i allmänhet ostrukturerade, det finns en sak som konkret kan läras av dem - deras svårighetsgrad. I ett programs steady state förväntar vi oss att förhållandet mellan "bra" spår (*Info* och *Utförlig*) och "dåliga" spår (*Varning,* *Fel*och *Kritisk*) förblir stabilt. Antagandet är att "dåliga" spår kan ske regelbundet i viss utsträckning på grund av ett antal skäl (tillfälliga nätverksproblem till exempel). Men när ett verkligt problem börjar växa, manifesterar det oftast som en ökning av den relativa andelen "dåliga" spår vs "bra" spår. Application Insights Smart Detection analyserar automatiskt de spårningar som loggas av ditt program och kan varna dig om ovanliga mönster i svårighetsgraden av din spårningstelemetri.

Den här funktionen kräver ingen särskild konfiguration, förutom att konfigurera spårningsloggning för din app (se hur du konfigurerar en spårningsloggning för [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) eller [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Den är aktiv när appen genererar tillräckligt med undantagstelemetri.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När skulle jag få den här typen av meddelande om smart identifiering?
Du kan få den här typen av meddelande om förhållandet mellan "bra" spår (spår som loggats med en nivå av *info* eller *utförlig*) och "dåliga" spår (spår som loggats med en nivå av *varning,* *fel*eller *dödlig*) är förnedrande under en viss dag, jämfört med en baslinje som beräknats under de senaste sju dagarna.

## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt ett problem?
Nej, ett meddelande betyder inte att din app definitivt har ett problem. Även om en nedbrytning i förhållandet mellan "bra" och "dåliga" spår kan tyda på ett programproblem, kan den här ändringen i förhållandet vara godartad. Ökningen kan till exempel bero på ett nytt flöde i programmet som avger fler "dåliga" spår än befintliga flöden).

## <a name="how-do-i-fix-it"></a>Vad kan jag göra?
Meddelandena innehåller diagnostikinformation som stöd i diagnostikprocessen:
1. **Triage.** Meddelandet visar hur många åtgärder som påverkas. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattning.** Påverkar problemet all trafik, eller bara någon operation? Denna information kan erhållas från anmälan.
3. **Diagnostisera.** Du kan använda relaterade objekt och rapporter som länkar till stödjande information för att ytterligare diagnostisera problemet.


