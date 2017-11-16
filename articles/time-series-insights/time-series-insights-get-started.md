---
title: "Skapa en Azure Time Series Insights-miljö | Microsoft Docs"
description: "Den här artikeln beskriver hur du använder Azure-portalen för att skapa en ny tid serien insikter miljö."
services: time-series-insights
ms.service: time-series-insights
author: op-ravi
ms.author: omravi
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 6dba703851161a1eebce0101be8076682f09c76f
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Skapa en ny Time Series Insights-miljö i Azure Portal
Den här artikeln beskriver hur du skapar en ny tid serien insikter miljö med Azure-portalen.

Tid serien insikter kan du börja visualisera och hämtning av data som flödar till Azure IoT Hub och Händelsehubbar i minuter, hjälper dig att fråga stora datavolymer tid serien i sekunder.  Den har utformats för skalan sakernas internet (IoT) och kan hantera terabyte data.

## <a name="steps-to-create-the-environment"></a>Anvisningar för att skapa miljön
Följ dessa steg om du vill skapa en miljö:

1.  Logga in på [Azure Portal](https://portal.azure.com).

2.  Välj den **+ ny** knappen.

3.  Välj den **Sakernas Internet** kategori och välj **tid serien insikter**.

   ![Skapa Time Series Insights-miljön](media/time-series-insights-get-started/1-new-tsi.png)

4.  På den **tid serien insikter** väljer **skapa**.

5. Fyll i de obligatoriska parametrarna. I följande tabell beskrivs varje parameter:
   
   ![Skapa Time Series Insights-resursgruppen](media/time-series-insights-get-started/2-create-tsi.png)
   
   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Namn | Ett unikt namn | Det här namnet representerar miljön i [tid serien explorer](https://insights.timeseries.azure.com)
   Prenumeration | Din prenumeration | Välj den prenumeration som innehåller din händelsekälla helst om du har flera prenumerationer. Time Series Insights kan automatiskt identifiera Azure IoT Hub- och Event Hub-resurser som existerar i samma prenumeration.
   Resursgrupp | Skapa en ny eller använda befintliga | En resursgrupp är en samling med Azure-resurser som används tillsammans. Du kan välja en befintlig resursgrupp, till exempel en som innehåller din Händelsehubb eller IoT-hubb. Eller så kan du en ny om resursen inte är relaterad till andra resurser.
   Plats | Närmast din händelsekälla | Helst välja samma data center-plats som innehåller källdata händelse i för att undvika att läggas till mellan region och mellan zon bandbreddskostnader och läggs svarstid när data flyttas utanför området.
   Prisnivå | S1 | Välj genomflödet behövs. Välj S1 för lägsta kostnader och starter kapacitet.
   Kapacitet | 1 | Kapaciteten är multiplikatorn gäller för ingång hastighet, kapacitet och kostnader som är associerade med den valda SKU: N.  Du kan ändra kapacitet för en miljö när den har skapats. Välj en kapacitet på 1 för lägsta kostnader. 
  
6. Kontrollera **fäst på instrumentpanelen** bäst enkelt få tid serien miljön i framtiden.

   ![Fäst Time Series Insights vid instrumentpanelen](media/time-series-insights-get-started/3-pin-create.png)

7. Välj **skapa** att påbörja etableringen. Det kan ta några minuter.

8. För att övervaka processen för distribution, Välj den **meddelanden** symbol (klockikonen).

   ![Titta på alla meddelanden](media/time-series-insights-get-started/4-notifications.png)

När distributionen lyckas, kan du välja **finns resurs** för att konfigurera andra egenskaper, ange säkerhet med principer för åtkomst av data, lägga till händelsekällor och andra åtgärder.

## <a name="next-steps"></a>Nästa steg
* [Definiera principer för åtkomst av data](time-series-insights-data-access.md) att skydda din miljö.
* [Lägg till en Händelsehubb händelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md) till Azure tid serien Insights-miljön. 
* [Skicka händelser](time-series-insights-send-events.md) käll-händelse.
* Visa din miljö i [tid serien insikter explorer](https://insights.timeseries.azure.com).
