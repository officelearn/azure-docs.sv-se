---
title: "Nära realtid mått aviseringar i Azure-Monitor | Microsoft Docs"
description: "Nära realtid mått kan aviseringar du övervaka Azure-resurs så ofta som 1 minut."
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Nära realtid mått aviseringar (förhandsgranskning)
Azure-Monitor stöder nu en ny typ av mått aviseringar kallas nära realtid mått aviseringar (förhandsversion). Den här funktionen är för närvarande i förhandsversion.
Dessa aviseringar skiljer sig från vanlig mått aviseringar på några olika sätt

- **Förbättrad latens** -nära realtid mått kan aviseringar övervaka ändringar i måttvärden så snart som 1 minut.
- **Mer kontroll över mått villkor** -nära realtid mått aviseringar kan du definiera bättre Varningsregler. Aviseringarna nu stöd för övervakning maximala, minsta, genomsnittlig och totala värden mätvärden.
- **Kombinerade övervakning av flera mått** -nära realtid mått aviseringar kan du övervaka flera (för närvarande två) med en enskild regel. Varning aktiveras om båda mätvärdena bryta mot sina respektive tröskelvärden för den angivna tidsperioden.
- **Modulära meddelandesystem** - nära realtid mått aviseringar Använd [åtgärdsgrupper](monitoring-action-groups.md). Den här funktionen kan användare skapa åtgärder i en modulär sätt och återanvända för många Varningsregler.

> [!NOTE]
> Nära realtid mått aviseringar är för närvarande i förhandsversion. Funktioner och användarupplevelse kan ändras.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Vilka resurser kan skapa nära realtid mått aviseringar för?
Fullständig lista över resurstyper som stöds av nära realtid mått aviseringar:

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>Skapa en nära realtid mått
Nära realtid mått kan för närvarande aviseringar endast skapas via Azure-portalen. Stöd för att konfigurera nästan realtid mått aviseringar via PowerShell-kommandoradsgränssnittet (CLI) och REST-API för Azure-Monitor kommer snart.

1. I den [portal](https://portal.azure.com/), leta upp den resurs som du är intresserad av övervakning och markera den. Den här resursen ska vara av en av de resurstyper som anges i den [föregående avsnitt](#what-resources-can-i-create-near-real-time-metric-alerts-for). Du kan också göra samma för alla typer av resurser stöds centralt från övervakaren > aviseringar.

2. Välj **aviseringar** eller **Varna regler** under avsnittet övervakning. Text och ikon kan variera något mellan olika resurser.
   ![Övervakning](./media/insights-alerts-portal/AlertRulesButton.png)

3. Klicka på den **Lägg till nära realtid mått avisering (förhandsgranskning)** kommando. Om kommandot är nedtonad, se till att resursen är markerad i filtret.

    ![Lägg till nära realtid mått avisering knappen](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. **Namnet** aviseringen regel och väljer en **beskrivning**, som visar även i e-postmeddelanden.
5. Välj den **mått** du vill övervaka och väljer sedan en **villkoret**, **Aggregation tid**, och **tröskelvärdet** värdet för måttet. Du kan också välja en annan **mått** du vill övervaka och väljer sedan en **villkoret**, **Aggregation tid**, och **tröskelvärdet** värde för andra mått. 

    ![Lägg till nära realtid mått Alert1](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![lägga till nära realtid mått Alert2](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. Välj den **Period** som mått regler måste uppfyllas innan aviseringen utlösare. Om du använder perioden ”under de senaste 5 minuterna” och din avisering ser ut för CPU över 80% (och NetworkIn ovan 500 MB), startar exempelvis aviseringen när Processorn har konsekvent ovan 80% i 5 minuter. När den första utlösaren infaller utlöses igen när Processorn är mindre än 80% i 5 minuter. Aviseringen utvärderas enligt det **utvärdering frekvens**


6. Välja en lämplig **allvarlighetsgrad** i listrutan.

7. Ange om du vill använda ett nytt eller befintligt **grupp**.

8. Om du väljer att skapa **ny** grupp, ge åtgärdsgruppen ett namn och ett kort namn, ange åtgärder (SMS, e-post, Webhook) och fylla respektive information.


8. Välj **OK** när du är klar för att skapa aviseringen.   

Inom några minuter aviseringen är aktiv och utlöser som beskrivits tidigare.

## <a name="managing-near-real-time-metric-alerts"></a>Hantera nära realtid mått aviseringar
När du har skapat en avisering, kan du välja den och:

* Visa ett diagram som visar mått tröskelvärdet och faktiska värden från föregående dag.
* Redigera eller ta bort den.
* **Inaktivera** eller **aktivera** den om du vill att tillfälligt stoppa eller återuppta tar emot meddelanden om den här aviseringen.



