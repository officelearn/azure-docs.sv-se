---
title: Skapa och dela instrumentpaneler med data från Azure Log Analytics | Microsoft Docs
description: Den här kursen hjälper dig att förstå hur instrumentpanelerna i Log Analytics kan visualisera alla dina sparade loggfrågor, vilket ger dig en enskild lins och visa din miljö.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 93cda8680bc665055d449e86c24d6565f6fc525f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296446"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Skapa och dela instrumentpaneler för Log Analytics

Log Analytics instrumentpaneler kan visualisera alla dina sparade loggfrågor, så att du kan hitta, samordna och dela IT-användningsdata i organisationen.  Den här självstudien visas hur du skapar en loggfråga som används för att stödja en delad instrumentpanel som ska användas av IT-supporten driftsgruppen.  Lär dig att:

> [!div class="checklist"]
> * Skapa en delad instrumentpanel i Azure Portal
> * Visualisera en prestanda loggfråga 
> * Lägga till en loggfråga till en delad instrumentpanel 
> * Anpassa en panel i en delad instrumentpanel

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.  
 
## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Skapa delade instrumentpaneler
Välj **instrumentpanelen** att öppna din standard [instrumentpanelen](../../azure-portal/azure-portal-dashboards.md). Din instrumentpanel ser annorlunda än i exemplet nedan.

![Instrumentpanelen för Azure portal](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Här kan du samla de driftdata som är viktigast för IT över alla dina Azure-resurser, inklusive telemetri från Azure Log Analytics.  Innan vi visualiserar en loggfråga vi först skapa en instrumentpanel och dela den.  Vi kan sedan fokusera på våra exempel prestanda log frågan, vilket ska återges som ett linjediagram och lägga till den på instrumentpanelen.  

Om du vill skapa en instrumentpanel väljer du knappen **Ny instrumentpanel** bredvid namnet på den aktuella instrumentpanelen.

![Skapa ny instrumentpanel i Azure-portalen](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Detta skapar en ny, tom, privat instrumentpanel och du hamnar i anpassningsläge. Där kan du ge instrumentpanelen ett namn och lägga till eller ordna om paneler. Redigera namnet på instrumentpanelen och ange *Exempelinstrumentpanel* för den här självstudien och välj **anpassningen är klar**.<br><br> ![Spara anpassade instrumentpaneler i Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Instrumentpaneler är privata som standard när de skapas, vilket innebär att du är den enda som kan se dem. Använd knappen **Dela** som visas tillsammans med andra kommandon i instrumentpanelen för att visa den för andra.

![Dela en ny instrumentpanel i Azure-portalen](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Du uppmanas att välja en prenumeration och resursgrupp som din instrumentpanel ska publiceras till. För att underlätta för dig guidar portalen dig genom ett mönster där du placerar dina instrumentpaneler i en resursgrupp som kallas **instrumentpaneler**.  Verifiera den valda prenumerationen och klicka på **Publicera**.  Du kontrollerar åtkomsten till den information som visas i instrumentpanelen med [Azures resursbaserade åtkomstkontroll](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualisera en loggfråga
[Log Analytics](../log-query/get-started-portal.md) är en dedikerad portal som används för att arbeta med loggfrågor och resultaten. Funktionen ger möjlighet att redigera en fråga på flera rader, köra kod selektivt, sammanhangsberoende Intellisense och Smart Analytics. I de här självstudierna använder du Log Analytics för att skapa en prestandavy i grafisk form, spara den för framtida fråga, och fästa den på den delade instrumentpanelen du skapade tidigare.

Öppna Log Analytics genom att välja **loggar** i Azure Monitor-menyn. Det börjar med en ny tom fråga.

![Startsida](media/tutorial-logs-dashboards/homepage.png)

Anger du följande fråga för att returnera processor processoranvändningsposter för både Windows och Linux-datorer, grupperade efter dator och TimeGenerated och visas i ett visuellt diagram. Klicka på **kör** att köra frågan och visa det resulterande diagrammet.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Spara frågan genom att välja den **spara** knappen högst upp på sidan.

![Spara fråga](media/tutorial-logs-dashboards/save-query.png)

I den **Spara fråga** Kontrollpanelen, ange ett namn som *Azure virtuella datorer – processoranvändning* och en kategori som *instrumentpaneler* och klicka sedan på **spara** .  På så sätt kan du skapa ett bibliotek med vanliga frågor som du kan använda och ändra.  Slutligen att fästa den delade instrumentpanelen du skapade tidigare genom att välja den **PIN-kod** knappen från det övre högra hörnet av sidan och sedan välja instrumentpanelens namn.

Nu när du har en fråga som är fäst på instrumentpanelen, ser du att den har en generisk rubrik och en kommentar under sig.

![Exempel på instrumentpanel i Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Du bör byta namn till ett beskrivande namn, som enkelt kan tolkas av de som tittar på den.  Klicka på redigeringsknappen om du vill anpassa rubrik och underrubrik för panelen och klicka sedan på **uppdatering**.  En banderoll visas med en fråga om du vill publicera eller ta bort ändringarna.  Klicka på **spara en kopia**.  

![Slutförd konfiguration av exemplet för instrumentpanel](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig att skapa en instrumentpanel i Azure portal och Lägg till en loggfråga.  Fortsätt till nästa självstudie och lär dig vilka olika svar som du kan implementera baserat på log frågeresultat.  

> [!div class="nextstepaction"]
> [Svara på händelser med Log Analytics-aviseringar](tutorial-response.md)
