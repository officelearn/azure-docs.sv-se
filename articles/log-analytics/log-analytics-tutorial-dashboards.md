---
title: Skapa och dela instrumentpaneler med data från Azure Log Analytics | Microsoft Docs
description: Den här självstudien hjälper dig att förstå hur instrumentpanelerna i Log Analytics kan visualisera alla dina sparade loggsökningar, för att visa din miljö i en enda vy.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: f6a70f33b143a5b9354411af9717d2a21e24af0c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31409273"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Skapa och dela instrumentpaneler för Log Analytics

Instrumentpaneler för Log Analytics visar alla dina sparade loggsökningar, så att du kan hitta, samordna och dela IT-användningsdata i organisationen.  I den här självstudien visas hur du skapar en loggsökning som kan användas för att stödja en delad instrumentpanel som supportteamet kan använda för IT-åtgärder.  Lär dig att:

> [!div class="checklist"]
> * Skapa en delad instrumentpanel i Azure Portal
> * Visualisera en prestandaloggsökning 
> * Lägga till en loggsökning till en delad instrumentpanel 
> * Anpassa en panel i en delad instrumentpanel

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](log-analytics-quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.  
 
## <a name="log-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Skapa delade instrumentpaneler

Det första som visas när du loggar in på Microsoft Azure Portal är [instrumentpanelen](../azure-portal/azure-portal-dashboards.md).<br> ![Azure Portal-instrumentpanelen](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Här kan du samla de driftdata som är viktigast för IT över alla dina Azure-resurser, inklusive telemetri från Azure Log Analytics.  Innan vi går vidare med hur du visualiserar en loggsökning måste vi först skapa en instrumentpanel och dela den.  Detta gör att vi kan få undan den innan vi tar våra exempelprestanda för loggsökningen (som ska återges som ett linjediagram) och lägger till detta på instrumentpanelen.  

Om du vill skapa en instrumentpanel väljer du knappen **Ny instrumentpanel** bredvid namnet på den aktuella instrumentpanelen.<br> ![Skapa ny instrumentpanel i Azure Portal](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Detta skapar en ny, tom, privat instrumentpanel och du hamnar i anpassningsläge. Där kan du ge instrumentpanelen ett namn och lägga till eller ordna om paneler. Redigera namnet på instrumentpanelen och skriv *Exempelinstrumentpanel* för den här självstudien och välj **Anpassningen är klar**.<br><br> ![Spara anpassade instrumentpaneler i Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Instrumentpaneler är privata som standard när de skapas, vilket innebär att du är den enda som kan se dem. Använd knappen **Dela** som visas tillsammans med andra kommandon i instrumentpanelen för att visa den för andra.<br> ![Dela en ny instrumentpanel i Azure Portal](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

Du uppmanas att välja en prenumeration och resursgrupp som din instrumentpanel ska publiceras till. För att underlätta för dig guidar portalen dig genom ett mönster där du placerar dina instrumentpaneler i en resursgrupp som kallas **instrumentpaneler**.  Verifiera den valda prenumerationen och klicka på **Publicera**.  Du kontrollerar åtkomsten till den information som visas i instrumentpanelen med [Azures resursbaserade åtkomstkontroll](../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-search"></a>Visualisera en loggsökning

Skapa grundläggande frågor på en enda rad från loggsökningsportalen i Azure Portal. Loggsökningsportalen kan användas utan att du behöver starta externa portaler och du kan använda den för att utföra en mängd funktioner med loggsökningar, som t.ex. skapa varningsregler, datorgrupper och exportera frågeresultat. 

[Portalen Advanced Analytics](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) är en dedikerad portal som tillhandahåller avancerade funktioner som inte finns tillgängliga i loggsökningsportalen. Funktionen ger möjlighet att redigera en fråga på flera rader, köra kod selektivt, sammanhangsberoende Intellisense och Smart Analytics. I Advanced Analytics-portalen kan du skapa en prestandavy i grafisk form, spara den för framtida sökningar och fästa den på den delade instrumentpanelen som skapades tidigare.   

Du kan starta Advanced Analytics-portalen från en länk i loggsökningsportalen.<br> ![Starta Advanced Analytics-portalen](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

Ange följande fråga i Analytics-portalen för att bara returnera frågor gällande processoranvändning, för både Windows och Linux-datorer, grupperade efter dator och TimeGenerated, i ett visuellt diagram:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Spara frågan genom att välja knappen **Spara fråga** i det övre högra hörnet.<br> ![Spara fråga från Advanced Analytics-portalen](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> I kontrollpanelen **Spara fråga** anger du ett namn, som t.ex. *Virtuella datorer i Azure – processoranvändning* och klickar sedan på **Spara**.  På det här sättet kan du skapa ett bibliotek med vanliga frågor att söka med eller ändra utan att behöva skriva om helt.  Avsluta med att fästa den delade instrumentpanelen du skapade innan genom att välja knappen för att **fästa schemat på Azure-instrumentpanelen** i mitten till höger på sidan.  

Nu när du har en fråga som är fäst på instrumentpanelen, ser du att den har en generisk rubrik och en kommentar under sig.<br> ![Exempel på instrumentpanel i Azure](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Du bör byta namn till ett beskrivande namn, som enkelt kan tolkas av de som tittar på den.  Högerklicka på panelen och välj **Redigera panel**.  När du har angett en rubrik och en underrubrik för panelen klickar du på **Uppdatera**.  En banderoll visas med en fråga om du vill publicera eller ta bort ändringarna.  Klicka på **Publicera ändringar** och stäng sedan kontrollpanelen **Redigera panel**.  

![Slutförd konfiguration av exemplet för instrumentpanel](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur du skapar en instrumentpanel i Azure Portal och lägger till en loggsökning.  Fortsätt till nästa självstudie för att lära dig vilka olika svar du kan implementera baserat på resultaten av loggsökningen.  

> [!div class="nextstepaction"]
> [Svara på händelser med Log Analytics-aviseringar](log-analytics-tutorial-response.md)