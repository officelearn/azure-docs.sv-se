---
title: "Identifiering - lågt utnyttjande av molnresurser som identifieras av Azure Application Insights för smartkort | Microsoft Docs"
description: "Övervaka program med Azure Application Insights för lågt utnyttjande av molnresurser."
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
ms.openlocfilehash: 8382f6047ae222a01cc0e8d6ca9dcf5593d0dff6
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="low-utilization-of-cloud-resources-preview"></a>Lågt utnyttjande av molnresurser (förhandsgranskning)

Application Insights automatiskt analyserar processorförbrukningen för varje instans av serverroll i ditt program och identifierar instanser med låg CPU-användning. Denna identifiering kan du minska dina Azure-resurser och minska kostnaderna, genom att minska antalet rollinstanser som använder varje roll eller genom att minska antalet roller.

Den här funktionen kräver några särskilda inställningar än [konfigurera prestandaräknare](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) för din app. Det är aktivt när din app genererar tillräckligt med CPU prestandaräknaren telemetri (% processortid).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
En typisk avisering inträffar när många av dina Web/Worker rollinstanser uppvisar låg CPU-användning.

## <a name="does-my-app-definitely-consume-too-many-resources"></a>Använda min app definitivt för många resurser?
Nej, ett meddelande innebär inte att din app definitivt förbrukar för mycket resurser. Även om dessa mönster av låg CPU-belastning indikerar vanligtvis att resursanvändningen kan minskas, detta kan vara vanliga din specifika roll eller kan ha en naturlig affärsjustering och kan ignoreras. Det kan till exempel vara att flera instanser krävs för vissa resurser, till exempel minne/nätverks- och inte CPU.

## <a name="how-do-i-fix-it"></a>Hur kan jag göra?
Aviseringarna inkluderar diagnostisk information som stöd i processen för diagnostik:
1. **Prioritering.** Meddelandet visas rollerna i din app som uppvisar låg CPU-användning. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattningen.** Hur många roller begäras låg CPU-användning och hur många instanser i varje roll utnyttja låg CPU? Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller procentandelen av Processorn som används, visar processoranvändningen för varje instans över tid. Du kan också använda de relaterade objekt och rapporter länka till extra information, till exempel percentiler av processoranvändningen, som hjälper dig att diagnosticera problemet.
