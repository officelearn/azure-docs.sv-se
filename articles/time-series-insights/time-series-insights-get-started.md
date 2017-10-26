---
title: "Skapa en Azure Time Series Insights-miljö | Microsoft Docs"
description: "I den här självstudien får du lära dig att skapa en Time Series-miljö, ansluta den till en händelsekälla och redo att analysera dina händelsedata på några minuter."
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2017
ms.author: omravi
ms.openlocfilehash: d8a3f79630868c83cd9fde6ea0c414e334a58e22
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Skapa en ny Time Series Insights-miljö i Azure Portal

Time Series Insights-miljön är en Azure-resurs med ingångs- och lagringskapacitet. Kunder etablerar miljöer via Azure Portal med den kapacitet som krävs.

## <a name="steps-to-create-the-environment"></a>Anvisningar för att skapa miljön

Följ de här anvisningarna för att skapa en miljö:

1.  Logga in på [Azure Portal](https://portal.azure.com).
2.  Klicka på plustecknet (”+”) i det övre vänstra hörnet.
3.  Sök efter ”Time Series Insights” i sökrutan.

  ![Skapa Time Series Insights-miljön](media/get-started/getstarted-create-environment1.png)

4.  Välj ”Time Series Insights”, klicka på ”Skapa”.

  ![Skapa Time Series Insights-resursgruppen](media/get-started/getstarted-create-environment2.png)

5.  Ange ett namn på miljön. Namnet representerar miljön i [time series explorer](https://insights.timeseries.azure.com).
6.  Välj en prenumeration. Välj ett namn som innehåller din händelsekälla. Time Series Insights kan automatiskt identifiera Azure IoT Hub- och Event Hub-resurser som existerar i samma prenumeration.
7.  Välj eller skapa en Resursgrupp. En resursgrupp är en samling med Azure-resurser som används tillsammans.
8.  Välj en värdplats. Välj en plats som innehåller din källa för att undvika att flytta data mellan datacenter.
9.  Välj en prisnivå.
10. Välj kapacitet. Du kan ändra kapacitet för en miljö när den har skapats.
11. Skapa din miljö. Du kan också fästa miljön på instrumentpanelen för enkel åtkomst när du loggar in.

  ![Fäst Time Series Insights vid instrumentpanelen](media/get-started/getstarted-create-environment3.png)

## <a name="next-steps"></a>Nästa steg

* [Definiera dataåtkomstprinciper](time-series-insights-data-access.md) för att få åtkomst till miljön i [Time Series Insights-portalen](https://insights.timeseries.azure.com)
* [Skapa en händelsekälla](time-series-insights-add-event-source.md)
* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan
