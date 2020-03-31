---
title: Skicka händelser till en miljö – Insikter i Azure Time Series | Microsoft-dokument
description: Lär dig hur du konfigurerar en händelsehubb, kör ett exempelprogram och skickar händelser till din Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254253"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Skicka händelser till en time series insights-miljö med hjälp av en händelsehubb

I den här artikeln beskrivs hur du skapar och konfigurerar en händelsehubb i Azure Event Hubs. Den beskriver också hur du kör ett exempelprogram för att skicka händelser till Azure Time Series Insights från Event Hubs. Om du har en befintlig händelsehubb med händelser i JSON-format hoppar du över den här självstudien och visar din miljö i [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub

1. Mer information om hur du skapar en händelsehubb läser du [dokumentationen för Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. Sök efter **händelsehubbar**i sökrutan . Välj **Event Hubs**i den returnerade listan .
1. Välj din händelsehubb.
1. När du skapar en händelsehubb skapar du ett namnområde för händelsehubben. Om du ännu inte har skapat en händelsehubb i namnområdet skapar du en händelsehubb på menyn under **Entiteter.**  

    [![Lista över händelsehubbar](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. När du har skapat en händelsehubb markerar du den i listan över händelsehubbar.
1. Välj **Händelserhubbar under Entiteter**på menyn. **Entities**
1. Välj namnet på händelsehubben för att konfigurera den.
1. Under **Översikt**väljer du **Konsumentgrupper**och väljer sedan **Konsumentgrupp**.

    [![Skapa en konsumentgrupp](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Se till att du skapar en konsumentgrupp som används uteslutande av händelsekällan Time Series Insights.

    > [!IMPORTANT]
    > Kontrollera att den här konsumentgruppen inte används av någon annan tjänst, till exempel ett Azure Stream Analytics-jobb eller en annan Time Series Insights-miljö. Om konsumentgruppen används av de andra tjänsterna påverkas läsåtgärderna negativt både för den här miljön och för andra tjänster. Om du använder **$Default** som konsumentgrupp kan andra läsare eventuellt återanvända din konsumentgrupp.

1. Välj Principer för delad **åtkomst** **på**menyn och välj sedan **Lägg till**.

    [![Välj principer för delad åtkomst och välj sedan knappen Lägg till](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. Skapa en delad åtkomst med namnet **MySendPolicy**i **fönstret Lägg till ny princip för delad** åtkomst . Du använder den här principen om delad åtkomst för att skicka händelser i C#-exemplen senare i den här artikeln.

    [![Ange MySendPolicy i rutan Principnamn](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. Markera kryssrutan **Skicka** under **Anspråk.**

## <a name="add-a-time-series-insights-instance"></a>Lägga till en Time Series Insights-instans

Uppdateringen Time Series Insights använder instanser för att lägga till kontextuella data i inkommande telemetridata. Data sammanfogas vid frågetillfället med hjälp av ett **tidsserie-ID**. **Time Series ID** för provet väderkvarnar projekt som `id`vi använder senare i den här artikeln är . Mer information om Time Series Insight-instanser och **Time Series-ID**läser du [Time Series Models](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Skapa en händelsekälla för Tidsserier

1. Om du inte har skapat en händelsekälla slutför du stegen för att [skapa en händelsekälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ange ett `timeSeriesId`värde för . Om du vill veta mer om **Time Series ID**läser du [Time Series Models](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Push händelser till väderkvarnar prov

1. Sök efter **händelsehubbar**i sökfältet . Välj **Event Hubs**i den returnerade listan .

1. Välj din händelsehubbinstans.

1. Gå till **Principer för** > delad åtkomst**MySendPolicy**. Kopiera värdet för **strängprimtnyckel för anslutning**.

    [![Kopiera värdet för anslutningssträngen för primärnyckel](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. Gå till https://tsiclientsample.azurewebsites.net/windFarmGen.html. URL:en skapar och kör simulerade väderkvarnenheter.
1. Klistra in anslutningssträngen som du kopierade i [indatafältet](#push-events-to-windmills-sample)Event **Hub På** webbsidan .
  
    [![Klistra in anslutningssträngen för primärnyckel i rutan Anslutningssträng för händelsehubben](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. Välj **Klicka för att starta**. 

    > [!TIP]
    > Väderkvarnsimulatorn skapar också JSON som du kan använda som nyttolast med [Time Series Insights GA Query API:er](https://docs.microsoft.com/rest/api/time-series-insights/ga-query).

    > [!NOTE]
    > Simulatorn fortsätter att skicka data tills webbläsarfliken stängs.

1. Gå tillbaka till din händelsehubb i Azure-portalen. På sidan **Översikt** visas de nya händelser som tas emot av händelsehubben.

    [![En översiktssida för händelsehubben som visar mått för händelsehubben](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>JSON-former som stöds

### <a name="example-one"></a>Exempel ett

* **Ingång**: Ett enkelt JSON-objekt.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Utdata**: En händelse.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exempel två

* **Ingång**: En JSON-matris med två JSON-objekt. Varje JSON-objekt konverteras till en händelse.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **Utdata**: Två händelser.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exempel tre

* **Indata**: Ett JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **Utdata**: Två händelser. **Egenskapsplatsen** kopieras över till varje händelse.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exempel fyra

* **Indata**: Ett JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt. Den här indata visar att globala egenskaper kan representeras av det komplexa JSON-objektet.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **Utdata**: Två händelser.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Nästa steg

- [Visa din miljö](https://insights.timeseries.azure.com) i utforskaren Time Series Insights.

- Läs mer om [IoT Hub-enhetsmeddelanden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
