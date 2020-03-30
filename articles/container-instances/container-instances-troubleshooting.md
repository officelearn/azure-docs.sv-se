---
title: Felsök vanliga problem
description: Lär dig hur du felsöker vanliga problem när du distribuerar, kör eller hanterar Azure Container Instances
ms.topic: article
ms.date: 09/25/2019
ms.custom: mvc
ms.openlocfilehash: 07cdbfb27aaf9076e726ebda861ed24996e10135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533384"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Felsöka vanliga problem med Azure Container Instances

Den här artikeln visar hur du felsöker vanliga problem för att hantera eller distribuera behållare till Azure Container Instances. Se även [Vanliga frågor](container-instances-faq.md)och svar .

Om du behöver ytterligare support kan du se tillgängliga **supportalternativ för hjälp +** i [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problem under distribution av containergrupp
### <a name="naming-conventions"></a>Namngivningskonventioner

När du definierar behållarspecifikationen kräver vissa parametrar att namngivningsbegränsningar följs. Nedan finns en tabell med specifika krav för behållare gruppegenskaper. Mer information om Azure-namngivningskonventioner finns i [Namngivningskonventioner][azure-name-restrictions] i Azure Architecture Center.

| Omfång | Längd | Skiftläge | Giltiga tecken | Föreslaget mönster | Exempel |
| --- | --- | --- | --- | --- | --- |
| Namn på containergrupp | 1-64 |Skiftlägesokänsligt |Alfanumeriska och bindestreck var som helst utom det första eller sista tecknet |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Containerns namn | 1-64 |Skiftlägesokänsligt |Alfanumeriska och bindestreck var som helst utom det första eller sista tecknet |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Behållarportar | Mellan 1 och 65535 |Integer |Heltal mellan 1 och 65535 |`<port-number>` |`443` |
| DNS-namnetikett | 5-63 |Skiftlägesokänsligt |Alfanumeriska och bindestreck var som helst utom det första eller sista tecknet |`<name>` |`frontend-site1` |
| Miljövariabel | 1–63 |Skiftlägesokänsligt |Alfanumeriskt och understreck (_) var som helst utom det första eller sista tecknet |`<name>` |`MY_VARIABLE` |
| Volymnamn | 5-63 |Skiftlägesokänsligt |Gemener och siffror och bindestreck var som helst utom det första eller sista tecknet. Det går inte att innehålla två på varandra följande bindestreck. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>OS-versionen av bilden stöds inte

Om du anger en avbildning som Azure Container `OsVersionNotSupported` Instances inte stöder returneras ett fel. Felet liknar följande, var `{0}` är namnet på den bild du försökte distribuera:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Det här felet uppstår oftast vid distribution av Windows-avbildningar som baseras på halvårsversionen av kanal 1709 eller 1803, som inte stöds. Information om Windows-avbildningar som stöds i Azure Container Instances finns [i Vanliga frågor](container-instances-faq.md#what-windows-base-os-images-are-supported)och svar .

### <a name="unable-to-pull-image"></a>Det går inte att hämta bilden

Om Azure Container Instances inledningsvis inte kan dra din avbildning, försöker den under en viss tid. Om avbildningsdragningen fortsätter att misslyckas misslyckas ACI till `Failed to pull image` distributionen och du kan se ett fel.

Lös problemet genom att ta bort behållarinstansen och försöka igen med distributionen. Kontrollera att avbildningen finns i registret och att du har skrivit bildnamnet korrekt.

Om bilden inte kan dras visas händelser som följande i utdata från [az-behållaren:][az-container-show]

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Resursen är inte tillgänglig fel

På grund av varierande regional resursbelastning i Azure kan följande felmeddelande visas när du försöker distribuera en behållarinstans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av tung belastning i den region där du försöker distribuera, kan de resurser som angetts för din behållare inte allokeras vid den tidpunkten. Använd ett eller flera av följande begränsningssteg för att lösa problemet.

* Verifiera att dina inställningar för behållardistribution omfattas av de parametrar som definierats i [regiontillgänglighet för Azure Container-instanser](container-instances-region-availability.md)
* Ange lägre CPU- och minnesinställningar för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle

## <a name="issues-during-container-group-runtime"></a>Problem under körning i containergrupp
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Behållaren avslutas och startas om kontinuerligt (ingen tidskrävande process)

Behållargrupper som standard är en [omstartsprincip](container-instances-restart-policy.md) **för Alltid**, så behållare i behållargruppen startas alltid om när de har körts till slutförandet. Du kan behöva ändra detta till **OnFailure** eller **Aldrig** om du tänker köra aktivitetsbaserade behållare. Om du anger **OnFailure** och fortfarande ser kontinuerliga omstarter kan det finnas ett problem med programmet eller skriptet som körs i behållaren.

När du kör behållargrupper utan tidskrävande processer kan du se upprepade utgångar och omstarter med bilder som Ubuntu eller Alpine. Anslutning via [EXEC](container-instances-exec.md) kommer inte att fungera eftersom behållaren inte har någon process som håller den vid liv. LÃ¶s problemet genom att inkludera ett startkommando som ã¶¥ av ã¥l av med distributionen av behållargruppen och lÃ¶r att behålla behållaren.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

API:et för behållarinstanser och Azure-portalen innehåller en `restartCount` egenskap. Om du vill kontrollera antalet omstarter för en behållare kan du använda kommandot [az container show][az-container-show] i Azure CLI. I följande exempelutdata (som har trunkerats för korthet) kan du se egenskapen `restartCount` i slutet av utdata.

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
> De flesta behållaravbildningar för Linux-distributioner anger ett skal, till exempel bash, som standardkommando. Eftersom ett skal på egen hand inte är en tidskrävande tjänst, dessa behållare omedelbart avsluta och faller i en omstart slinga när den konfigureras med standard **Alltid** starta om principen.

### <a name="container-takes-a-long-time-to-start"></a>Behållaren tar lång tid att starta

De tre primära faktorer som bidrar till att behållarstarttiden i Azure Container Instances är:

* [Bildstorlek](#image-size)
* [Bildplats](#image-location)
* [Cachelagrade bilder](#cached-images)

Windows-bilder har [ytterligare överväganden](#cached-images).

#### <a name="image-size"></a>Bildstorlek

Om behållaren tar lång tid att starta, men så småningom lyckas, börja med att titta på storleken på din behållaravbildning. Eftersom Azure Container Instances hämtar din behållaravbildning på begäran är starttiden du ser direkt relaterad till dess storlek.

Du kan visa storleken på behållaravbildningen `docker images` med kommandot i Docker CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Nyckeln till att hålla bildstorlekar små är att se till att den slutliga bilden inte innehåller något som inte krävs vid körning. Ett sätt att göra detta är med [flera steg bygger][docker-multi-stage-builds]. Flerstegsversioner gör det enkelt att se till att den slutliga avbildningen bara innehåller de artefakter du behöver för ditt program, och inte något av det extra innehåll som krävdes vid byggtiden.

#### <a name="image-location"></a>Bildplats

Ett annat sätt att minska effekten av avbildningsdragningen på behållarens starttid är att vara värd för behållaravbildningen i [Azure Container Registry](/azure/container-registry/) i samma region där du tänker distribuera behållarinstansar. Detta förkortar nätverkssökvägen som behållaravbildningen behöver för att färdas, vilket avsevärt förkortar nedladdningstiden.

#### <a name="cached-images"></a>Cachelagrade bilder

Azure Container Instances använder en cachelagringsmekanism för att påskynda starttiden för behållaren `servercore:1809`för avbildningar som bygger på vanliga [Windows-basavbildningar,](container-instances-faq.md#what-windows-base-os-images-are-supported)inklusive `nanoserver:1809`, `servercore:ltsc2019`och . Vanliga Linux-avbildningar `ubuntu:1604` `alpine:3.6` som och cachelagras också. Om du vill ha en uppdaterad lista över cachelagrade bilder och taggar använder du [API:et för cachelagrade avbildningar.][list-cached-images]

> [!NOTE]
> Användning av Windows Server 2019-baserade avbildningar i Azure Container Instances är i förhandsversion.

#### <a name="windows-containers-slow-network-readiness"></a>Windows-behållare långsam nätverksberedskap

När Windows-behållarna skapas första skapelsen kan de inte ha någon inkommande eller utgående anslutning på upp till 30 sekunder (eller längre, i sällsynta fall). Om behållarprogrammet behöver en Internetanslutning lägger du till fördröjnings- och återförsökslogik för att tillåta 30 sekunder för att upprätta Internet-anslutning. Efter den första installationen bör behållarnätverket återupptas på rätt sätt.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Det går inte att ansluta till underliggande Docker API eller köra privilegierade behållare

Azure Container Instances visar inte direkt åtkomst till den underliggande infrastrukturen som är värd för behållargrupper. Detta inkluderar åtkomst till Docker API som körs på behållarens värd och kör privilegierade behållare. Om du behöver Docker-interaktion läser du [REST-referensdokumentationen](https://aka.ms/aci/rest) för att se vad ACI API stöder. Om det är något som [saknas,](https://aka.ms/aci/feedback)skicka en begäran på ACI feedback forum .

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>IP-adress för behållargrupp kanske inte är tillgänglig på grund av inkompatibla portar

Azure Container Instances stöder ännu inte portmappning som med vanlig dockerkonfiguration. Om du hittar en behållargrupps IP-adress inte är tillgänglig när du anser att den borde vara, kontrollerar du `ports` att du har konfigurerat behållaravbildningen för att lyssna på samma portar som du exponerar i din behållargrupp med egenskapen.

Om du vill bekräfta att Azure Container Instances kan lyssna på den port som `aci-helloworld` du konfigurerat i behållaravbildningen testar du en distribution av avbildningen som exponerar porten. Kör även `aci-helloworld` appen så att den lyssnar på porten. `aci-helloworld`accepterar en valfri `PORT` miljövariabel för att åsidosätta standardporten 80 som den lyssnar på. Om du till exempel vill testa port 9000 anger du [miljövariabeln](container-instances-environment-variables.md) när du skapar behållargruppen:

1. Ställ in behållargruppen för att exponera port 9000 och skicka portnumret som värdet för miljövariabeln. Exemplet är formaterat för bash-skalet. Om du föredrar ett annat skal, till exempel PowerShell eller Kommandotolk, måste du justera variabel tilldelning därefter.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Leta reda på IP-adressen för behållargruppen i kommandoutdata för `az container create`. Leta efter värdet på **ip**. 
1. När behållaren har etablerats bläddrar du till IP-adressen och porten för `192.0.2.0:9000`behållarappen i webbläsaren, till exempel: . 

    Du bör se "Välkommen till Azure Container Instances!" meddelande som visas av webbappen.
1. När du är klar med behållaren `az container delete` tar du bort den med kommandot:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hämtar behållarloggar och händelser](container-instances-get-logs.md) för att felsöka dina behållare.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
