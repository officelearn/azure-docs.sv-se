---
title: Skapa och dela instrumentpaneler med data från Azure Log Analytics | Microsoft Docs
description: Den här självstudien hjälper dig att förstå hur Log Analytics-instrumentpaneler kan visualisera alla dina sparade loggfrågor, vilket ger dig ett enda objektiv för att visa din miljö.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 76ba79561df4a75004369d24c4c6af82de9b1cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661540"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Skapa och dela instrumentpaneler för Log Analytics

Log Analytics-instrumentpaneler kan visualisera alla dina sparade loggfrågor, vilket ger dig möjlighet att hitta, korrelera och dela IT-operativa data i organisationen.  Den här självstudien omfattar att skapa en loggfråga som ska användas för att stödja en delad instrumentpanel som kommer att nås av supportteamet för IT-operationer.  Lär dig att:

> [!div class="checklist"]
> * Skapa en delad instrumentpanel i Azure Portal
> * Visualisera en prestandaloggfråga 
> * Lägga till en loggfråga på en delad instrumentpanel 
> * Anpassa en panel i en delad instrumentpanel

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.  
 
## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Skapa delade instrumentpaneler
Välj **Instrumentpanel för** att öppna [standardinstrumentpanelen](../../azure-portal/azure-portal-dashboards.md). Instrumentpanelen ser annorlunda ut än exemplet nedan.

![Instrumentpanelen för Azure-portalen](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Här kan du samla de driftdata som är viktigast för IT över alla dina Azure-resurser, inklusive telemetri från Azure Log Analytics.  Innan vi går in i visualisering av en loggfråga ska vi först skapa en instrumentpanel och dela den.  Vi kan sedan fokusera på vår exempel prestanda logg fråga, som kommer att återge som ett linjediagram, och lägga till den i instrumentpanelen.  

Om du vill skapa en instrumentpanel väljer du knappen **Ny instrumentpanel** bredvid namnet på den aktuella instrumentpanelen.

![Skapa ny instrumentpanel i Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Detta skapar en ny, tom, privat instrumentpanel och du hamnar i anpassningsläge. Där kan du ge instrumentpanelen ett namn och lägga till eller ordna om paneler. Redigera namnet på instrumentpanelen och ange *Exempelinstrumentpanel* för den här självstudien och välj sedan **Klar med anpassningen**.<br><br> ![Spara anpassade instrumentpaneler i Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Instrumentpaneler är privata som standard när de skapas, vilket innebär att du är den enda som kan se dem. Använd knappen **Dela** som visas tillsammans med andra kommandon i instrumentpanelen för att visa den för andra.

![Dela en ny instrumentpanel i Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Du uppmanas att välja en prenumeration och resursgrupp som din instrumentpanel ska publiceras till. För att underlätta för dig guidar portalen dig genom ett mönster där du placerar dina instrumentpaneler i en resursgrupp som kallas **instrumentpaneler**.  Verifiera den valda prenumerationen och klicka på **Publicera**.  Du kontrollerar åtkomsten till den information som visas i instrumentpanelen med [Azures resursbaserade åtkomstkontroll](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualisera en loggfråga
[Log Analytics](../log-query/get-started-portal.md) är en dedikerad portal som används för att arbeta med loggfrågor och deras resultat. Funktionen ger möjlighet att redigera en fråga på flera rader, köra kod selektivt, sammanhangsberoende Intellisense och Smart Analytics. I den här självstudien använder du Log Analytics för att skapa en prestandavy i grafisk form, spara den för en framtida fråga och fästa den på den delade instrumentpanelen som skapats tidigare.

Öppna Log Analytics genom att välja **Loggar** på Azure Monitor-menyn. Det börjar med en ny tom fråga.

![Startsida](media/tutorial-logs-dashboards/homepage.png)

Ange följande fråga för att returnera processoranvändningsposter för både Windows- och Linux-datorer, grupperade efter dator och TimeGenerated, och som visas i ett visuellt diagram. Klicka på **Kör** om du vill köra frågan och visa det resulterande diagrammet.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Spara frågan genom att välja knappen **Spara** högst upp på sidan.

![Spara fråga](media/tutorial-logs-dashboards/save-query.png)

Ange ett namn som *Azure-virtuella datorer – processoranvändning* och en kategori som *instrumentpaneler* på kontrollpanelen **Spara** fråga och klicka sedan på **Spara**.  På så sätt kan du skapa ett bibliotek med vanliga frågor som du kan använda och ändra.  Slutligen fäster du den på den delade instrumentpanelen som skapats tidigare genom att välja knappen **Fäst på instrumentpanelen** längst upp till höger på sidan och sedan välja instrumentpanelsnamnet.

Nu när du har en fråga som är fäst på instrumentpanelen, ser du att den har en generisk rubrik och en kommentar under sig.

![Exempel på instrumentpanel i Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Du bör byta namn till ett beskrivande namn, som enkelt kan tolkas av de som tittar på den.  Klicka på redigeringsknappen om du vill anpassa rubriken och undertexten för panelen och klicka sedan på **Uppdatera**.  En banderoll visas med en fråga om du vill publicera eller ta bort ändringarna.  Klicka på **Spara en kopia**.  

![Slutförd konfiguration av exemplet för instrumentpanel](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien lärde du dig hur du skapar en instrumentpanel i Azure-portalen och lägger till en loggfråga i den.  Gå vidare till nästa självstudiekurs om du vill lära dig de olika svaren som du kan implementera baserat på loggfrågeresultat.  

> [!div class="nextstepaction"]
> [Svara på händelser med Log Analytics-aviseringar](tutorial-response.md)
