---
title: "Identifiering - minnesläcka som identifieras av Azure Application Insights för smartkort | Microsoft Docs"
description: "Övervaka program med Azure Application Insights för potentiella minnesläckor."
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
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 452d0a9d0231e54df2a7f1df76c3c2c0fcd94d87
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="memory-leak-detection-preview"></a>Identifiering av minnesläcka (förhandsgranskning)

Application Insights automatiskt analyserar minnesförbrukning för varje process i ditt program och varna dig om potentiella minnesläckor eller ökad minnesförbrukning.

Den här funktionen kräver några särskilda inställningar än [konfigurera prestandaräknare](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) för din app. Det är aktivt när din app genererar tillräckligt med minne prestandaräknare telemetri (till exempel privata byte).


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Ett typiskt meddelande följer en konsekvent ökning minnesförbrukning över en längre tidsperiod några timmar, i en eller flera processer och/eller en eller flera datorer som ingår i ditt program.
Maskininlärningsalgoritmer används för att upptäcka ökad minnesförbrukning som matchar ett mönster för en minnesläcka i stället för ökad minnesförbrukning på grund av naturligt öka programanvändning.

## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt problem?
Nej, ett meddelande innebär inte att appen verkligen har problem. Även om mönster för minnesläckor indikerar vanligtvis ett problem med programmet, dessa mönster kan vara vanligt att en specifik process, eller kan ha en naturlig affärsjustering och kan ignoreras.

## <a name="how-do-i-fix-it"></a>Hur kan jag göra?
Aviseringarna inkluderar diagnostisk information som stöd i diagnostiska analysen:
1. **Prioritering.** Meddelandet visas du mängden minne ökar (i GB) och tidsintervall som minnet har ökat. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattningen.** Hur många datorer visas minnesläcka mönster? Hur många undantag utlöstes under den en minnesläckan? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller minnesläcka mönster visar minnesanvändningen för processen över tid. Du kan också använda de relaterade objekt och rapporter länka till extra information som hjälper dig att ytterligare felsökning.
