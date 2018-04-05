---
title: Felsöka Azure-Behållarinstanser
description: Lär dig hur du felsöker problem med Azure Container instanser
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: ac7ddbccdef736bce941716883cf821a0f5c1f34
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="troubleshoot-container-and-deployment-issues-in-azure-container-instances"></a>Felsökning av problem med behållare och distribution i Azure Container instanser

Den här artikeln visar hur du felsöker problem när du distribuerar behållare till Azure-Behållarinstanser. Här beskrivs också några vanliga problem som kan uppstå.

## <a name="view-logs-and-stream-output"></a>Visa loggfiler och strömmad utdata

När du har en felaktigt behållare startar genom att visa loggar med [az behållaren loggar][az-container-logs], och dess standard out och standardfel med [az behållaren bifoga] [az-container-attach].

### <a name="view-logs"></a>Visa loggar

Om du vill visa loggar från din programkod i en behållare som du kan använda den [az behållaren loggar] [ az-container-logs] kommando.

Nedan visas utdata från exempel uppgiftsbaserade behållare i [köra en av aktivitet som ACI](container-instances-restart-policy.md)efter att ha matas den en ogiltig URL för att bearbeta:

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
Traceback (most recent call last):
  File "wordcount.py", line 11, in <module>
    urllib.request.urlretrieve (sys.argv[1], "foo.txt")
  File "/usr/local/lib/python3.6/urllib/request.py", line 248, in urlretrieve
    with contextlib.closing(urlopen(url, data)) as fp:
  File "/usr/local/lib/python3.6/urllib/request.py", line 223, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/local/lib/python3.6/urllib/request.py", line 532, in open
    response = meth(req, response)
  File "/usr/local/lib/python3.6/urllib/request.py", line 642, in http_response
    'http', request, response, code, msg, hdrs)
  File "/usr/local/lib/python3.6/urllib/request.py", line 570, in error
    return self._call_chain(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 504, in _call_chain
    result = func(*args)
  File "/usr/local/lib/python3.6/urllib/request.py", line 650, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found
```

### <a name="attach-output-streams"></a>Ansluta utdataströmmar

Den [az behållaren bifoga] [ az-container-attach] kommandot ger diagnostisk information under behållaren start. När behållaren har börjat strömmas STDOUT och STDERR till din lokala konsolen.

Här är till exempel utdata från uppgiftsbaserade behållare i [köra en av aktivitet som ACI](container-instances-restart-policy.md)efter att ha angett en giltig URL för en stor textfil att bearbeta:

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Unknown'...
Container 'mycontainer' is in state 'Waiting'...
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2018-03-09 23:21:33+00:00) pulling image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Successfully pulled image "microsoft/aci-wordcount:latest"
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Created container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618
(count: 1) (last timestamp: 2018-03-09 23:21:49+00:00) Started container with id e495ad3e411f0570e1fd37c1e73b0e0962f185aa8a7c982ebd410ad63d238618

Start streaming logs:
[('the', 22979),
 ('I', 20003),
 ('and', 18373),
 ('to', 15651),
 ('of', 15558),
 ('a', 12500),
 ('you', 11818),
 ('my', 10651),
 ('in', 9707),
 ('is', 8195)]
```

## <a name="get-diagnostic-events"></a>Hämta diagnostiska händelser

Om det inte går att distribuera har din behållare, måste du diagnostisk information som tillhandahålls av Azure Behållarinstanser resursprovidern. Om du vill visa händelser för din behållaren kör den [az behållaren visa] [ az-container-show] kommando:

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

I följande avsnitt beskrivs vanliga problem för de flesta fel i behållaren distribution:

* [Bildversionen stöds inte](#image-version-not-supported)
* [Det gick inte att pull-bild](#unable-to-pull-image)
* [Behållaren kontinuerligt avslutas och startas om](#container-continually-exits-and-restarts)
* [Behållaren tar lång tid att starta](#container-takes-a-long-time-to-start)
* [”Resursen är inte tillgänglig” fel](#resource-not-available-error)

## <a name="image-version-not-supported"></a>Bildversionen stöds inte

Om du anger en avbildning som Behållarinstanser som Azure inte stöder en `ImageVersionNotSupported` fel returneras. Värdet för felet är `The version of image '{0}' is not supported.`, och för närvarande gäller för Windows 1709 bilder. Om du vill undvika det här problemet använder du en LTS Windows-avbildning. Stöd för Windows 1709 bilder pågår.

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

Det finns två primära faktorer som bidrar till behållaren starttiden på Azure-instanser som behållare:

* [Avbildningens storlek](#image-size)
* [Platsen för avbildning](#image-location)

Windows-avbildningar har [ytterligare överväganden](#use-recent-windows-images).

### <a name="image-size"></a>Avbildningens storlek

Om din behållaren tar lång tid att starta, men till slut lyckas, starta genom att titta på storleken på behållaren avbildningen. Eftersom Azure Behållarinstanser hämtar avbildningen behållare på begäran, relaterat starttiden uppstår direkt till dess storlek.

Du kan visa storleken på behållaren avbildningen med hjälp av `docker images` i Docker CLI:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Nyckeln till att hålla bildstorleken små är att säkerställa att dina slutliga avbildningen inte innehåller allt som inte krävs vid körning. Det här är ett sätt att göra med [flera steg versioner][docker-multi-stage-builds]. Flera steg skapar gör det enkelt att se till att den slutliga avbildningen innehåller de artefakter som du behöver för ditt program och inte något av extra innehåll som krävdes vid byggning.

### <a name="image-location"></a>Platsen för avbildning

Ett annat sätt att minska effekten av avbildningen pull på din behållaren starttiden är värd för behållaren bilden i [Azure Container registret](/azure/container-registry/) i samma region som du tänker distribuera behållarinstanser. Detta förkortar nätverkssökvägen behållaren avbildningen måste reser kan avsevärt minska hämtningstiden.

### <a name="use-recent-windows-images"></a>Använd de senaste Windows-avbildningar

Azure Behållarinstanser använder en cachelagringsmekanism för att hjälpa hastighet behållaren starttiden för bilder baserat på vissa Windows-avbildningar.

För att säkerställa snabbaste starttiden för Windows-behållare, kan du använda en av de **tre senaste** versioner av följande **två avbildningar** som basavbildningen:

* [Windows Server 2016] [ docker-hub-windows-core] (LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

## <a name="resource-not-available-error"></a>Resursen inte tillgängliga fel

På grund av olika regionala resurs läsa in i Azure, kan du få följande fel vid försök att distribuera en behållare instans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av hög belastning i den region där du försöker distribuera resurserna som angetts för din behållaren inte kan allokeras vid den tiden. Använda en eller flera av följande säkerhetsåtgärder för att lösa problemet.

* Kontrollera distributionsinställningarna behållaren faller inom de parametrar som definierats i [kvoter och regional tillgänglighet för Azure-Behållarinstanser](container-instances-quotas.md#region-availability)
* Ange inställningar för lägre CPU och minne för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az_container_attach
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show