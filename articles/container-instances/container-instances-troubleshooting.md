---
title: Felsöka Azure Container Instances
description: Lär dig hur du felsöker problem med Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bfa616fb16470a3543f8c981a0104f6bda24cf4d
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823490"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Felsöka vanliga problem i Azure Container Instances

Den här artikeln visar hur du felsöker vanliga problem för att hantera och distribuera behållare till Azure Container Instances.

## <a name="naming-conventions"></a>Namngivningskonventioner

När du definierar din container-specifikationen kräver vissa parametrar infört naming begränsningar. Nedan visas en tabell med särskilda krav för behållaren gruppegenskaper. Mer information om namngivningskonventioner för Azure finns i [namngivningskonventioner] [ azure-name-restrictions] i Azure Architecture Center.

| Scope | Längd | Skiftläge | Giltiga tecken | Föreslagna mönster | Exempel |
| --- | --- | --- | --- | --- | --- | --- |
| Namnet på behållargruppen | 1-64 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst utom det första eller sista tecknet |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Containerns namn | 1-64 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst utom det första eller sista tecknet |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Behållarportar | Mellan 1 och 65535 |Integer |Heltal mellan 1 och 65535 |`<port-number>` |`443` |
| DNS-namnetikett | 5-63 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst utom det första eller sista tecknet |`<name>` |`frontend-site1` |
| Miljövariabel | 1–63 |Skiftlägesokänsligt |Alfanumeriskt och understreck (_) var som helst utom det första eller sista tecknet |`<name>` |`MY_VARIABLE` |
| Volymnamn | 5-63 |Skiftlägesokänsligt |Gemena bokstäver och siffror och bindestreck var som helst utom det första eller sista tecknet. Får inte innehålla två bindestreck. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Operativsystemsversionen av avbildningen som inte stöds

Om du anger en avbildning som Azure Container Instances inte stöder en `OsVersionNotSupported` fel returneras. Fel som liknar följande var `{0}` är namnet på den avbildning som du har försökt att distribuera:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Det här felet uppstår oftast när distribuera Windows-avbildningar som baseras på en Halvårskanal kanal SAC ()-versionen. Till exempel Windows version 1709 och 1803 är SAC versioner och generera felet på distributionen.

Azure Container Instances stöder för närvarande baseras bara på Windows-avbildningar i **Windows Server 2016 Long-Term Servicing kanal (LTSC)** versionen. Om du vill åtgärda problemet när du distribuerar Windows-behållare, alltid distribuera Windows Server 2016 LTSC-baserade avbildningar. Bilder baserat på Windows Server 2019 (LTSC) stöds inte.

Mer information om LTSC och SAC versioner av Windows finns i [översikt över Windows Server Halvårskanal][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Det går inte att pull-bild

Om Azure Container Instances inledningsvis inte att hämta din avbildning, ett nytt försök görs för en viss tidsperiod. Om avbildningen pull-åtgärden fortfarande misslyckas, ACI misslyckas så småningom distributionen och du kan se en `Failed to pull image` fel.

Lös problemet genom att ta bort behållarinstansen och försök din distribution. Kontrollera att avbildningen finns i registret och att du har angett rätt avbildningens namn.

Om avbildningen inte kan hämtas, händelser som liknar följande visas i utdata från [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"microsoft/aci-helloworld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"microsoft/aci-helloworld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"microsoft/aci-helloworld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Behållaren kontinuerligt avslutas och startas om (inga tidskrävande process)

Behållargrupper som standard en [omstartsprincip](container-instances-restart-policy.md) av **alltid**, så att behållare i behållargruppen Starta alltid om när de körs kan slutföras. Du kan behöva ändra detta till **OnFailure** eller **aldrig** om du planerar att köra uppgiften-baserade behållare. Om du anger **OnFailure** och fortfarande se kontinuerliga startas om, det kan finnas ett problem med programmet eller skriptet som körs i din behållare.

När du kör behållargrupper utan tidskrävande processer kan du se upprepade avslutas och startas om med bilder, till exempel Ubuntu eller Alpine. Ansluta via [EXEC](container-instances-exec.md) fungerar inte som behållaren har ingen process att hålla det alive. Inkludera ett start-kommando som liknar följande med din grupp behållardistribution att hålla den behållare som körs för att lösa problemet.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image microsoft/windowsservercore:ltsc2016
 --command-line "ping -t localhost"
```

Container Instances API och Azure portal innehåller ett `restartCount` egenskapen. Du kan använda för att kontrollera antalet omstarter för en behållare i [az container show] [ az-container-show] i Azure CLI. I följande exempel utdata (som har trunkerats av utrymmesskäl) ser du den `restartCount` egenskapen i slutet av utdata.

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
> De flesta behållaravbildningar för Linux-distributioner kan du ange ett gränssnitt, till exempel bash, som kommandot. Eftersom ett gränssnitt på egen hand inte är en tidskrävande-tjänst, de här behållarna omedelbart Avsluta och delas in i en loop för omstart när den konfigurerats med standard **alltid** omstartsprincip.

## <a name="container-takes-a-long-time-to-start"></a>Behållaren tar lång tid att starta

De två främsta faktorerna som bidrar till starttiden för behållare i Azure Container Instances är:

* [Avbildningens storlek](#image-size)
* [Platsen för](#image-location)

Windows-avbildningar har [ytterligare överväganden](#cached-windows-images).

### <a name="image-size"></a>Avbildningens storlek

Om din behållare tar lång tid att starta, men till slut lyckas, starta genom att titta på storleken på din behållaravbildning. Eftersom Azure Container Instances hämtar behållaravbildningen på begäran, relaterade på starttiden som du ser direkt till dess storlek.

Du kan visa storleken på din behållaravbildning med hjälp av den `docker images` i Docker CLI:

```console
$ docker images
REPOSITORY                  TAG       IMAGE ID        CREATED        SIZE
microsoft/aci-helloworld    latest    7f78509b568e    13 days ago    68.1MB
```

Nyckeln till att hålla bildstorleken små är att säkerställa att din slutliga avbildningen inte innehåller något som inte är nödvändiga vid körning. Det här är ett sätt att göra med [flerstegs versioner][docker-multi-stage-builds]. Flera steg skapar gör det enkelt att säkerställa att den slutliga avbildningen innehåller endast de artefakter som du behöver för ditt program och inte någon av extra innehåll som krävdes vid Byggtiden.

### <a name="image-location"></a>Platsen för

Ett annat sätt att minska effekten av avbildningen pull på starttiden för din behållare är att vara värd för behållaravbildningen i [Azure Container Registry](/azure/container-registry/) i samma region som du tänker distribuera behållarinstanser. Detta förkortar nätverkssökvägen som behållaravbildningen behöver reser avsevärt förkorta tid.

### <a name="cached-windows-images"></a>Cachelagrade Windows-avbildningar

Azure Container Instances använder en cachelagringsmekanism för att snabbt behållaren starttiden för bilder baserat på vanliga Windows och Linux-avbildningar. En detaljerad lista över cachelagrade avbildningar och taggar, använda den [lista cachelagrade avbildningar] [ list-cached-images] API.

För att säkerställa den snabbaste starttiden för Windows-behållare, Använd en av de **tre senaste** versioner av följande **två avbildningar** som basavbildningen:

* [Windows Server Core 2016] [ docker-hub-windows-core] (endast LTSC)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Beredskap för Windows-behållare långsamt nätverk

Inledande skapats har Windows-behållare ingen inkommande eller utgående anslutning för upp till 30 sekunder (eller längre i sällsynta fall). Om ditt behållarprogram behöver en Internetanslutning, lägga till en förskjutning och logik för omprövning för att tillåta 30 sekunder för att upprätta en Internet-anslutning. Efter den första konfigurationen återupptas nätverk för behållare på rätt sätt.

## <a name="resource-not-available-error"></a>Resursen inte tillgänglig fel

På grund av varierande regionbaserad resurs läsa in i Azure, kan du få följande fel vid försök att distribuera en behållarinstans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av hög belastning i den region där du vill distribuera de resurser som angetts för din behållare inte kan allokeras vid den tidpunkten. Använda en eller flera av följande åtgärderna för att lösa problemet.

* Kontrollera behållarinställningarna för distributionen faller inom de parametrar som definierats i [kvoter och regiontillgänglighet för Azure Container Instances](container-instances-quotas.md#region-availability)
* Ange inställningar för lägre CPU och minne för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Det går inte att ansluta till den underliggande Docker API eller köra Privilegierade behållare

Azure Container Instances exponerar inte direkt åtkomst till den underliggande infrastrukturen som är värd för behållargrupper. Detta inkluderar åtkomst till Docker-API som körs på den behållare värden och Privilegierade behållare som körs. Om du behöver Docker interaktion kan kontrollera den [referensdokumentation för REST](https://aka.ms/aci/rest) ACI API stöder. Om det inte finns något saknas, skicka en begäran på den [ACI feedback-forum](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>IP-adresser är kanske inte tillgänglig på grund av felaktigt portar

Azure Container Instances stöder för närvarande inte port mappning som med vanlig docker konfiguration, men den här snabbkorrigeringen är på översikten. Om du hittar IP-adresser inte är tillgängliga när du tycker att det ska vara, kan du kontrollera att du har konfigurerat en behållaravbildning för att lyssna på samma portar som du exponera i din behållargrupp med den `ports` egenskapen.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hämta behållarloggarna och händelser](container-instances-get-logs.md) för att felsöka dina behållare.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/r/microsoft/windowsservercore/
[docker-hub-windows-nano]: https://hub.docker.com/r/microsoft/nanoserver/

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
