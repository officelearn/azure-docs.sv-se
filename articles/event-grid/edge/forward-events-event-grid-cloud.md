---
title: Händelser framåt till Event Grid-molnet – Azure Event Grid IoT Edge | Microsoft-dokument
description: Händelser framåt till moln för händelserutnät
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844725"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Självstudiekurs: Vidarebefordra händelser till moln för Händelserutnät

Den här artikeln går igenom alla steg som behövs för att vidarebefordra kanthändelser till Event Grid i Azure-molnet. Du kanske vill göra det av följande skäl:

* Reagera på kanthändelser i molnet.
* Vidarebefordra händelser till Event Grid i molnet och använd Azure Event Hubs eller Azure Storage-köer för att buffra händelser innan du bearbetar dem i molnet.

 För att slutföra den här självstudien måste du ha en förståelse för eventrutnätsbegrepp på [kant](concepts.md) och [Azure](../concepts.md). Ytterligare måltyper finns i [händelsehanterare](event-handlers.md). 

## <a name="prerequisites"></a>Krav 
För att slutföra den här guiden behöver du:

* **Azure-prenumeration** - Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub och IoT Edge-enhet** – Följ stegen i snabbstarten för [Linux-](../../iot-edge/quickstart-linux.md) eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Skapa ämne och prenumeration i händelserutnät i molnet

Skapa ett ämne och en prenumeration i molnet genom att följa den [här självstudien](../custom-event-quickstart-portal.md). `topicURL`Anmför `sasKey`, `topicName` och det nyskapade ämne som du ska använda senare i självstudien.

Om du till exempel har `testegcloudtopic` skapat ett ämne med namnet i västra USA skulle värdena se ut ungefär som:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**:`testegcloudtopic`
* **SasKey:** Tillgänglig under **AccessKey** av ditt ämne. Använd **key1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Skapa händelserutnätsämne vid kanten

1. Skapa topic3.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation.](api.md)

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Kör följande kommando för att skapa ämnet. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Kör följande kommando för att verifiera att ämnet har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Skapa prenumeration på Event Grid-abonnemang på gränsen

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa prenumeration3.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation.](api.md)

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **SlutpunktenUrl** anger att händelsen Rutnätsämne URL i molnet. **SasKey** refererar till händelsen Grid molnämnets nyckel. Värdet i **topicName** används för att stämpla alla utgående händelser till Event Grid. Detta kan vara användbart när du bokför ett domänavsnitt för Event Grid. Mer information om domänavsnittet För Händelserutnät finns i [Händelsedomäner](../event-domains.md)

    Exempel:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Kör följande kommando för att skapa prenumerationen. HTTP-statuskoden för 200 OK ska returneras.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Kör följande kommando för att verifiera att prenumerationen har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publicera en händelse vid kanten

1. Skapa event3.json med följande innehåll. Mer information om nyttolasten finns i [API-dokumentationen.](api.md)

    ```json
        [
          {
            "id": "eventId-egcloud-0",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. Kör följande kommando:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Verifiera kanthändelse i molnet

Information om hur du visar händelser som levereras av molnämnet finns i [självstudien](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando för att ta bort ämnet och alla dess prenumerationer

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Ta bort ämne och prenumerationer som skapats i molnet (Azure Event Grid) också.

## <a name="next-steps"></a>Nästa steg

I den här självstudien publicerade du en händelse på kanten och vidarebefordras till Event Grid i Azure-molnet. Nu när du vet de grundläggande stegen för att vidarebefordra till Event Grid i molnet:

* Information om felsökning av problem med att använda Azure Event Grid på IoT Edge finns i [felsökningsguide .](troubleshoot.md)
* Vidarebefordra händelser till IoTHub genom att följa den här [självstudien](forward-events-iothub.md)
* Vidarebefordra händelser till Webhook i molnet genom att följa den här [självstudien](pub-sub-events-webhook-cloud.md)
* [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)
