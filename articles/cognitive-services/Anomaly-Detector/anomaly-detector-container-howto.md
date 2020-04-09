---
title: Så här installerar och kör du behållare för att använda API för avvikelsedetektor
titleSuffix: Azure Cognitive Services
description: Använd API:et för avvikelsedetektorns avancerade algoritmer för att identifiera avvikelser i tidsseriedata.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fa25d27e99a9516d461a84dde184e2a6412baa0b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875067"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Installera och köra behållare för avvikelsedetektor (förhandsversion)

Avvikelsedetektorn har följande behållarfunktionsfunktioner:

| Funktion | Funktioner |
|--|--|
| Avvikelsedetektor | <li> Identifierar avvikelser när de inträffar i realtid. <li> Identifierar avvikelser i hela datauppsättningen som en batch. <li> Härleder det förväntade normala intervallet för dina data. <li> Stöder justering av känslighetsjustering för avvikelseidentifiering för att bättre passa dina data. |

Mer information om API:erna finns i:
* [Läs mer om API-tjänsten för avvikelsedetektorn](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Du måste uppfylla följande förutsättningar innan du använder behållare för avvikelsedetektor:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du måste dockermotorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Förtrogenhet med Docker | Du bör ha en grundläggande förståelse för Docker-begrepp, till exempel register, databaser, behållare och `docker` behållaravbildningar, samt kunskap om grundläggande kommandon.| 
|Avvikelsedetektor resurs |För att kunna använda dessa behållare måste du ha:<br><br>En Azure _Anomaly Detector-resurs_ för att hämta den associerade API-nyckeln och slutpunkts-URI. Båda värdena är tillgängliga på Azure-portalens **avvikelsedetektoröversikt** och nycklar och krävs för att starta behållaren.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på sidan **Nycklar**<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **sidan Översikt**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först fylla i och skicka formuläret Begäran om begäran om begäran om begäran om åtkomst till behållaren för [avvikelsedetektorbegäran.](https://aka.ms/adcontainer)

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs de minsta och rekommenderade CPU-kärnorna och minnet som ska allokeras för behållare för avvikelsedetektor.

| QPS(Frågor per sekund) | Minimum | Rekommenderas |
|-----------|---------|-------------|
| 10 QPS (10 QPS) | 4-kärniga, 1 GB minne | 8 kärnor 2 GB-minne |
| 20 QPS (20 QPS) | 8 kärnor, 2 GB minne | 16 kärnor 4 GB minne |

Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Kärna och minne `--cpus` motsvarar `--memory` och inställningar, som används `docker run` som en del av kommandot.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med`docker pull`

Använd [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) kommandot för att hämta en behållaravbildning.

| Container | Lagringsplats |
|-----------|------------|
| kognitiv-tjänster-anomali-detektor | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker dra för Anomali Detector behållaren

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Så här använder du behållaren

När behållaren finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de faktureringsinställningar som krävs. Fler [exempel](anomaly-detector-container-configuration.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga.
1. [Fråga behållarens förutsägelseslutpunkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd [kommandot docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Se [samla in nödvändiga parametrar](#gathering-required-parameters) för `{ENDPOINT_URI}` information `{API_KEY}` om hur du får och värden.

[Exempel](anomaly-detector-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en behållare för avvikelsedetektor från behållarbilden
* Allokerar en CPU-kärna och 4 GIGABYTE (GB) minne
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn. 

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Köra flera behållare på samma värd

Om du tänker köra flera behållare med exponerade portar, se till att köra varje behållare med en annan port. Kör till exempel den första behållaren på port 5000 och den andra behållaren på port 5001.

Ersätt `<container-registry>` och `<container-name>` med värdena för de behållare du använder. Dessa behöver inte vara samma behållare. Du kan ha behållaren avvikelsedetektor och LUIS-behållaren som körs på värdvärden tillsammans eller så kan du ha flera avvikelsedetektorbehållare igång. 

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Kör den andra behållaren på port 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Varje efterföljande behållare bör vara på en annan port. 

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelseslutpunkt

Behållaren tillhandahåller REST-baserade slutpunkts-API:er för frågeförutsägels. 

Använd värden, http://localhost:5000för behållar-API:er.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en [utdatamontering](anomaly-detector-container-configuration.md#mount-settings) och loggning aktiverad genererar behållaren loggfiler som är användbara för att felsöka problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Behållaren för avvikelsedetektor skickar faktureringsinformation till Azure med hjälp av en _avvikelsedetektorresurs_ på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Konfigurera behållare](anomaly-detector-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöde för att hämta, installera och köra behållare för avvikelsedetektor. Sammanfattningsvis:

* Avvikelsedetektor ger en Linux-behållare för Docker, kapsla in avvikelseidentifiering med batch vs streaming, förväntad intervall inferens och känslighetsjustering.
* Behållaravbildningar hämtas från ett privat Azure Container-register som är dedikerat för förhandsgranskning av behållare.
* Behållaravbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i behållare för avvikelsedetektor genom att ange behållarens värd-URI.
* Du måste ange faktureringsinformation när du instansierar en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata (t.ex. tidsseriedata som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](anomaly-detector-container-configuration.md) för konfigurationsinställningar
* [Distribuera en avvikelsedetektorbehållare till Azure Container-instanser](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Läs mer om API-tjänsten för avvikelsedetektorn](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
