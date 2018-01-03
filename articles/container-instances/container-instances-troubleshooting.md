---
title: "Felsöka Azure-Behållarinstanser"
description: "Lär dig hur du felsöker problem med Azure Container instanser"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0b7397e00c2d11c4c7be51421fb40ca6a9fe5779
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="troubleshoot-deployment-issues-with-azure-container-instances"></a>Felsöka distributionsproblem med Azure Container instanser

Den här artikeln visar hur du felsöker problem när du distribuerar behållare till Azure-Behållarinstanser. Här beskrivs också några vanliga problem som kan uppstå.

## <a name="get-diagnostic-events"></a>Hämta diagnostiska händelser

Om du vill visa loggar från din programkod i en behållare som du kan använda den [az behållaren loggar] [ az-container-logs] kommando. Men om din behållaren inte distribuerar har du behöver diagnostisk information som tillhandahålls av Azure Behållarinstanser resursprovidern. Om du vill visa händelser för din behållaren kör den [az behållaren visa] [ az-container-show] kommando:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

Utdata innehåller huvudegenskaper för din behållare, tillsammans med distribution av händelser (visas här trunkerat):

```JSON
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
            "firstTimestamp": "2017-12-21T22:50:49+00:00",
            "lastTimestamp": "2017-12-21T22:50:49+00:00",
            "message": "pulling image \"microsoft/aci-helloworld\"",
            "name": "Pulling",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Successfully pulled image \"microsoft/aci-helloworld\"",
            "name": "Pulled",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Created container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Created",
            "type": "Normal"
          },
          {
            "count": 1,
            "firstTimestamp": "2017-12-21T22:50:59+00:00",
            "lastTimestamp": "2017-12-21T22:50:59+00:00",
            "message": "Started container with id 2677c7fd54478e5adf6f07e48fb71357d9d18bccebd4a91486113da7b863f91f",
            "name": "Started",
            "type": "Normal"
          }
        ],
        "previousState": null,
        "restartCount": 0
      },
      "name": "mycontainer",
      "ports": [
        {
          "port": 80,
          "protocol": null
        }
      ],
      ...
    }
  ],
  ...
}
```

## <a name="common-deployment-issues"></a>Vanliga distributionsproblem med

Det finns några vanliga problem för de flesta fel i distributionen.

## <a name="unable-to-pull-image"></a>Det gick inte att pull-bild

Om Azure Behållarinstanser inte att hämta bilden från början, ett nytt försök under en period innan förr eller senare. Om bilden inte kan hämtas, händelser, t.ex. följande visas i utdata från [az behållaren visa][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

Lös, ta bort behållaren och försök distributionen betalande uppmärksam på att du har angett rätt avbildning.

## <a name="container-continually-exits-and-restarts"></a>Behållaren kontinuerligt avslutas och startas om

Om din behållare körs kan slutföras och startar om automatiskt, du kan behöva ange ett [starta om principen](container-instances-restart-policy.md) av **OnFailure** eller **aldrig**. Om du anger **OnFailure** och fortfarande se kontinuerliga startas om, kan det vara ett problem med program eller skript som körs i en behållare.

Behållaren instanser API innehåller en `restartCount` egenskap. Du kan använda för att kontrollera antalet omstarter för en behållare i [az behållaren visa] [ az-container-show] i Azure CLI 2.0. I följande exempel på utdata (som har trunkerats planeringsaspekter), kan du se den `restartCount` egenskapen i slutet av utdata.

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

Nyckeln till att hålla bildstorleken små är att säkerställa att dina slutliga avbildningen inte innehåller allt som inte krävs vid körning. Det här är ett sätt att göra med [flera steg versioner][docker-multi-stage-builds]. Flera steg skapar gör det enkelt att se till att den slutliga avbildningen innehåller de artefakter som du behöver för ditt program och inte något av extra innehåll som krävdes vid byggning.

Ett annat sätt att minska effekten av avbildningen pull på din behållaren starttiden är värd för behållaren avbildningen med Azure Container registret i samma region som du tänker använda Azure Container instanser. Detta förkortar nätverkssökvägen behållaren avbildningen måste reser kan avsevärt minska hämtningstiden.

## <a name="resource-not-available-error"></a>Resursen inte tillgängliga fel

På grund av olika regionala resurs läsa in i Azure, kan du få följande fel vid försök att distribuera en behållare instans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av hög belastning i den region där du försöker distribuera resurserna som angetts för din behållaren inte kan allokeras vid den tiden. Använda en eller flera av följande säkerhetsåtgärder för att lösa problemet.

* Kontrollera distributionsinställningarna behållaren faller inom de parametrar som definierats i [regional tillgänglighet för Azure-Behållarinstanser](container-instances-region-availability.md)
* Ange inställningar för lägre CPU och minne för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/

<!-- LINKS - Internal -->
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show