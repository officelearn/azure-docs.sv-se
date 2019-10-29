---
title: Spara tillstånd i Windows-Azure Event Grid IoT Edge | Microsoft Docs
description: Sparat tillstånd i Windows
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992267"
---
# <a name="persist-state-in-windows"></a>Sparat tillstånd i Windows

Ämnen och prenumerationer som skapas i Event Grid-modulen lagras som standard i behållar fil systemet. Utan persistence, om modulen har distribuerats om, kommer alla metadata som skapats att gå förlorade. Om du vill bevara data mellan distributioner måste du spara data utanför behållar fil systemet. För närvarande sparas endast metadata. Händelser lagras i minnet. Om Event Grid modul omdistribueras eller startas om, kommer alla ej levererade händelser att gå förlorade.

Den här artikeln innehåller de steg som krävs för att distribuera Event Grid-modulen med persistence i Windows-distributioner.

> [!NOTE]
>Event Grid-modulen körs som en låg privilegie rad användar **ContainerUser** i Windows.

## <a name="persistence-via-volume-mount"></a>Persistence via volym montering

[Docker-volymer](https://docs.docker.com/storage/volumes/) används för att bevara data mellan distributioner. Om du vill montera en volym måste du skapa den med Docker-kommandon, ge behörigheter så att behållaren kan läsa, skriva till den och sedan distribuera modulen. Det finns ingen möjlighet att automatiskt skapa en volym med nödvändiga behörigheter för Windows. Den måste skapas innan du distribuerar.

1. Skapa en volym genom att köra följande kommando:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Exempel:

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Hämta värd katalogen som volymen mappar till genom att köra kommandot nedan

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
1. Lägg till gruppen **användare** till värdet som påpekas av **monterings punkt** enligt följande:
    1. Starta Utforskaren.
    1. Navigera till mappen som är påpekad av **monterings punkt**.
    1. Högerklicka på och välj sedan **Egenskaper**.
    1. Välj **säkerhet**.
    1. Under * grupp-eller användar namn väljer du **Redigera**.
    1. Välj **Lägg till**, ange `Users`, Välj **kontrol lera namn**och välj **OK**.
    1. Under *behörigheter för användare*väljer du **ändra**och sedan **OK**.
1. Använd **bindningar** för att montera volymen och distribuera om Event Grid modul från Azure Portal

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
   >Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För Event Grid modul i Windows måste den vara **C:\\app\\metadataDb**.


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
                "myeventgridvol:C:\\app\\metadataDb"
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

## <a name="persistence-via-host-directory-mount"></a>Beständighet via värd katalog montering

Du kan också välja att skapa en katalog på värd systemet och montera den katalogen.

1. Skapa en katalog på värd fil systemet genom att köra följande kommando.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Exempel:

   ```sh
   mkdir C:\myhostdir
   ```
1. Använd **bindningar** för att montera katalogen och distribuera om Event Grid-modulen från Azure Portal.

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
    >Ändra inte den andra delen av bind-värdet. Den pekar på en angiven plats i modulen. För Event Grid modulen i Windows måste den vara **C:\\app\\metadataDb**.

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
