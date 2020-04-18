---
title: Skapa en miljö – Insikter i Azure Time Series | Microsoft-dokument
description: Lär dig hur du använder Azure-portalen för att skapa en ny Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 7459c6afc775aa0df43d6f9285191c4c7e1b8cb8
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602374"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Skapa en ny Time Series Insights-miljö i Azure Portal

I den här artikeln beskrivs hur du skapar en ny Time Series Insights-miljö med hjälp av Azure-portalen.

Med Time Series Insights kan du komma igång med att visualisera och fråga data som flödar in i Azure IoT Hubs och Event Hubs på några minuter, så att du kan fråga stora volymer tidsseriedata på några sekunder.  Den har utformats för Internet-of-things (IoT) skala och kan hantera terabyte data.

## <a name="steps-to-create-the-environment"></a>Anvisningar för att skapa miljön

Så här skapar du en miljö:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj knappen **+ Skapa en resurs.**

1. Välj kategorin **Sakernas Internet** och välj **Time Series Insights**.

   [![Skapa Time Series Insights-miljön](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. På sidan **Time Series Insights** väljer du **Skapa**.

1. Fyll i de parametrar som krävs. I följande tabell förklaras varje parameter:

   [![Skapa Time Series Insights-resursgruppen](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Inställning|Föreslaget värde|Beskrivning
   ---|---|---
   Miljönamn | Ett unikt namn | Det här namnet representerar miljön i [tidsserieutforskaren](https://insights.timeseries.azure.com)
   Prenumeration | Din prenumeration | Om du har flera prenumerationer väljer du den prenumeration som innehåller din händelsekälla helst. Time Series Insights kan automatiskt identifiera Azure IoT Hub- och Event Hub-resurser som finns i samma prenumeration.
   Resursgrupp | Skapa en ny eller använd befintlig | En resursgrupp är en samling med Azure-resurser som används tillsammans. Du kan välja en befintlig resursgrupp, till exempel den som innehåller din eventhubb eller IoT Hub. Du kan också skapa en ny om resursen inte är relaterad till de andra resurserna.
   Location | Närmast din händelsekälla | Välj helst samma datacenterplats som innehåller händelsekällans data, i ett försök att undvika extra bandbreddskostnader mellan regioner och korszoner och ökad svarstid när data flyttas från regionen.
   Prisnivå | S1 | Välj det genomflöde som behövs. För lägsta kostnader och startkapacitet väljer du S1.
   Kapacitet | 1 | Kapacitet är multiplikatorn gäller för ingående hastighet, lagringskapacitet och kostnad som är associerad med den valda SKU.  Du kan ändra kapacitet för en miljö när den har skapats. För lägsta kostnader väljer du en kapacitet på 1.
  
1. Välj **Skapa** om du vill påbörja etableringsprocessen. Det kan ta några minuter.

1. Om du vill övervaka distributionsprocessen väljer du symbolen **Meddelanden** (klockikonen).

   [![Titta på meddelandena](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Kontrollera konfigurationsinställningarna för distributionen i **resursöversikten.**

   [![Fäst Time Series Insights vid instrumentpanelen](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Valfritt)** Välj **pin-ikonen** i det övre högra hörnet för att enkelt komma åt din Time Series Insights-miljö i framtiden.

## <a name="next-steps"></a>Nästa steg

* Definiera principer för [dataåtkomst](time-series-insights-data-access.md) för att skydda din miljö.

* [Lägg till en event hub-händelsekälla](time-series-insights-how-to-add-an-event-source-eventhub.md) i din Azure Time Series Insights-miljö.

* [Skicka händelser](time-series-insights-send-events.md) till händelsekällan.

* Visa din miljö i [Time Series Insights explorer](https://insights.timeseries.azure.com).
