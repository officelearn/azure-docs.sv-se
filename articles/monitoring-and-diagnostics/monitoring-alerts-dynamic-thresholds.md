---
title: Skapa aviseringar med dynamiska tröskelvärden i Azure-Monitor | Microsoft Docs
description: Skapa aviseringar med maskininlärningsbaserade dynamiska tröskelvärden
author: antonfrMSFT
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: antonfr;mbullwin
ms.openlocfilehash: c847052134b1d83cd606e0e2b51b63b580f7917c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Aviseringar med dynamiska tröskelvärden i Azure-Monitor (begränsad förhandsversion)

Aviseringar med dynamiska tröskelvärden är en förbättring av Azure mått aviseringar i Azure-Monitor utnyttja avancerade funktioner i Machine Learning (ML) Läs mått historiska beteende för att automatiskt beräkna baslinjer och använda dem som tröskelvärden.

Fördelarna med att använda dynamiska tröskelvärden är:

- Spara besväret som är associerade med att ange en fördefinierad hård gräns som övervakaren automatiskt lär sig historiska prestanda för måttet och tillämpar ML algoritmer för att fastställa tröskelvärden.
- De kan identifiera säsongsbaserade beteende och avisering bara på avvikelser från den förväntade när funktionen. Mått aviseringar med dynamiska tröskelvärden utlöser inte om tjänsten är inaktiv regelbundet på helgerna och sedan ger spikar varje måndag i diagrammet. Stöds för närvarande: timvis dagliga och veckovisa säsongsvärdet.
- Kontinuerligt lär sig mått prestanda och är anpassningsbar mått ändringar.

Dynamiska tröskelvärdesbaserad aviseringar är tillgängliga för alla Azure-monitor-baserad mått källor som anges i detta [artikel](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Registrera dig för att få åtkomst till förhandsgranskningen

Göra den här funktionen för en rotationsrutan [registrera dig för förhandsversionen](http://aka.ms/DynamicThresholdMetricAlerts). Som alltid, vi vill gärna höra dina synpunkter, se till att den kommer på [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Så här konfigurerar du aviseringar med dynamiska tröskelvärden

Aviseringar med dynamiska tröskelvärden kan konfigureras via aviseringar i Azure-Monitor

![Aviseringar preview](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Skapa en aviseringsregel med dynamiska tröskelvärden

1. I fönstret aviseringar under övervakaren väljer du den **ny Aviseringsregel** för att skapa en ny avisering i Azure.

   ![Ny aviseringsregel](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. Avsnittet Skapa regeln visas med som består av tre delar: _definiera aviseringstillståndet_, _definiera aviseringsinformation_, och _definiera grupp_. Startar med den _definiera aviseringstillståndet_ avsnittet används den **Välj mål** länken för att ange mål, genom att välja en resurs. Klicka på klart när en lämplig resurs väljs.

   ![Välj mål](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. Därefter använda den **lägga till villkor** för att visa en lista över signal alternativen för resursen och från listan över signal välja en lämplig **mått** alternativet. (Till exempel procent CPU.)

   ![Lägg till villkor](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. På skärmen Konfigurera signal logik har möjlighet att växla villkoret till en typ av dynamisk som automatiskt genererar dynamiska tröskelvärden (röda linjer) tillsammans med mått (blå linje) i avsnittet avisering logik.

   ![Dynamisk](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. De tröskelvärden som visas i diagrammet beräknas baserat på de senaste sju dagarna av historiska data när en avisering skapas dynamiskt trösklar skaffar ytterligare historiska data som är tillgängliga och kontinuerligt lära dig baserat på nya data att göra det tröskelvärden mer exakt.

6. Ytterligare avisering logik inställningar:
   - Villkor - du kan välja att aviseringen ska utlösas på något av följande tre villkor:
       - Större än det övre tröskelvärdet eller lägre än det lägre tröskelvärdet (standard)
       - Större än det övre tröskelvärdet
       - Lägre än det lägre tröskelvärdet.
   - Tid aggregering: genomsnittlig (standard), sum, min, max.
   - Aviseringen känslighet:
       - Hög – fler aviseringar som ska utlösa aviseringen på minsta avvikelse.
       - Med – mindre känslig än hög färre aviseringar än med hög känslighet (standard)
       - Låg – minst känsliga tröskelvärdet.

    ![Logik aviseringsinställningar](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. Utvärderas baserat på:
    -  Vilka varaktighet, aviseringen ska söka efter de angivna villkoren genom att välja den **Period**.

    ![Utvärderas baserat på](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Period värden som stöds: 5 minuter, 10 minuter, 30 minuter och 1 timme.

   Om du vill minska avisering bruset som genererats av tillfälliga toppar, bör du använda inställningarna för ”antal överträdelser ska utlösa aviseringen”. Den här funktionen kan du få en avisering om tröskelvärdet har överskridits X gånger i följd eller Y tider utanför den sista Z punkter. Exempel:

    För att utlösa en avisering när problemet kontinuerlig för 15 minuter, 3 gånger i följd under en viss period på 5 minuter, använder du följande inställningar:

   ![Utvärderas baserat på](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    För att utlösa en avisering när en överträdelse från en dynamisk tröskelvärdet 15 minuter från de senaste 30 minuterna med 5 minuter, använder du följande inställningar:

   ![Utvärderas baserat på](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. För närvarande kan användare ha aviseringar med dynamiska tröskelvärdet kriterier som en enda villkor.

   ![Skapa regel](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Frågor och svar

- F: om måttet långsamt ändras med tiden kommer detta att utlösa en avisering med dynamiska tröskelvärden?

- S: förmodligen inte. Dynamisk tröskelvärden är bra för identifiering av betydande avvikelser i stället för långsamt utvecklingen av problem.

- F: kan jag konfigurera dynamisk tröskelvärden via ett API?

- S: Vi arbetar på den.
