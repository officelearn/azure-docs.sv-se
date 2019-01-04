---
title: Smart identifiering – onormal ökning av undantagsvolym i Azure Application Insights | Microsoft Docs
description: Övervaka undantagsfel med Azure Application Insights för onormala mönster i volym för undantag.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: a6e7e8e01ccb623a3ff340c318c9c238c919cb38
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028903"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Onormal uppgång av undantagsvolym (förhandsversion)

Application Insights automatiskt analyserar undantag i ditt program och kan varna dig om onormala mönster i telemetrin undantag.

Den här funktionen kräver några särskilda inställningar än [konfigurera undantag rapporter](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) för din app. Den är aktiv när din app genererar mycket undantagstelemetri.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Du kan få den här typen av meddelande om din app visar en onormal ökning av antalet undantag av en viss typ under en dag, jämfört med grundregel beräknad under de senaste sju dagarna.
Machine learning-algoritmer som används för att upptäcka ökning av antal undantag, samtidigt med hänsyn till en naturlig tillväxt i ditt program som körs på.

## <a name="does-my-app-definitely-have-a-problem"></a>Min app definitivt finns det ett problem?
Nej, ett meddelande innebär inte att din app definitivt finns ett fel. Även om ett orimligt antal undantag tyder vanligtvis på ett problem med programmet, kanske de här undantagen ofarliga och hanteras korrekt av programmet.

## <a name="how-do-i-fix-it"></a>Hur jag för att åtgärda det?
Aviseringarna inkluderar diagnostisk information som stöd i diagnostikprocessen för:
1. **Prioritering.** Aviseringen visar hur många användare eller hur många begäranden som påverkas. Detta kan du tilldela en prioritet till problemet.
2. **Omfattningen.** Är problemet påverkar all trafik, eller enbart på vissa åtgärden? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller information om den metod som undantaget uppstod samt vilken undantagstyp. Du kan också använda de relaterade objekt och rapporter som länkar till kompletterande information som hjälper dig att ytterligare diagnostisera problemet.