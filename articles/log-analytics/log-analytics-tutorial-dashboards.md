---
title: Skapa och dela instrumentpaneler med data i Azure Log Analytics | Microsoft Docs
description: "Den här kursen hjälper dig att förstå hur logganalys instrumentpaneler kan visualisera alla dina sparad logg-sökningar, vilket ger dig en enda lins att visa din miljö."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 272945134b534a5ded794379ce5e96b0902a4227
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Skapa och dela instrumentpaneler för logganalys-data

Logganalys instrumentpaneler kan visualisera alla dina sparad logg-sökningar, ger dig möjlighet att hitta, korrelera och dela IT användningsdata i organisationen.  Den här kursen ingår att skapa en sökning i loggen som ska användas för att stödja en delad instrumentpanel som ska användas av supportteamet för IT-åtgärder.  Lär dig att:

> [!div class="checklist"]
> * Skapa en delad instrumentpanel i Azure-portalen
> * Visualisera en prestanda log-sökning 
> * Lägga till en logg sökning till en delad instrumentpanel 
> * Anpassa en panel i en delad instrumentpanel

Du måste ha en befintlig virtuell dator för att slutföra exemplet i den här självstudiekursen [ansluten till logganalys-arbetsytan](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Skapa en delad instrumentpanel

Det första som visas när du loggar in på Microsoft Azure portal är en [instrumentpanelen](../azure-portal/azure-portal-dashboards.md).<br> ![Azure portalens instrumentpanel](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Här kan du sammanfoga användningsdata som är viktigast för IT över alla dina Azure-resurser, inklusive telemetri från Azure logganalys.  Innan vi Stega visualisera en logg sökning vi först skapa en instrumentpanel och dela den.  Detta gör att vi kan få undan innan vi tar våra exempel prestanda loggen sökning, som ska återges som ett linjediagram och lägga till på instrumentpanelen.  

Om du vill skapa en instrumentpanel, Välj den **ny instrumentpanel** knappen bredvid namnet på den aktuella instrumentpanelen.<br> ![Skapa ny instrumentpanel i Azure-portalen](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Den här åtgärden skapar en ny, tom, privata instrumentpanel och placerar du i anpassning läge där du kan kalla instrumentpanelen och lägga till eller ordna om paneler. Redigera namnet på instrumentpanelen och ange *Exempelinstrumentpanel* för den här kursen väljer **klar anpassa**.<br><br> ![Spara anpassade Azure instrumentpanelen](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Den är privat som standard, vilket innebär att du är den enda som kan se den när du skapar en instrumentpanel. Använd för att göra den synlig för andra i **resursen** som visas tillsammans med andra kommandon för instrumentpanelen.<br> ![Dela en ny instrumentpanel i Azure-portalen](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

Du uppmanas att välja en prenumeration och resursgrupp för din instrumentpanel som ska publiceras. För enkelhetens skull portalen har publicering upplevelse guider du mot ett mönster som placerar du instrumentpaneler i en resursgrupp kallas **instrumentpaneler**.  Verifiera den valda prenumerationen och klicka sedan på **publicera**.  Åtkomst till den information som visas i instrumentpanelen styrs med [Azure baserad åtkomstkontroll till resurser](../active-directory/role-based-access-control-configure.md).   

## <a name="visualize-a-log-search"></a>Visualisera en logg-sökning

Du kan skapa grundläggande frågor på en rad från portalen loggen Sök i Azure-portalen. Loggen Sök-portalen kan användas utan att starta en extern portal och du kan använda för att utföra en mängd funktioner med loggen sökningar, inklusive att skapa Varningsregler, skapar datorgrupper och exportera resultatet av frågan. 

Den [avancerade analyser portal](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) är en dedikerad portal som ger avancerade funktioner som inte är tillgängliga i loggen Sök-portalen. Innefattar möjligheten att redigera en fråga på flera rader, selektivt köra kod, sammanhangsberoende Intellisense och smarta Analytics. Advanced Analytics-portalen du skapa en prestandavy i grafisk form, spara den för framtida sökningar och Fäst det på den delade instrumentpanel som har skapats tidigare.   

Du kan starta Advanced Analytics-portalen från en länk i loggen Sök-portalen.<br> ![Starta Advanced Analytics-portalen](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

Ange följande fråga för att returnera endast processor användning poster för både Windows och Linux-datorer, grupperade efter dator och TimeGenerated och visas i ett visual diagram i Analytics-portalen:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Spara frågan genom att välja den **spara frågan** knappen från det övre högra hörnet.<br> ![Spara frågan från Advanced Analytics-portalen](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> I den **Spara fråga** Kontrollpanelen, ange ett namn som *virtuella datorer i Azure - processorbelastning* och klicka sedan på **spara**.  Det här sättet kan du skapa ett bibliotek med vanliga frågor för att söka med eller ändra den utan att behöva Skriv den helt.  Slutligen fästa det delade instrumentpanelen skapade tidigare genom att välja **PIN-kod diagram på instrumentpanelen i Azure** knappen från mitten till höger på sidan.  

Nu när vi har en fråga som är fäst på instrumentpanelen kan ser du den har en generisk rubrik och en kommentar under den.<br> ![Instrumentpanelen för Azure-exempel](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Vi bör byta namn på den till ett beskrivande som enkelt kan tolkas av de visar den.  Högerklicka på panelen och välj **redigera panelen**.  När du är klar anpassa rubriken och underrubriken för panelen klickar du på **uppdatering**.  En banderoll visas där du ombeds att publicera ändringar eller ta bort.  Klicka på **publicera ändringar** och stäng sedan den **redigera panelen** Kontrollpanelen.  

![Slutfört konfigurationen av exemplet på instrumentpanel](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du skapar en instrumentpanel i Azure-portalen och lägger till en logg-sökning.  Gå till nästa kurs att lära sig olika svar som du kan implementera baserat på loggen sökresultat.  

> [!div class="nextstepaction"]
> [Svara på händelser med Log Analytics aviseringar](log-analytics-tutorial-response.md)