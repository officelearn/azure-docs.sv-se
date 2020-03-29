---
title: Publicera, prenumerera på händelser i molnet – Azure Event Grid IoT Edge | Microsoft-dokument
description: Publicera, prenumerera på händelser i molnet med Webhook med Event Grid på IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844605"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Självstudiekurs: Publicera, prenumerera på händelser i molnet

Den här artikeln går igenom alla steg som behövs för att publicera och prenumerera på händelser med Event Grid på IoT Edge. Den här självstudien använder och Azure-funktionen som händelsehanterare. Ytterligare måltyper finns i [händelsehanterare](event-handlers.md).

Se [Begrepp i händelserutnätet](concepts.md) för att förstå vad ett händelserutnätsämne och prenumeration är innan du fortsätter.

## <a name="prerequisites"></a>Krav 
För att slutföra den här guiden behöver du:

* **Azure-prenumeration** - Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub och IoT Edge-enhet** – Följ stegen i snabbstarten för [Linux-](../../iot-edge/quickstart-linux.md) eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Skapa en Azure-funktion i Azure-portalen

Följ stegen som beskrivs i [självstudien](../../azure-functions/functions-create-first-azure-function.md) för att skapa en Azure-funktion. 

Ersätt kodavsnittet med följande kod:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

I den nya funktionen väljer du **Hämta funktions-URL** längst upp till höger, väljer standard (**funktionstangent**) och väljer sedan **Kopiera**. Du kommer att använda funktions-URL-värdet senare i självstudien.

> [!NOTE]
> Se [Azure Functions-dokumentationen](../../azure-functions/functions-overview.md) för fler exempel och självstudier om hur du reagerar på händelser som använder EventGrid-händelseutlösare.

## <a name="create-a-topic"></a>Skapa ett ämne

Som utgivare av en händelse måste du skapa ett ämne för händelserutnätet. Avsnittet refererar till en slutpunkt där utgivare kan skicka händelser till.

1. Skapa topic2.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation.](api.md)

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Kör följande kommando för att skapa ämnet. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Kör följande kommando för att verifiera att ämnet har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Skapa en händelseprenumeration

Prenumeranter kan registrera sig för evenemang som publiceras i ett ämne. För att få alla händelser måste prenumeranterna skapa en prenumeration på ett evenemang av intresse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa prenumeration2.json med följande innehåll. Se vår [API-dokumentation](api.md) för mer information om nyttolasten.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **Slutpunkten anger** att prenumeranten är en Webhook.  **SlutpunktenUrl** anger webbadressen där prenumeranten lyssnar efter händelser. Den här URL:en motsvarar det Azure-funktionsexempel som du har konfigurerat tidigare.
2. Kör följande kommando för att skapa prenumerationen. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Kör följande kommando för att verifiera att prenumerationen har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicera en händelse

1. Skapa event2.json med följande innehåll. Se vår [API-dokumentation](api.md) för mer information om nyttolasten.

    ```json
        [
          {
            "id": "eventId-func-1",
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
1. Kör följande kommando för att publicera händelse

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verifiera händelseleverans

Du kan visa händelsen som levererades i Azure-portalen under alternativet **Övervaka** för din funktion.

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando för att ta bort ämnet och alla dess prenumerationer

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Ta bort Azure-funktionen som skapats i Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du ett ämne för händelserutnät, prenumeration och publicerade händelser. Nu när du känner till de grundläggande stegen läser du följande artiklar:

* Information om felsökning av problem med att använda Azure Event Grid på IoT Edge finns i [felsökningsguide .](troubleshoot.md)
* Skapa/uppdatera prenumeration med [filter](advanced-filtering.md).
* Konfigurera persistens för Event Grid-modulen på [Linux](persist-state-linux.md) eller [Windows](persist-state-windows.md)
* Följ [dokumentationen](configure-client-auth.md) för att konfigurera klientautentisering
* Vidarebefordra händelser till Azure Event Grid i molnet genom att följa den här [självstudien](forward-events-event-grid-cloud.md)
* [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)
