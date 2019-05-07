---
title: Skapa en Azure Time Series Insights-miljö | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure-portalen för att skapa en ny Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: d6e38465b46f387092ab457ebe6716a3dcff4768
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205850"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Skapa en ny Time Series Insights-miljö i Azure Portal

Den här artikeln beskriver hur du skapar en ny Time Series Insights-miljö med Azure portal.

Time Series Insights kan du börja visualisera och köra frågor mot data som flödar till Azure IoT Hub och Event Hubs på några minuter, så att du kan ställa frågor till stora volymer av time series-data på några sekunder.  Det har utformats för att skala för internet of things (IoT) och kan hantera flera terabyte data.

## <a name="steps-to-create-the-environment"></a>Anvisningar för att skapa miljön

Följ stegen nedan för att skapa en miljö:

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj den **+ ny** knappen.

1. Välj den **Internet of Things** kategori och välj **Time Series Insights**.

   ![Skapa Time Series Insights-miljön](media/time-series-insights-get-started/1-new-tsi.png)

1. På den **Time Series Insights** väljer **skapa**.

1. Fyll i de obligatoriska parametrarna. I följande tabell beskrivs varje parameter:
   
   ![Skapa Time Series Insights-resursgruppen](media/time-series-insights-get-started/2-create-tsi.png)
   
   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Miljönamn | Ett unikt namn | Det här namnet representerar miljön i [time series explorer](https://insights.timeseries.azure.com)
   Prenumeration | Din prenumeration | Om du har flera prenumerationer väljer du den prenumeration som innehåller din källa som helst. Time Series Insights kan automatiskt identifiera Azure IoT Hub- och Event Hub-resurser som existerar i samma prenumeration.
   Resursgrupp | Skapa en ny eller Använd en befintlig | En resursgrupp är en samling med Azure-resurser som används tillsammans. Du kan välja en befintlig resursgrupp, till exempel det som innehåller din Händelsehubb eller IoT-hubb. Eller så kan du ett nytt om den här resursen inte är relaterat till andra resurser.
   Location | Närmast din händelsekälla | Välj helst samma datacenterplats som innehåller källdata händelse i för att undvika att stöd för kostnader för bandbredd över flera regioner och mellan zoner samt fördröjning när du flyttar data från regionen.
   Prisnivå | S1 | Välj det genomflöde som behövs. Välj S1 för lägsta kostnader och starter kapacitet.
   Kapacitet | 1 | Kapaciteten är multiplikatorn som gäller för ingångshändelser, kapacitet och kostnad som hör till den valda SKU: N.  Du kan ändra kapacitet för en miljö när den har skapats. Välj en kapacitet på 1 för lägsta kostnader. 
  
1. Kontrollera **fäst på instrumentpanelen** bäst enkelt åtkomst till din Time Series-miljö i framtiden.

   ![Fäst Time Series Insights vid instrumentpanelen](media/time-series-insights-get-started/3-pin-create.png)

1. Välj **skapa** att påbörja etableringen. Det kan ta några minuter.

1. För att övervaka distributionsprocessen, Välj den **meddelanden** symbolen (klockikon).

   ![Titta på meddelanden](media/time-series-insights-get-started/4-notifications.png)

När distributionen är klar kan du välja **gå till resurs** för att konfigurera andra egenskaper, ställa in säkerhet med principerna för dataåtkomst, lägga till händelsekällor och andra åtgärder.

## <a name="next-steps"></a>Nästa steg

* [Definiera dataåtkomstprinciper](time-series-insights-data-access.md) att skydda din miljö.

* [Lägga till en Event Hub-händelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md) till Azure Time Series Insights-miljön.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Visa din miljö i [Time Series Insights explorer](https://insights.timeseries.azure.com).
