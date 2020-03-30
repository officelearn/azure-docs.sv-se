---
title: Beständigt tillstånd i Windows – Azure Event Grid IoT Edge | Microsoft-dokument
description: Beständigt tillstånd i Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c2bae3bd268dba8efdf23ae314671b17a2c89420
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086623"
---
# <a name="persist-state-in-windows"></a>Beständigt tillstånd i Windows

Ämnen och prenumerationer som skapats i modulen Event Grid lagras som standard i behållarfilsystemet. Utan beständighet, om modulen distribueras om, skulle alla metadata som skapas gå förlorade. Om du vill bevara data över distributioner och omstarter måste du spara data utanför behållarfilsystemet. 

Som standard sparas endast metadata och händelser lagras fortfarande i minnet för bättre prestanda. Följ avsnittet Beständiga händelser för att även aktivera händelsebeständighet.

Den här artikeln innehåller de steg som krävs för att distribuera Event Grid-modulen med uthållighet i Windows-distributioner.

> [!NOTE]
>Event Grid-modulen körs som en **lågbehörighetsanvändarcontaineranvändareuser** i Windows.

## <a name="persistence-via-volume-mount"></a>Persistens via volymfäste

[Docker-volymer](https://docs.docker.com/storage/volumes/) används för att bevara data över distributioner. Om du vill montera en volym måste du skapa den med dockerkommandon, ge behörighet så att behållaren kan läsa, skriva till den och sedan distribuera modulen.

1. Skapa en volym genom att köra följande kommando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Exempel:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Hämta värdkatalogen som volymen mappar till genom att köra kommandot nedan

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Exempel:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Exempel på utdata:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. Lägg till gruppen **Användare** i värdet som **monterats** på följande sätt:
    1. Starta Utforskaren.
    1. Navigera till mappen som pekas av **Mountpoint**.
    1. Högerklicka och välj sedan **Egenskaper**.
    1. Välj **Säkerhet**.
    1. Under *Grupp- eller användarnamn väljer du **Redigera**.
    1. Välj **Lägg** `Users`till , välj **Kontrollera namn**och välj **Ok**.
    1. Under *Behörigheter för användare*väljer du **Ändra**och väljer **Ok**.
1. Använd **bindningar** för att montera den här volymen och distribuera om event grid-modulen från Azure-portalen

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >Ändra inte den andra delen av bindningsvärdet. Det pekar på en specifik plats i modulen. För Event Grid-modulen i windows måste det vara **C:\\app\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>Persistens via värdkatalogfäste

I stället för att montera en volym kan du skapa en katalog på värdsystemet och montera den katalogen.

1. Skapa en katalog i värdfilsystemet genom att köra följande kommando.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Exempel:

   ```sh
   mkdir C:\myhostdir
   ```
1. Använd **Bindningar** för att montera katalogen och distribuera om event grid-modulen från Azure-portalen.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Ändra inte den andra delen av bindningsvärdet. Det pekar på en specifik plats i modulen. För modulen Event Grid i windows måste det vara **C:\\app\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>Bevara händelser

Om du vill aktivera händelsebeständighet måste du först aktivera händelser persistens antingen via volymmontering eller värdkatalogfäste med hjälp av ovanstående avsnitt.

Viktiga saker att notera om kvarstående händelser:

* Beständiga händelser aktiveras per händelseprenumeration och är opt-in när en volym eller katalog har monterats.
* Händelsebeständighet har konfigurerats på en händelseprenumeration vid skapande och kan inte ändras när händelseprenumerationen har skapats. Om du vill växla händelsebeständighet måste du ta bort och återskapa händelseprenumerationen.
* Ihållande händelser är nästan alltid långsammare än i minnesoperationer, men hastighetsskillnaden är mycket beroende av enhetens egenskaper. Avvägningen mellan hastighet och tillförlitlighet är inneboende i alla meddelandesystem men blir bara en märkbar i stor skala.

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