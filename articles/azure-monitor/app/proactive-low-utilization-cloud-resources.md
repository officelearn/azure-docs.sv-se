---
title: Smart identifiering – lågt utnyttjande av molnresurser som identifieras av Azure Application Insights | Microsoft Docs
description: Övervaka program med Azure Application Insights för lågt utnyttjande av resurser i molnet.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 7cf72068b9cabceb0c5b535986ac4dfb62151b94
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028834"
---
# <a name="low-cpu-utilization-in-cloud-resources-preview"></a>Låg CPU-belastning i molnresurser (förhandsversion)

Application Insights automatiskt analyserar processorförbrukningen för varje rollinstans i ditt program och identifierar instanser med låg CPU-användning. Den här identifieringen kan du minska dina Azure-resurser och minska kostnaderna, genom att minska antalet rollinstanser som använder sig av varje roll eller genom att minska antalet roller.

Den här funktionen kräver några särskilda inställningar än [konfigurerar prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) för din app. Den är aktiv när din app genererar mycket CPU prestandaräknaren telemetri (% processortid).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
En typisk avisering inträffar när många av dina Web/Worker-rollinstanser uppvisar låg CPU-användning.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Min app definitivt förbrukar för många resurser?
Nej, ett meddelande innebär inte att din app definitivt förbrukar för många resurser. Även om dessa mönster för låg CPU-användning indikerar vanligtvis att resursförbrukning kan minskas, det här beteendet kan vara vanliga för din specifika roll eller kan ha en naturlig affärsjustering och kan ignoreras. Det kan till exempel vara att flera instanser krävs för andra resurser, till exempel minne/nätverks- och inte CPU.

## <a name="how-do-i-fix-it"></a>Hur jag för att åtgärda det?
Aviseringarna inkluderar diagnostisk information som stöd i diagnostikprocessen för:
1. **Prioritering.** Meddelandet visas rollerna i din app som uppvisar låg CPU-användning. Detta kan du tilldela en prioritet till problemet.
2. **Omfattningen.** Hur många roller visas låg CPU-användning och hur många instanser i varje roll använda låg CPU? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller procentandelen av CPU som används, som visar CPU-belastningen för varje instans över tid. Du kan också använda de relaterade objekt och rapporter som länka till extra information, till exempel percentilerna för CPU-användning för att diagnosticera problemet.
