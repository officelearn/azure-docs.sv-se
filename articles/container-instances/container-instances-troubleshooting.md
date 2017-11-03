---
title: "Felsöka Azure-Behållarinstanser"
description: "Lär dig hur du felsöker problem med Azure Container instanser"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ff6da0ce95d0405714602c3872da34a2bff344d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Felsöka distributionsproblem med Azure Container instanser

Den här artikeln visar hur du felsöker problem när du distribuerar behållare till Azure-Behållarinstanser. Här beskrivs också några vanliga problem kan du stöta på.

## <a name="getting-diagnostic-events"></a>Hämtning av diagnostiska händelser

Om du vill visa loggar från din programkod i en behållare som du kan använda den [az behållaren loggar](/cli/azure/container#logs) kommando. Men om din behållaren inte distribuerar har du behöver diagnostisk information som tillhandahålls av Azure Behållarinstanser resursprovidern. Om du vill visa händelser för din behållare, kör du följande kommando:

```azurecli-interactive
az container show -n mycontainername -g myresourcegroup
```

Utdata innehåller huvudegenskaper för din behållare, tillsammans med distribution av händelser:

```bash
{
  "containers": [
    {
      "command": null,
      "environmentVariables": [],
      "image": "microsoft/aci-helloworld",
      ...

      "events": [
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:52+00:00",
        "lastTimestamp": "2017-08-03T22:12:52+00:00",
        "message": "Pulling: pulling image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Pulled: Successfully pulled image \"microsoft/aci-helloworld\"",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Created: Created container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      },
      {
        "count": 1,
        "firstTimestamp": "2017-08-03T22:12:55+00:00",
        "lastTimestamp": "2017-08-03T22:12:55+00:00",
        "message": "Started: Started container with id 61602059d6c31529c27609ef4ec0c858b0a96150177fa045cf944d7cf8fbab69",
        "type": "Normal"
      }
    ],
    "name": "helloworld",
      "ports": [
        {
          "port": 80
        }
      ],
    ...
  ]
}
```

## <a name="common-deployment-issues"></a>Vanliga distributionsproblem med

Det finns några vanliga problem för de flesta fel i distributionen.

### <a name="unable-to-pull-image"></a>Det gick inte att pull-bild

Om Azure Behållarinstanser inte att hämta bilden från början, ett nytt försök under en period innan förr eller senare. Om bilden inte kan hämtas, visas händelser som liknar följande:

```bash
"events": [
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:31+00:00",
    "lastTimestamp": "2017-08-03T22:19:31+00:00",
    "message": "Pulling: pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:32+00:00",
    "lastTimestamp": "2017-08-03T22:19:32+00:00",
    "message": "Failed: Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image microsoft/aci-hellowrld:latest not found",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:19:33+00:00",
    "lastTimestamp": "2017-08-03T22:19:33+00:00",
    "message": "BackOff: Back-off pulling image \"microsoft/aci-hellowrld\"",
    "type": "Normal"
  }
]
```

Lös, ta bort behållaren och försök distributionen betalande uppmärksam på att du har angett rätt avbildning.

### <a name="container-continually-exits-and-restarts"></a>Behållaren kontinuerligt avslutas och startas om

För närvarande stöder endast Behållarinstanser som Azure tidskrävande tjänster. Om din behållare körs slutförande och avslutar den automatiskt startar och körs igen. Om det händer visas händelser som de som följer. Observera att behållaren startar och sedan startar om snabbt. Behållaren instanser API innehåller en `retryCount` egenskap som visar hur många gånger en viss behållare har startats om.

```bash
"events": [
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:55+00:00",
    "lastTimestamp": "2017-08-03T22:23:22+00:00",
    "message": "Pulling: pulling image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 5,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:23:23+00:00",
    "message": "Pulled: Successfully pulled image \"alpine\"",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Created: Created container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:57+00:00",
    "lastTimestamp": "2017-08-03T22:21:57+00:00",
    "message": "Started: Started container with id ad2bf9bc51761c5f935260b4bab53b164d52d9cbc045b16afcb26fb4d14d0a70",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Created: Created container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:21:58+00:00",
    "lastTimestamp": "2017-08-03T22:21:58+00:00",
    "message": "Started: Started container with id 7687b9bd15dc01731fa66fc45f6f0241495600602dd03841e559453245e7f70b",
    "type": "Normal"
  },
  {
    "count": 13,
    "firstTimestamp": "2017-08-03T22:21:59+00:00",
    "lastTimestamp": "2017-08-03T22:24:36+00:00",
    "message": "BackOff: Back-off restarting failed container",
    "type": "Warning"
  },
  {
    "count": 1,
    "firstTimestamp": "2017-08-03T22:22:13+00:00",
    "lastTimestamp": "2017-08-03T22:22:13+00:00",
    "message": "Created: Created container with id 72e347e891290e238135e4a6b3078748ca25a1275dbbff30d8d214f026d89220",
    "type": "Normal"
  },
  ...
```

> [!NOTE]
> De flesta behållaren avbildningar för Linux-distributioner kan du ange ett gränssnitt, till exempel bash, som kommandot. Eftersom ett gränssnitt på sin egen inte är en tidskrävande tjänst avsluta omedelbart i dessa behållare och faller inom en omstart loop.

### <a name="container-takes-a-long-time-to-start"></a>Behållaren tar lång tid att starta

Om din behållaren tar lång tid att starta, men till slut lyckas, starta genom att titta på storleken på behållaren avbildningen. Eftersom Azure Behållarinstanser hämtar avbildningen behållare på begäran, relaterat starttiden uppstår direkt till dess storlek.

Du kan visa storleken på avbildningen behållare med Docker CLI:

```bash
docker images
```

Resultat:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
microsoft/aci-helloworld               latest              7f78509b568e        13 days ago         68.1MB
```

Nyckeln till att hålla bildstorleken små är att säkerställa att dina slutliga avbildningen inte innehåller allt som inte krävs vid körning. Det här är ett sätt att göra med [flera steg versioner](https://docs.docker.com/engine/userguide/eng-image/multistage-build/). Flera steg skapar gör det enkelt att se till att den slutliga avbildningen innehåller de artefakter som du behöver för ditt program och inte något av extra innehåll som krävdes vid byggning.

Ett annat sätt att minska effekten av avbildningen pull på din behållaren starttiden är värd för behållaren avbildningen med Azure Container registret i samma region som du tänker använda Azure Container instanser. Detta förkortar nätverkssökvägen behållaren avbildningen måste reser kan avsevärt minska hämtningstiden.

### <a name="resource-not-available-error"></a>Resursen inte tillgängliga fel

På grund av olika regionala resurs läsa in i Azure, kan du få följande fel vid försök att distribuera en behållare instans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av hög belastning i den region där du försöker distribuera resurserna som angetts för din behållaren inte kan allokeras vid den tiden. Använda en eller flera av de följande säkerhetsåtgärder för att lösa problemet.

* Kontrollera distributionsinställningarna behållaren faller inom de parametrar som definierats i [regional tillgänglighet för Azure-Behållarinstanser](container-instances-region-availability.md)
* Ange inställningar för lägre CPU och minne för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle
