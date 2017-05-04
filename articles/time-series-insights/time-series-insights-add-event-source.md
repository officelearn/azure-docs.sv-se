---
title: "Lägga till en händelsekälla i miljön för Azure Time Series Insights | Microsoft Docs"
description: "I den här självstudien ansluter du en händelsekälla till Time Series Insights-miljön"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 9dc394c0da34a8ee2796c6114e7f2f647c5345a1
ms.lasthandoff: 04/25/2017

---

# <a name="create-an-event-source-for-your-time-series-insights-environment-using-the-azure-portal"></a>Skapa en händelsekälla för miljön för Time Series Insights med Azure Portal

Händelsekällan Time Series Insights härleds från en asynkron meddelandekö för händelser, som Azure Event Hubs. Time Series Insights ansluts direkt till händelsekällor och matar in dataströmmen utan att kräva att användarna skriver en enda rad kod. För närvarande stöder Time Series Insights Azure Event Hubs och Azure IoT Hubs, och i framtiden kommer fler händelsekällor att läggas till.

## <a name="steps-to-add-an-event-source-to-your-environment"></a>Anvisningar för att lägga till en händelsekälla i din miljö

1.    Logga in på [Azure-portalen](https://portal.azure.com).
2.    Klicka på ”Alla resurser” på menyn på vänster sida av Azure Portal.
3.    Välj Time Series Insights-miljö.

  ![Skapa händelsekällan för Time Series Insights](media/add-event-source/getstarted-create-eventsource1.png)

4.    Välj ”händelsekällor” och klicka på ”+ Lägg till”.

  ![Skapa händelsekällan för Time Series Insights – information](media/add-event-source/getstarted-create-eventsource2.png)

5.    Ange namnet på händelsekällan. Det här namnet är associerat med alla händelser som kommer från den här händelsekällan och är tillgänglig när en fråga körs.
6.    Välj en Event Hub från listan över Event Hub-resurser i den aktuella prenumerationen eller välj importalternativet "Tillhandahåll händelsehubbsinställningar manuellt” för att ange en Event Hub som finns i en annan prenumeration. Händelser måste publiceras i JSON-format.
7.    Välj en princip som har läsbehörighet i Event Hub.
8.    Välja konsumentgrupp för Event Hub.

  > [!IMPORTANT]
  > Kontrollera att den här konsumentgruppen inte används av andra tjänster (t.ex Stream Analytics-jobb eller en annan Time Series Insights-miljö). Om en konsumentgrupp används av en annan tjänst påverkas läsåtgärden negativt för den här miljön och de andra tjänsterna. Om du använder ”$Default” som konsumentgrupp kan det leda till eventuell återanvändning av andra läsare.

9.    Klicka på ”Skapa”.

När du har skapat händelsekällan börjar Time Series Insights automatiskt att strömma data till din miljö.

## <a name="next-steps"></a>Nästa steg

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan
* Visa din miljö i [Time Series Insights-portalen](https://insights.timeseries.azure.com)

