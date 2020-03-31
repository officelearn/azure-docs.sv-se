---
title: Händelsehanterare och mål – Azure Event Grid IoT Edge | Microsoft-dokument
description: Händelsehanterare och destinationer i händelserutnät på kant
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849752"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>Händelsehanterare och destinationer i händelserutnät på kant

En händelsehanterare är den plats där händelsen för ytterligare åtgärd eller för att bearbeta händelsen. Med modulen Event Grid on Edge kan händelsehanteraren finnas på samma kantenhet, en annan enhet eller i molnet. Du kan använda valfri WebHook för att hantera händelser eller skicka händelser till en av de inbyggda hanterarna som Azure Event Grid.

Den här artikeln innehåller information om hur du konfigurerar var och en.

## <a name="webhook"></a>WebHook (olika)

Om du vill publicera till en `endpointType` WebHook-slutpunkt ställer du in och `WebHook` anger:

* slutpunktUrl: Url:en webhook-slutpunkt

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

Om du vill publicera till en molnslutpunkt för Azure Event Grid anger du `endpointType` till `eventGrid` och tillhandahåller:

* endpointUrl: URL för händelserutnätsämne i molnet
* sasKey: Sas-tangenten för sas-händelserutnätet
* topicName: Namn om du vill stämpla alla utgående händelser i Händelserutnätet. Ämnesnamn är användbart när du bokför ett eventrutnätsdomänavsnitt.

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

## <a name="iot-edge-hub"></a>IoT Edge-hubb

Om du vill publicera till `endpointType` en `edgeHub` Edge Hub-modul ställer du in och tillhandahåller:

* outputName: Utdata som modulen Event Grid dirigerar händelser som matchar den här prenumerationen till edgeHub. Till exempel skrivs händelser som matchar nedanstående prenumeration till /messages/modules/eventgridmodule/outputs/sampleSub4.

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

## <a name="event-hubs"></a>Händelsehubbar

Om du vill publicera till `endpointType` `eventHub` en eventhubb ställer du in och tillhandahåller:

* connectionString: Anslutningssträng för den specifika händelsehubben som du riktar in dig på genereras via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutningssträngen måste vara entitetsspecifik. Det fungerar inte att använda en namnområdesanslutningssträng. Du kan generera en entitetsspecifik anslutningssträng genom att navigera till den specifika händelsehubb som du vill publicera till i Azure Portal och klicka på principer för **delad åtkomst** för att generera en ny entitetsspecifik connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>Service Bus-köer

Om du vill publicera till `endpointType` en `serviceBusQueue` servicebusskö ställer du in och tillhandahåller:

* connectionString: Anslutningssträng för den specifika servicebusskö som du riktar in dig på genereras via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutningssträngen måste vara entitetsspecifik. Det fungerar inte att använda en namnområdesanslutningssträng. Generera en entitetsspecifik anslutningssträng genom att navigera till den specifika servicebusskö som du vill publicera till i Azure Portal och klicka på Principer för **delad åtkomst** för att generera en ny entitetsspecifik connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>Avsnitt om Service Bus

Om du vill publicera till `endpointType` ett `serviceBusTopic` servicebussavsnitt ställer du in och tillhandahåller:

* connectionString: Anslutningssträng för det specifika servicebussämne som du riktar in dig på genereras via en princip för delad åtkomst.

    >[!NOTE]
    > Anslutningssträngen måste vara entitetsspecifik. Det fungerar inte att använda en namnområdesanslutningssträng. Generera en entitetsspecifik anslutningssträng genom att navigera till det specifika servicebussämne som du vill publicera till i Azure Portal och klicka på principer för **delad åtkomst** för att generera en ny entitetsspecifik connecection-sträng.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>Lagringsköer

Om du vill publicera i `endpointType` `storageQueue` en lagringskö ställer du in och tillhandahåller:

* queueName: Namnet på den lagringskö som du publicerar till.
* connectionString: Anslutningssträng för lagringskontot som lagringskön finns i.

    >[!NOTE]
    > Avgränsade händelsehubbar, servicebussköer och servicebussavsnitt, anslutningssträngen som används för lagringsköer är inte entitetsspecifik. I stället måste den bara anslutningssträngen för lagringskontot.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```