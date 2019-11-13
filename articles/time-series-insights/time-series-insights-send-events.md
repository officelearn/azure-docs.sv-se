---
title: Skicka händelser till en miljö – Azure Time Series Insights | Microsoft Docs
description: Lär dig hur du konfigurerar en Event Hub, kör ett exempel program och skickar händelser till din Azure Time Series Insights-miljö.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: cdcd64b5281ce16002720072db3b5f29f1978cac
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014835"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av en event hub

Den här artikeln beskriver hur du skapar och konfigurerar en Event Hub i Azure Event Hubs. Det beskriver också hur du kör ett exempel program för att push-överföra händelser till Azure Time Series Insights från Event Hubs. Om du har en befintlig händelsehubben med händelser i JSON-format, hoppar du över den här självstudien och visar din miljö i [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub

1. Läs hur du skapar en event hub i den [dokumentation om Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. I sökrutan söker du efter **Händelsehubbar**. Välj i den returnerade listan **Händelsehubbar**.
1. Välj din event hub.
1. När du skapar en Event Hub skapar du ett namn område för Event Hub. Om du ännu inte har skapat en händelsehubben i namn området går du till menyn och skapar en händelsehubben under **entiteter**.  

    [![lista över Event Hub](media/send-events/1-event-hub-namespace.png)](media/send-events/1-event-hub-namespace.png#lightbox)

1. När du skapar en event hub, markerar du den i listan över händelsehubbar.
1. På menyn under **entiteter**väljer du **Event Hubs**.
1. Välj namnet på händelsehubben för att konfigurera den.
1. Under **Översikt**väljer du **konsument grupper**och sedan **konsument grupp**.

    [![skapa en konsument grupp](media/send-events/2-consumer-group.png)](media/send-events/2-consumer-group.png#lightbox)

1. Se till att du skapar en konsument grupp som uteslutande används av din Time Series Insights händelse källa.

    > [!IMPORTANT]
    > Se till att konsument gruppen inte används av någon annan tjänst, till exempel ett Azure Stream Analytics jobb eller någon annan Time Series Insights miljö. Om konsumentgruppen används av den andra påverkas negativt tjänster, Läs-och skrivåtgärder både för den här miljön och för andra tjänster. Om du använder **$Default** som konsumentgrupp, andra läsare potentiellt kan återanvända din konsumentgrupp.

1. Välj **principer för delad åtkomst**på menyn under **Inställningar**och välj sedan **Lägg till**.

    [![välja principer för delad åtkomst och välj sedan knappen Lägg till](media/send-events/3-shared-access-policy.png)](media/send-events/3-shared-access-policy.png#lightbox)

1. I den **Lägg till ny princip för delad åtkomst** fönstret Skapa en delad åtkomst med namnet **MySendPolicy**. Du använder den här delade åtkomst principen för att skicka händelser C# i exemplen senare i den här artikeln.

    [![i rutan princip namn, ange MySendPolicy](media/send-events/4-shared-access-policy-confirm.png)](media/send-events/4-shared-access-policy-confirm.png#lightbox)

1. Under **anspråk**markerar du kryss rutan **Skicka** .

## <a name="add-a-time-series-insights-instance"></a>Lägg till en Time Series Insights-instans

Time Series Insights-uppdateringen använder instanser för att lägga till kontextuella data i inkommande telemetridata. Data är ansluten när en fråga körs med hjälp av en **Time Series-ID**. **Time Series-ID: t** för det exempel Windmills-projekt som vi använder senare i den här artikeln är `id`. Mer information om Time Series Insight-instanser och **Time Series-ID**, se [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Skapa en händelsekälla för Time Series Insights

1. Om du inte har skapat en händelsekälla kan slutföra stegen till [skapa en händelsekälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ange ett värde för `timeSeriesId`. Mer information om **Time Series-ID**, se [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="push-events-to-windmills-sample"></a>Push-händelser till Windmills-exempel

1. I sökfältet söker du efter **Händelsehubbar**. Välj i den returnerade listan **Händelsehubbar**.

1. Välj Event Hub-instansen.

1. Gå till **principer för delad åtkomst** > **MySendPolicy**. Kopiera värdet för **anslutnings strängen – primär nyckel**.

    [![kopiera värdet för primär nyckelns anslutnings sträng](media/send-events/5-sample-code-connection-string.png)](media/send-events/5-sample-code-connection-string.png#lightbox)

1. Gå till https://tsiclientsample.azurewebsites.net/windFarmGen.html. URL: en körs windmill simulerade enheter.
1. I rutan **anslutnings sträng för händelsehubben** på webb sidan klistrar du in anslutnings strängen som du kopierade i fältet [Windmill](#push-events-to-windmills-sample).
  
    [![klistra in anslutnings strängen för primär nyckel i rutan anslutnings sträng för händelse hubb](media/send-events/6-wind-mill-sim.png)](media/send-events/6-wind-mill-sim.png#lightbox)

1. Välj **Klicka om du vill starta**. Simulatorn genererar instans JSON som du kan använda direkt.

1. Gå tillbaka till din event hub i Azure-portalen. På sidan **Översikt** ser du de nya händelser som tas emot av händelsehubben.

    [![en översikts sida för Event Hub som visar mått för händelsehubben](media/send-events/7-telemetry.png)](media/send-events/7-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>JSON-former som stöds

### <a name="example-one"></a>Exempel en

* **Inmatade**: ett enkelt JSON-objekt.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Utdata**: en händelse.

    |id|tidsstämpel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exempel två

* **Inmatade**: en JSON-matris med två JSON-objekt. Varje JSON-objekt konverteras till en händelse.

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

* **Utdata**: två händelser.

    |id|tidsstämpel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exempel tre

* **Inmatade**: ett JSON-objekt med en KAPSLAd JSON-matris som innehåller två JSON-objekt.

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

* **Utdata**: två händelser. Egenskapen **plats** kopieras till varje händelse.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exempel fyra

* **Inmatade**: ett JSON-objekt med en KAPSLAd JSON-matris som innehåller två JSON-objekt. Denna indata visar att globala egenskaperna kan representeras av komplexa JSON-objekt.

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

* **Utdata**: två händelser.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.units|events.data.value|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|tryck|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>Nästa steg

- [Visa din miljö](https://insights.timeseries.azure.com) i Time Series Insights Explorer.

- Läs mer om [IoT Hub enhets meddelanden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)
