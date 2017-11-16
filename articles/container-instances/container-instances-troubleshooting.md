---
title: "Felsöka Azure-Behållarinstanser"
description: "Lär dig hur du felsöker problem med Azure Container instanser"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: mmacy
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 78bd45f7f71fd25e351d4e9b922a6a3f171437fd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Felsöka distributionsproblem med Azure Container instanser

Den här artikeln visar hur du felsöker problem när du distribuerar behållare till Azure-Behållarinstanser. Här beskrivs också några vanliga problem som kan uppstå.

## <a name="get-diagnostic-events"></a>Hämta diagnostiska händelser

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

## <a name="unable-to-pull-image"></a>Det gick inte att pull-bild

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

## <a name="container-continually-exits-and-restarts"></a>Behållaren kontinuerligt avslutas och startas om

Om din behållare körs kan slutföras och startar om automatiskt, du kan behöva ange ett [starta om principen](container-instances-restart-policy.md) av **OnFailure** eller **aldrig**. Om du anger **OnFailure** och fortfarande se kontinuerliga startas om, kan det vara ett problem med program eller skript som körs i en behållare.

Behållaren instanser API innehåller en `restartCount` egenskap. Du kan använda för att kontrollera antalet omstarter för en behållare i [az behållaren visa](/cli/azure/container#az_container_show) i Azure CLI 2.0. I följande exempel på utdata (som har trunkerats planeringsaspekter), kan du se den `restartCount` egenskapen i slutet av utdata.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> De flesta behållaren avbildningar för Linux-distributioner kan du ange ett gränssnitt, till exempel bash, som kommandot. Eftersom ett gränssnitt på sin egen inte är en tidskrävande tjänst behållarna omedelbart Avsluta och delas in i en omstart loop konfigurerades med **alltid** starta om principen.

## <a name="container-takes-a-long-time-to-start"></a>Behållaren tar lång tid att starta

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

## <a name="resource-not-available-error"></a>Resursen inte tillgängliga fel

På grund av olika regionala resurs läsa in i Azure, kan du få följande fel vid försök att distribuera en behållare instans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av hög belastning i den region där du försöker distribuera resurserna som angetts för din behållaren inte kan allokeras vid den tiden. Använda en eller flera av de följande säkerhetsåtgärder för att lösa problemet.

* Kontrollera distributionsinställningarna behållaren faller inom de parametrar som definierats i [regional tillgänglighet för Azure-Behållarinstanser](container-instances-region-availability.md)
* Ange inställningar för lägre CPU och minne för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle
