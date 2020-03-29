---
title: Vidarebefordra händelserutnätshändelser till IoTHub – Azure Event Grid IoT Edge | Microsoft-dokument
description: Vidarebefordra händelserutnätshändelser till IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844708"
---
# <a name="tutorial-forward-events-to-iothub"></a>Självstudiekurs: Vidarebefordra händelser till IoTHub

Den här artikeln går igenom alla steg som behövs för att vidarebefordra Event Grid-händelser till andra IoT Edge-moduler, IoTHub med hjälp av vägar. Du kanske vill göra det av följande skäl:

* Fortsätt att använda befintliga investeringar som redan finns med edgeHubs routning
* Föredrar att dirigera alla händelser från en enhet endast via IoT Hub

För att slutföra den här självstudien måste du förstå följande begrepp:

- [Begrepp för händelserutnät](concepts.md)
- [IoT Edge-nav](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Krav 
För att slutföra den här guiden behöver du:

* **Azure-prenumeration** - Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub och IoT Edge-enhet** – Följ stegen i snabbstarten för [Linux-](../../iot-edge/quickstart-linux.md) eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Skapa ämne

Som utgivare av en händelse måste du skapa ett ämne för händelserutnätet. Avsnittet refererar till en slutpunkt där utgivare sedan kan skicka händelser till.

1. Skapa topic4.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation.](api.md)

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Kör följande kommando för att skapa ämnet. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Kör följande kommando för att verifiera att ämnet har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Skapa händelseprenumeration

Prenumeranter kan registrera sig för evenemang som publiceras i ett ämne. För att få alla händelser måste de skapa en prenumeration på ett evenemang av intresse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa prenumeration4.json med innehållet nedan. Se vår [API-dokumentation](api.md) för mer information om nyttolasten.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > Anger `endpointType` att abonnenten ska vara `edgeHub`. Anger `outputName` den utdata som modulen Event Grid dirigerar händelser som matchar den här prenumerationen till edgeHub. Till exempel skrivs händelser som matchar `/messages/modules/eventgridmodule/outputs/sampleSub4`ovanstående prenumeration till .
2. Kör följande kommando för att skapa prenumerationen. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Kör följande kommando för att verifiera att prenumerationen har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Konfigurera en kanthubbväg

Uppdatera edge-hubbens väg till händelser för vidarebefordrar händelseprenumerationer som ska vidarebefordras till IoTHub enligt följande:

1. Logga in på [Azure-portalen](https://ms.portal.azure.com)
1. Navigera till **IoT Hub**.
1. Välj **IoT Edge** på menyn
1. Välj målenhetens ID i listan över enheter.
1. Välj **Ange moduler**.
1. Välj **Nästa** och till avsnittet rutter.
1. Lägg till en ny rutt i rutterna

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Exempel:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Den ovan nämnda vägen vidarebefordrar alla händelser som matchas för den här prenumerationen som ska vidarebefordras till IoT-hubben. Du kan använda routningsfunktionerna för [Edge-hubb](../../iot-edge/module-composition.md) för att filtrera ytterligare och dirigera händelserutnätshändelserna till andra IoT Edge-moduler.

## <a name="setup-iot-hub-route"></a>Guiden Konfigurera IoT Hub

Se [IoT Hub-routningsdjälvstudien](../../iot-hub/tutorial-routing.md) för att konfigurera en väg från IoT-hubben så att du kan visa händelser som vidarebefordras från modulen Event Grid. Använd `true` för frågan för att hålla självstudien enkel.  



## <a name="publish-an-event"></a>Publicera en händelse

1. Skapa event4.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation.](api.md)

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Kör följande kommando för att publicera händelse:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verifiera händelseleverans

Se IoT Hub [routningsdjälvstudien](../../iot-hub/tutorial-routing.md) för stegen för att visa händelserna.

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando om du vill ta bort avsnittet och alla dess prenumerationer på gränsen:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Ta bort alla resurser som skapas när du konfigurerar IoTHub-routning i molnet också.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du ett ämne för händelserutnät, edge hub-prenumeration och publicerade händelser. Nu när du vet de grundläggande stegen för att vidarebefordra till en kanthubb, se följande artiklar:

* Information om felsökning av problem med att använda Azure Event Grid på IoT Edge finns i [felsökningsguide .](troubleshoot.md)
* Använda [kanthubbvägfilter](../../iot-edge/module-composition.md) för att partitionera händelser
* Konfigurera persistens för Event Grid-modulen på [Linux](persist-state-linux.md) eller [Windows](persist-state-windows.md)
* Följ [dokumentationen](configure-client-auth.md) för att konfigurera klientautentisering
* Vidarebefordra händelser till Azure Event Grid i molnet genom att följa den här [självstudien](forward-events-event-grid-cloud.md)
* [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)