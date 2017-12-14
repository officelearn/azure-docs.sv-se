---
title: "Identifiering - onormal ökning av undantag volymen i Azure Application Insights för smartkort | Microsoft Docs"
description: "Övervaka programmet undantag med Azure Application Insights för ovanliga mönster i undantag volym."
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
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: 8030f3331a03170bb265c417a57725544bdc7d3f
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2017
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Onormal ökning av undantag volym (förhandsgranskning)

Application Insights automatiskt analyserar undantag i ditt program och varna dig om ovanliga mönster i din undantagstelemetri.

Den här funktionen kräver några särskilda inställningar än [konfigurerar undantag reporting](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) för din app. Det är aktivt när din app genererar tillräckligt med undantagstelemetri om.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>När ska jag den här typen av meddelande för smart identifiering?
Du kan hämta den här typen av meddelande om din app uppvisar en onormal ökning i antalet undantag för en specifik typ under en dag, jämfört med en baslinje för beräknad under de senaste sju dagarna.
Maskininlärningsalgoritmer som används för att upptäcka ökning av antalet undantag, medan med hänsyn till en naturlig tillväxt i din användning av programmet.

## <a name="does-my-app-definitely-have-a-problem"></a>Har min app definitivt problem?
Nej, ett meddelande innebär inte att appen verkligen har problem. Även om ett orimligt antal undantag indikerar vanligtvis ett problem med programmet, kanske undantagen ofarlig och hanterade korrekt av programmet.

## <a name="how-do-i-fix-it"></a>Hur kan jag göra?
Aviseringarna inkluderar diagnostisk information som stöd i processen för diagnostik:
1. **Prioritering.** Meddelandet visas hur många användare eller hur många förfrågningar som påverkas. Detta kan hjälpa dig att tilldela en prioritet till problemet.
2. **Omfattningen.** Problemet som påverkar all trafik, eller bara vissa åtgärden Den här informationen kan hämtas från meddelandet.
3. **Diagnostisera.** Identifieringen innehåller information om metoden som undantaget uppstod samt undantagstyp. Du kan också använda de relaterade objekt och rapporter länka till extra information som hjälper dig att ytterligare felsökning.