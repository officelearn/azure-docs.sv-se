---
title: Onormal ökning av undantagsvolymen - Azure Application Insights
description: Övervaka programundantag med Smart Identifiering i Azure Application Insights för ovanliga mönster i undantagsvolym.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: a08fae4774a8afb9959f55ea3196cd1a45c33439
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671774"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Onormal ökning av undantagsvolymen (förhandsgranskning)

Application Insights analyserar automatiskt de undantag som genereras i ditt program och kan varna dig om ovanliga mönster i din undantagstelemetri.

Den här funktionen kräver ingen särskild konfiguration, förutom [att konfigurera undantagsrapportering](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) för din app. Den är aktiv när appen genererar tillräckligt med undantagstelemetri.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När skulle jag få den här typen av meddelande om smart identifiering?
Du kan få den här typen av avisering om din app uppvisar en onormal ökning av antalet undantag av en viss typ under en dag, jämfört med en originalplan som beräknats under de senaste sju dagarna.
Machine learning-algoritmer används för att identifiera ökningen av antalet undantag, samtidigt som hänsyn tas till en naturlig tillväxt i din programanvändning.

## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt ett problem?
Nej, ett meddelande betyder inte att din app definitivt har ett problem. Även om ett alltför stort antal undantag vanligtvis indikerar ett programproblem, kan dessa undantag vara godartade och hanteras korrekt av ditt program.

## <a name="how-do-i-fix-it"></a>Vad kan jag göra?
Meddelandena innehåller diagnostikinformation som stöd i diagnostikprocessen:
1. **Triage.** Meddelandet visar hur många användare eller hur många begäranden som påverkas. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattning.** Påverkar problemet all trafik, eller bara någon operation? Denna information kan erhållas från anmälan.
3. **Diagnostisera.** Identifieringen innehåller information om metoden som undantaget skapades från, samt undantagstypen. Du kan också använda relaterade objekt och rapporter som länkar till stödjande information för att ytterligare diagnostisera problemet.
