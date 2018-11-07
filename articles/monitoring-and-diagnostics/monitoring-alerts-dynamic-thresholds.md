---
title: Skapa aviseringar med dynamiska tröskelvärden i Azure Monitor
description: Skapa aviseringar med dynamiska tröskelvärden för maskininlärningsbaserade
author: antonfrMSFT
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: mbullwin
ms.reviewer: antonfr
ms.component: alerts
ms.openlocfilehash: 1b47e3804d8be36e3c6c8c570fec06f542e8dbf2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233974"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Aviseringar med dynamiska tröskelvärden i Azure Monitor (begränsad offentlig förhandsversion)

Aviseringar med dynamiska tröskelvärden är en förbättring av Azure mått aviseringar i Azure Monitor kan utnyttja avancerade funktioner för Machine Learning (ML) Läs mått historiska beteende för att automatiskt beräkna baslinjer och använda dem som tröskelvärden.

Fördelarna med att använda dynamiska tröskelvärden är:

- Spara arbetet som är associerade med att ange en fast fördefinierade gräns som övervakaren automatiskt lär sig historiska prestanda för måttet och tillämpar ML-algoritmer för att avgöra tröskelvärdena.
- De kan identifiera säsongsbaserade beteenden och aviseringar endast om avvikelser från förväntat säsongens beteende. Måttaviseringar med dynamiska tröskelvärden utlöser inte om din tjänst är regelbundet inaktiv på helgerna och toppar varje måndag. För närvarande: per timme, dagliga och veckovisa säsongsberoende.
- Lär sig hur mått och är anpassningsbara för metriska ändringar kontinuerligt.

Dynamisk tröskelbaserade aviseringar är tillgängliga för alla Azure monitor-baserad mått källor som anges i detta [artikeln](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Registrera dig för att få åtkomst till förhandsversionen

Kan den här funktionen för en skapa [registrera dig för förhandsversionen av](https://aka.ms/DynamicThresholdMetricAlerts). Som alltid kan vi vill gärna höra dina synpunkter, hålla det kommer på [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Konfigurera aviseringar med dynamiska tröskelvärden

Aviseringar med dynamiska tröskelvärden kan konfigureras via aviseringar i Azure Monitor

![Förhandsversion av aviseringar](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Skapa en aviseringsregel med dynamiska tröskelvärden

1. Fönstret aviseringar under Övervakare, Välj den **ny Aviseringsregel** för att skapa en ny avisering i Azure.

   ![Ny aviseringsregel](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. Avsnittet Skapa regeln visas med tre delar som består av: _definiera aviseringsvillkoret_, _definiera Aviseringsinformationen_, och _definiera åtgärdsgruppen_. Startar med den _definiera aviseringsvillkoret_ avsnittet används den **Välj mål** länk för att ange målet, genom att välja en resurs. Klicka på klart-knappen när en lämplig resurs väljs.

   ![Välja mål](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. Därefter använder den **lägga till villkor** för att visa en lista över signalen alternativ som är tillgängliga för resursen och signal listan Välj ett lämpligt **mått** alternativet. (Till exempel procent CPU.)

   ![Lägg till villkor](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. På skärmen Konfigurera signalen logic har möjlighet att ändra villkoret till en typ av dynamisk, som automatiskt genererar de dynamiska tröskelvärdena (röda linjer) tillsammans med måttet (blå linje) i avsnittet Alert logic.

   ![Dynamisk](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. De tröskelvärden som visas i diagrammet beräknas baserat på de senaste sju dagarna av historiska data när en avisering har skapats kan de dynamiska tröskelvärdena skaffar ytterligare historiska data som är tillgängliga och kontinuerligt lära dig baserat på nya data att göra det tröskelvärden mer exakta.

6. Ytterligare Alert logic-inställningar:
   - Villkor - du kan välja aviseringen ska utlösas på någon av följande tre villkor:
       - Större än det övre tröskelvärdet eller lägre än det nedre tröskelvärdet (standard)
       - Större än det högsta tröskelvärdet
       - Lägre än det nedre tröskelvärdet.
   - Tidsmängd: genomsnittlig (standard), sum, min, max.
   - Aviseringskänslighet:
       - Hög – fler aviseringar som ska utlösa aviseringen på minsta avvikelse.
       - Med – mindre känslig än hög, färre aviseringar än med hög känslighet (standard)
       - Med låg minst känsliga tröskelvärdet.

    ![Aviseringslogik inställningar](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. Utvärderas baserat på:
    -  Vilka varaktighet, aviseringen bör se ut för det angivna villkoret genom att välja den **Period**.

    ![Utvärderas baserat på](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Period värden som stöds: 5 minuter, 10 minuter, 30 minuter och 1 timme.

   För att minska onödig avisering som genererats av övergående spikar, bör du använda inställningarna för ”antal överträdelser för att utlösa aviseringen”. Den här funktionen kan du få en avisering om tröskelvärdet har överskridits X gånger i följd eller Y-tider utanför den sista Z punkter. Exempel:

    Om du vill utlösa en avisering när problemet är kontinuerlig för 15 minuter, 3 på varandra följande gånger inom en viss period på 5 minuter, använder du följande inställningar:

   ![Utvärderas baserat på](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    Om du vill utlösa en avisering när det har en överträdelse från en dynamiskt tröskelvärde i 15 minuter från de senaste 30 minuterna med period på fem minuter, använder du följande inställningar:

   ![Utvärderas baserat på](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. Användare kan för närvarande har aviseringar med villkor för dynamiskt tröskelvärde som enskilt villkor.

   ![Skapa regel](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Frågor och svar

- F: om måttet ändras långsamt över tid, kommer detta att utlösa en avisering med dynamiska tröskelvärden?

- S: förmodligen Nej. Dynamiska tröskelvärden är bra för identifiering av betydande avvikelser i stället för att långsamt utvecklas problem.

- F: kan jag konfigurera dynamiska tröskelvärden via ett API?

- S: Vi arbetar på den.
