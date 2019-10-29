---
title: Sparat tillstånd i Linux-Azure Event Grid IoT Edge | Microsoft Docs
description: Bevara metadata i Linux
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 6639b8f4d947720db38a6b366c47700a56154af3
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992280"
---
# <a name="persist-state-in-linux"></a>Sparat tillstånd i Linux

Ämnen och prenumerationer som skapas i Event Grid-modulen lagras som standard i behållar fil systemet. Utan persistence, om modulen har distribuerats om, kommer alla metadata som skapats att gå förlorade. För närvarande sparas endast metadata. Händelser lagras i minnet. Om Event Grid modul omdistribueras eller startas om, kommer alla ej levererade händelser att gå förlorade.

Den här artikeln innehåller steg för att distribuera Event Grid-modulen med persistence i Linux-distributioner.

> [!NOTE]
>Event Grid-modulen körs som en låg privilegie rad användare med UID `2000` och namn `eventgriduser`.

## <a name="persistence-via-volume-mount"></a>Persistence via volym montering

 [Docker-volymer](https://docs.docker.com/storage/volumes/) används för att bevara data mellan distributioner. Du kan låta Docker skapa en namngiven volym automatiskt som en del av distributionen av Event Grid-modulen. Det här alternativet är det enklaste alternativet. Du kan ange det volym namn som ska skapas i avsnittet **bindningar** på följande sätt:

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
>Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För modulen Event Grid i Linux måste den vara **/app/metadata**.

Följande konfiguration leder till exempel till skapandet av volymen **egmetadataDbVol** där metadata kommer att sparas.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
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

Du kan också skapa en Docker-volym med hjälp av Docker-klient kommandon. 

## <a name="persistence-via-host-directory-mount"></a>Beständighet via värd katalog montering

I stället för en Docker-volym har du också möjlighet att montera en Host-mapp.

1. Skapa först en användare med namnet **eventgriduser** och ID **2000** på värddatorn genom att köra följande kommando:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Skapa en katalog på värd fil systemet genom att köra följande kommando.

   ```sh
   md <your-directory-name-here>
   ```

    Om du till exempel kör följande kommando skapas en katalog med namnet **myhostdir**.

    ```sh
    md /myhostdir
    ```
1. Sedan gör du **eventgriduser** ägare av den här mappen genom att köra följande kommando.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Exempel:

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Använd **bindningar** för att montera katalogen och distribuera om Event Grid-modulen från Azure Portal.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    Exempel:

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
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
    >Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För modulen Event Grid i Linux måste den vara **/app/metadata**.
