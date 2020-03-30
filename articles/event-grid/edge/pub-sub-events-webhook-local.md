---
title: Publicera, prenumerera på händelser lokalt - Azure Event Grid IoT Edge | Microsoft-dokument
description: Publicera, prenumerera på händelser lokalt med Webhook med Event Grid på IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281007"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Självstudiekurs: Publicera, prenumerera på händelser lokalt

I den här artikeln får du lära dig igenom alla steg som behövs för att publicera och prenumerera på händelser med Event Grid på IoT Edge.

> [!NOTE]
> Mer information om ämnen och prenumerationer i Azure Event Grid finns i [Begrepp om händelserutnät](concepts.md).

## <a name="prerequisites"></a>Krav 
För att slutföra den här guiden behöver du:

* **Azure-prenumeration** - Skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub och IoT Edge-enhet** – Följ stegen i snabbstarten för [Linux-](../../iot-edge/quickstart-linux.md) eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har någon.

## <a name="deploy-event-grid-iot-edge-module"></a>Distribuera IoT Edge-module för händelserutnät

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar för Azure Event Grid på IoT Edge. I den här artikeln beskrivs stegen för att distribuera Event Grid på IoT Edge från Azure-portalen.

>[!NOTE]
> I den här självstudien distribuerar du modulen Event Grid utan att vara beständig. Det innebär att alla ämnen och prenumerationer som du skapar i den här självstudien tas bort när du distribuerar om modulen. Mer information om hur du konfigurerar persistens finns i följande artiklar: [Beständigt tillstånd i Linux](persist-state-linux.md) eller [Kvarstår i Windows](persist-state-windows.md). För produktionsarbetsbelastningar rekommenderar vi att du installerar modulen Event Grid med uthållighet.


### <a name="select-your-iot-edge-device"></a>Välj din IoT Edge-enhet

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Navigera till din IoT Hub.
1. Välj **IoT Edge** på menyn i avsnittet **Automatisk enhetshantering.** 
1. Klicka på målenhetens ID från listan över enheter
1. Välj **Ange moduler**. Håll sidan öppen. Du fortsätter med stegen i nästa avsnitt.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributionsmanifest

Ett distributionsmanifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan modulerna och önskade egenskaper för modultvillingarna. Azure-portalen har en guide som hjälper dig att skapa ett distributionsmanifest i stället för att skapa JSON-dokumentet manuellt.  Den har tre steg: **Lägg till moduler,** **Ange vägar**och Granska **distribution**.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **Distributionsmoduler** väljer du **Lägg till**
1. Välj **IoT Edge Module** i de typer av moduler som finns i listrutan
1. Ange alternativ för att skapa namn, avbildning, behållare för behållaren:

   * **Namn**: eventgridmodule
   * **Bild URI:**`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Skapa alternativ för att skapa behållare:**

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Klicka på **Spara**
 1. Fortsätt till nästa avsnitt för att lägga till Azure Event Grid Subscriber-modulen innan du distribuerar dem tillsammans.

    >[!IMPORTANT]
    > I den här självstudien distribuerar du modulen Event Grid med klientautentisering inaktiverad. För produktionsarbetsbelastningar rekommenderar vi att du aktiverar klientautentiseringen. Mer information om hur du konfigurerar Modulen Event Grid på ett säkert sätt finns i [Säkerhet och autentisering](security-authentication.md).
    > 
    > Om du använder en Virtuell Azure-dator som en kantenhet lägger du till en inkommande portregel för att tillåta inkommande trafik på port 4438. Instruktioner om hur du lägger till regeln finns i [Så här öppnar du portar till en virtuell dator](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Distribuera IoT Edge-modulen För händelserutnät

I det här avsnittet visas hur du distribuerar en annan IoT-modul som skulle fungera som en händelsehanterare till vilken händelser som kan levereras.

### <a name="add-modules"></a>Lägga till moduler

1. Välj **Lägg till** igen i avsnittet **Distributionsmoduler.** 
1. Välj **IoT Edge Module** i de typer av moduler som finns i listrutan
1. Ange alternativ för att skapa namn, avbildning och behållare för behållaren:

   * **Namn**: abonnent
   * **Bild URI:**`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Alternativ för att skapa behållare:** Inga
1. Klicka på **Spara**
1. Klicka på **Nästa** om du vill fortsätta till flödet

 ### <a name="setup-routes"></a>Installationsvägar

Behåll standardvägarna och välj **Nästa** om du vill fortsätta till granskningsavsnittet

### <a name="submit-the-deployment-request"></a>Skicka distributionsbegäran

1. Granskningsavsnittet visar det JSON-distributionsmanifest som skapades baserat på dina val i föregående avsnitt. Bekräfta att du ser båda modulerna: **eventgridmodule** och **prenumerant** som anges i JSON. 
1. Granska distributionsinformationen och välj sedan **Skicka**. När du har skickat distributionen går du tillbaka till **enhetssidan.**
1. I **avsnittet Moduler**kontrollerar du att både **eventgrid-** och **abonnentmoduler** visas. Kontrollera att kolumnerna **Anges i distributionen** och **Rapporterad av enhet** är inställda på **Ja**.

    Det kan ta en stund innan modulen startas på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan för att se en uppdaterad status.

## <a name="create-a-topic"></a>Skapa ett ämne

Som utgivare av en händelse måste du skapa ett ämne för händelserutnätet. I Azure Event Grid refererar ett ämne till en slutpunkt där utgivare kan skicka händelser till.

1. Skapa topic.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Kör följande kommando för att skapa ett händelserutnätsämne. Bekräfta att HTTP-statuskoden `200 OK`är .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Kör följande kommando för att verifiera att ämnet har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Exempel på utdata:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Skapa en händelseprenumeration

Prenumeranter kan registrera sig för evenemang som publiceras i ett ämne. Om du vill ta emot en händelse måste du skapa en Event Grid-prenumeration för ett ämne av intresse.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Skapa subscription.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > Egenskapen **endpointType** anger att prenumeranten är en **Webhook**.  **SlutpunktenUrl** anger webbadressen där prenumeranten lyssnar efter händelser. Den här URL:en motsvarar exemplet på Azure Subscriber som du har distribuerat tidigare.
2. Kör följande kommando för att skapa en prenumeration för ämnet. Bekräfta att HTTP-statuskoden `200 OK`är .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Kör följande kommando för att verifiera att prenumerationen har skapats. HTTP-statuskoden för 200 OK ska returneras.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publicera en händelse

1. Skapa event.json med följande innehåll. Mer information om nyttolasten finns i vår [API-dokumentation](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Kör följande kommando för att publicera en händelse.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Verifiera händelseleverans

1. SSH eller RDP till din IoT Edge VM.
1. Kontrollera prenumerantloggarna:

    I Windows kör du följande kommando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Kör följande kommando på Linux:

    ```sh
    sudo docker logs subscriber
    ```

    Exempel på utdata:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Rensa resurser

* Kör följande kommando för att ta bort ämnet och alla dess prenumerationer.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Ta bort prenumerantmodulen från din IoT Edge-enhet.


## <a name="next-steps"></a>Nästa steg
I den här självstudien skapade du ett ämne för händelserutnät, prenumeration och publicerade händelser. Nu när du känner till de grundläggande stegen läser du följande artiklar: 

- Information om felsökning av problem med att använda Azure Event Grid på IoT Edge finns i [felsökningsguide .](troubleshoot.md)
- Skapa/uppdatera prenumeration med [filter](advanced-filtering.md).
- Aktivera persistens av Event Grid-modulen på [Linux](persist-state-linux.md) eller [Windows](persist-state-windows.md)
- Följ [dokumentationen](configure-client-auth.md) för att konfigurera klientautentisering
- Vidarebefordra händelser till Azure-funktioner i molnet genom att följa den här [självstudien](pub-sub-events-webhook-cloud.md)
- [Reagera på Blob Storage-händelser på IoT Edge](react-blob-storage-events-locally.md)
- [Övervaka ämnen och prenumerationer på gränsen](monitor-topics-subscriptions.md)

