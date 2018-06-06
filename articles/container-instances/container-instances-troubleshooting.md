---
title: Felsöka Azure-Behållarinstanser
description: Lär dig hur du felsöker problem med Azure Container instanser
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/14/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 39c43c079ea4d10686bd656ba2d451ff42aac9f6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700238"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Felsök vanliga problem i Azure Container instanser

Den här artikeln visar hur du felsöker vanliga problem för att hantera och distribuera behållare till Behållarinstanser som Azure.

## <a name="naming-conventions"></a>Namngivningskonventioner

När du definierar din behållaren specification kräver vissa parametrar att namnge begränsningar. Nedan visas en tabell med särskilda krav för behållaren egenskaper.
Mer information om namngivningskonventioner för Azure finns [namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) i Azure-arkitektur Center.

| Omfång | Längd | Skiftläge | Giltiga tecken | Föreslagna mönster | Exempel |
| --- | --- | --- | --- | --- | --- | --- |
| Namn på behållare | 1-64 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst utom det första eller sista tecknet |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Behållarens namn | 1-64 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst utom det första eller sista tecknet |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Behållaren portar | Mellan 1 och 65535 |Integer |Heltal mellan 1 och 65535 |`<port-number>` |`443` |
| DNS-namnetikett | 5-63 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst utom det första eller sista tecknet |`<name>` |`frontend-site1` |
| Miljövariabel | 1–63 |Skiftlägesokänsligt |Alfanumeriskt och '_' chracter var som helst utom det första eller sista tecknet |`<name>` |`MY_VARIABLE` |
| Volymnamn | 5-63 |Skiftlägesokänsligt |Gemena bokstäver, siffror och bindestreck var som helst utom det första eller sista tecknet. Får inte innehålla två bindestreck efter varandra. |`<name>` |`batch-output-volume` |

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

Windows-avbildningar har [ytterligare överväganden](#cached-windows-images).

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

### <a name="cached-windows-images"></a>Cachelagrade Windows-avbildningar

Azure Behållarinstanser använder en cachelagringsmekanism för att hjälpa hastighet behållaren starttiden för bilder baserat på vissa Windows-avbildningar.

För att säkerställa snabbaste starttiden för Windows-behållare, kan du använda en av de **tre senaste** versioner av följande **två avbildningar** som basavbildningen:

* [Windows Server 2016] [ docker-hub-windows-core] (LTS)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Windows-behållare långsamt nätverk beredskap

Windows-behållare kan tillkomma ingen inkommande eller utgående anslutning för upp till 5 sekunder på Skapa. Efter att installationen ska behållaren nätverk återuppta på lämpligt sätt.

## <a name="resource-not-available-error"></a>Resursen inte tillgängliga fel

På grund av olika regionala resurs läsa in i Azure, kan du få följande fel vid försök att distribuera en behållare instans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av hög belastning i den region där du försöker distribuera resurserna som angetts för din behållaren inte kan allokeras vid den tiden. Använda en eller flera av följande säkerhetsåtgärder för att lösa problemet.

* Kontrollera distributionsinställningarna behållaren faller inom de parametrar som definierats i [kvoter och regional tillgänglighet för Azure-Behållarinstanser](container-instances-quotas.md#region-availability)
* Ange inställningar för lägre CPU och minne för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [hämta loggar för behållaren & händelser](container-instances-get-logs.md) för felsökning av behållarna.

<!-- LINKS - External -->
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show