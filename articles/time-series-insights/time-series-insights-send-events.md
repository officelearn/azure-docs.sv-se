---
title: Skicka händelser till en Azure Time Series Insights-miljö | Microsoft Docs
description: Lär dig hur du konfigurerar en event hub och kör ett exempelprogram för push-händelser som du kan visa i Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/26/2019
ms.custom: seodec18
ms.openlocfilehash: 84eb0e230875b999218b67d47a66a3c92b494253
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072827"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>Skicka händelser till en Time Series Insights-miljö med hjälp av en event hub

Den här artikeln beskriver hur du skapar och konfigurerar en Event Hub i Azure Event Hubs. Det beskriver också hur du kör ett exempel program för att push-överföra händelser till Azure Time Series Insights från Event Hubs. Om du har en befintlig händelsehubben med händelser i JSON-format, hoppar du över den här självstudien och visar din miljö i [Azure Time Series Insights](./time-series-insights-update-create-environment.md).

## <a name="configure-an-event-hub"></a>Skapa en Event Hub

1. Läs hur du skapar en event hub i den [dokumentation om Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
1. I sökrutan söker du efter **Händelsehubbar**. Välj i den returnerade listan **Händelsehubbar**.
1. Välj din event hub.
1. När du skapar en Event Hub skapar du ett namn område för Event Hub. Om du ännu inte har skapat en händelsehubben i namn området går du till menyn och skaparen Händelsehubben under entiteter.  

    [![Lista över händelse nav](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. När du skapar en event hub, markerar du den i listan över händelsehubbar.
1. På menyn under entiteterväljer du **Event Hubs**.
1. Välj namnet på händelsehubben för att konfigurera den.
1. Under **Översikt**väljer du **konsument grupper**och sedan **konsument grupp**.

    [![Skapa en konsument grupp](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Se till att du skapar en konsument grupp som uteslutande används av din Time Series Insights händelse källa.

    > [!IMPORTANT]
    > Se till att konsument gruppen inte används av någon annan tjänst, till exempel ett Azure Stream Analytics jobb eller någon annan Time Series Insights miljö. Om konsumentgruppen används av den andra påverkas negativt tjänster, Läs-och skrivåtgärder både för den här miljön och för andra tjänster. Om du använder **$Default** som konsumentgrupp, andra läsare potentiellt kan återanvända din konsumentgrupp.

1. Välj **principer för delad åtkomst**på menyn under **Inställningar**och välj sedan **Lägg till**.

    [![Välj principer för delad åtkomst och välj sedan knappen Lägg till](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. I den **Lägg till ny princip för delad åtkomst** fönstret Skapa en delad åtkomst med namnet **MySendPolicy**. Du använder den här delade åtkomst principen för att skicka händelser C# i exemplen senare i den här artikeln.

    [![I rutan princip namn anger du MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. Under **anspråk**markerar du kryss rutan **Skicka** .

## <a name="add-a-time-series-insights-instance"></a>Lägg till en Time Series Insights-instans

Time Series Insights-uppdateringen använder instanser för att lägga till kontextuella data i inkommande telemetridata. Data är ansluten när en fråga körs med hjälp av en **Time Series-ID**. **Time Series-ID: t** för det exempel Windmills-projekt som vi använder senare i `id`den här artikeln är. Mer information om Time Series Insight-instanser och **Time Series-ID**, se [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="create-a-time-series-insights-event-source"></a>Skapa en händelsekälla för Time Series Insights

1. Om du inte har skapat en händelsekälla kan slutföra stegen till [skapa en händelsekälla](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).

1. Ange ett värde för `timeSeriesId`. Mer information om **Time Series-ID**, se [Time Series modeller](./time-series-insights-update-tsm.md).

### <a name="push-events"></a>Push-händelser (windmills exemplet)

1. I sökfältet söker du efter **Händelsehubbar**. Välj i den returnerade listan **Händelsehubbar**.

1. Välj Event Hub-instansen.

1. Gå till **principer** > för delad åtkomst**MySendPolicy**. Kopiera värdet för **anslutnings strängen – primär nyckel**.

    [![Kopiera värdet för primär nyckelns anslutnings sträng](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. Gå till https://tsiclientsample.azurewebsites.net/windFarmGen.html. URL: en körs windmill simulerade enheter.
1. I den **Händelsehubbens anslutningssträng** rutan på webbsidan, klistra in anslutningssträngen som du kopierade i [skicka händelser](#push-events).
  
    [![Klistra in anslutnings strängen primär nyckel i rutan anslutnings sträng för händelse hubb](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. Välj **Klicka om du vill starta**. Simulatorn genererar instans JSON som du kan använda direkt.

1. Gå tillbaka till din event hub i Azure-portalen. På sidan **Översikt** ser du de nya händelser som tas emot av händelsehubben.

    [![En översikts sida för händelsehubben som visar mått för händelsehubben](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>JSON-former som stöds

### <a name="example-one"></a>Exempel en

* Inmatade: Ett enda JSON-objekt.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **Utdata**: En händelse.

    |id|tidsstämpel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>Exempel två

* Inmatade: En JSON-matris med två JSON-objekt. Varje JSON-objekt konverteras till en händelse.

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

    |id|tidsstämpel|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>Exempel tre

* Inmatade: En JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt.

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

* **Utdata**: Två händelser. Egenskapen **plats** kopieras till varje händelse.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>Exempel fyra

* Inmatade: En JSON-objekt med en kapslad JSON-matris som innehåller två JSON-objekt. Denna indata visar att globala egenskaperna kan representeras av komplexa JSON-objekt.

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

- [Visa din miljö](https://insights.timeseries.azure.com) i Time Series Insights Explorer.
