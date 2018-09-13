---
title: Smart identifiering – potentiell minnesläcka som identifieras av Azure Application Insights | Microsoft Docs
description: Övervaka program med Azure Application Insights för potentiella minnesläckor.
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
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 25d26db3cd11fff7f7e9ba472247a920ecddea33
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649671"
---
# <a name="memory-leak-detection-preview"></a>Identifiering av minnesläcka (förhandsversion)

Application Insights automatiskt analyserar minnesanvändningen för varje process i ditt program och kan varna dig om potentiella minnesläckor eller ökad minnesförbrukning.

Den här funktionen kräver några särskilda inställningar än [konfigurerar prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) för din app. Den är aktiv när din app genererar tillräckligt med minne räknare prestandatelemetri (till exempel privata byte).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Ett typiskt meddelande följer en konsekvent ökning minnesförbrukning under en längre tid, i en eller flera processer och/eller en eller flera datorer som ingår i ditt program. Machine learning-algoritmer som används för att upptäcka ökad minnesförbrukning som matchar mönstret för en minnesläcka.

## <a name="does-my-app-really-have-a-problem"></a>Min app verkligen finns det ett problem?
Nej, ett meddelande innebär inte att din app definitivt finns ett fel. Även om mönster för minnesläckor indikerar vanligtvis ett problem med programmet, dessa mönster kan vara vanligt att din specifika process, eller kan ha en naturlig affärsjustering och kan ignoreras.

## <a name="how-do-i-fix-it"></a>Hur jag för att åtgärda det?
Aviseringarna inkluderar diagnostisk information som stöd i diagnostiska analysprocessen:
1. **Prioritering.** Meddelandet visas du mängden minne ökar (i GB) och där minnet som har ökat tidsintervallet. Detta kan du tilldela en prioritet till problemet.
2. **Omfattningen.** Hur många datorer visas mönstret minnesläcka? Hur många undantag utlöstes vid potentiell minnesläcka? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller minnesläcka mönster som visar minnesförbrukning på processen med tiden. Du kan också använda de relaterade objekt och rapporter som länkar till kompletterande information som hjälper dig att ytterligare diagnostisera problemet.
