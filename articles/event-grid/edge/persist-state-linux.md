---
title: Beständigt tillstånd i Linux – Azure Event Grid IoT Edge | Microsoft-dokument
description: Beständiga metadata i Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 12655d2ceb4a1124376d9bddf82194472c98ebb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086650"
---
# <a name="persist-state-in-linux"></a>Persist tillstånd i Linux

Ämnen och prenumerationer som skapats i modulen Event Grid lagras som standard i behållarfilsystemet. Utan beständighet, om modulen distribueras om, skulle alla metadata som skapas gå förlorade. Om du vill bevara data över distributioner och omstarter måste du spara data utanför behållarfilsystemet.

Som standard sparas endast metadata och händelser lagras fortfarande i minnet för bättre prestanda. Följ avsnittet Beständiga händelser för att även aktivera händelsebeständighet.

Den här artikeln innehåller stegen för att distribuera modulen Event Grid med uthållighet i Linux-distributioner.

> [!NOTE]
>Modulen Event Grid körs som en användare `2000` med `eventgriduser`låg behörighet med UID och namn .

## <a name="persistence-via-volume-mount"></a>Persistens via volymfäste

 [Docker-volymer](https://docs.docker.com/storage/volumes/) används för att bevara data över distributioner. Du kan låta docker automatiskt skapa en namngiven volym som en del av distributionen av modulen Event Grid. Det här alternativet är det enklaste alternativet. Du kan ange det volymnamn som ska skapas i avsnittet **Bindningar** på följande sätt:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Ändra inte den andra delen av bindningsvärdet. Det pekar på en specifik plats i modulen. För Event Grid-modulen på Linux måste det vara **/app/metadataDb**.

Följande konfiguration kommer till exempel att resultera i skapandet av volymen **egmetadataDbVol** där metadata kommer att bevaras.

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
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

I stället för att montera en volym kan du skapa en katalog på värdsystemet och montera den katalogen.

## <a name="persistence-via-host-directory-mount"></a>Persistens via värdkatalogfäste

I stället för en dockervolym har du också möjlighet att montera en värdmapp.

1. Skapa först en användare med namn **eventgriduser** och ID **2000** på värddatorn genom att köra följande kommando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Skapa en katalog på värdfilsystemet genom att köra följande kommando.

   ```sh
   md <your-directory-name-here>
   ```

    Om du till exempel kör följande kommando skapas en katalog som heter **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Gör sedan **eventgriduser** till ägare av den här mappen genom att köra följande kommando.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Exempel:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Använd **Bindningar** för att montera katalogen och distribuera om modulen Event Grid från Azure-portalen.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    Exempel:

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
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

    >[!IMPORTANT]
    >Ändra inte den andra delen av bindningsvärdet. Det pekar på en specifik plats i modulen. För Event Grid-modulen på Linux måste det vara **/app/metadataDb** och **/app/eventsDb**


## <a name="persist-events"></a>Bevara händelser

Om du vill aktivera händelsebeständighet måste du först aktivera metadatabeständighet antingen via volymmontering eller värdkatalogfäste med hjälp av ovanstående avsnitt.

Viktiga saker att notera om kvarstående händelser:

* Beständiga händelser aktiveras per händelseprenumeration och är opt-in när en volym eller katalog har monterats.
* Händelsebeständighet har konfigurerats på en händelseprenumeration vid skapande och kan inte ändras när händelseprenumerationen har skapats. Om du vill växla händelsebeständighet måste du ta bort och återskapa händelseprenumerationen.
* Ihållande händelser är nästan alltid långsammare än i minnesoperationer, men hastighetsskillnaden är mycket beroende av enhetens egenskaper. Avvägningen mellan hastighet och tillförlitlighet är inneboende i alla meddelandesystem men i allmänhet bara blir en märkbar i stor skala.

Om du vill aktivera händelsebeständighet `persistencePolicy` `true`för en händelseprenumeration ställer du in på:

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
