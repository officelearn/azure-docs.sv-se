---
title: Installera och kör behållare - Avvikelseidentifiering detektor
titleSuffix: Azure Cognitive Services
description: Använd avancerade algoritmer för Avvikelseidentifiering detektor API för att identifiera avvikelser i tidsseriedata.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 05/07/2019
ms.author: aahi
ms.openlocfilehash: cc82e091ae4c033bda7f1d91c9aed36bb081de88
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233612"
---
# <a name="install-and-run-anomaly-detector-containers"></a>Installera och köra Avvikelseidentifiering detektor behållare

Avvikelseidentifiering detektor har följande behållare: 

|Funktion|Funktioner|
|-|-|
|Avvikelseidentifiering detektor| <li> Identifierar avvikelser allteftersom de sker i realtid. <li> Identifierar avvikelser i hela datauppsättningen som en batch. <li> Härleder det förväntade normala intervallet av dina data. <li> Har stöd för avvikelseidentifiering identifiering känslighet justering bättre passar dina data. |

Detaljerad information om API: erna finns:
* [Mer information om Avvikelseidentifiering detektor API-tjänsten](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste uppfylla följande krav innan du använder Avvikelseidentifiering detektor behållare:

|Obligatoriskt|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **På Windows**, Docker måste också konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.| 
|Avvikelseidentifiering detektor resurs |För att kunna använda de här behållarna, måste du ha:<br><br>En _Avvikelseidentifiering detektor_ Azure-resurs att hämta associerade krypteringsnyckeln och fakturering slutpunkt URI. Båda värdena är tillgängliga på Azure portal-sidorna för Avvikelseidentifiering detektor översikt och nycklar och krävs för att starta behållaren.<br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är: `https://westus2.api.cognitive.microsoft.com`|

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först slutför och skicka den [frågeformuläret för Avvikelseidentifiering detektor behållare](https://aka.ms/adcontainer) att begära åtkomst till behållaren.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade processorkärnor och minne för att allokera för Avvikelseidentifiering detektor behållare.

| Frågor per sekund (frågor per sekund) | Minimum | Rekommenderas |
|-----------|---------|-------------|
| 10 QPS | 4 kärnor, 1GB minne | 8 kärnor 2GB minne |
| 20 QPS | 8 kärnor, 2GB minne | 16 kärnor 4GB minne |

Varje kärna måste vara minst 2,6 GHz (gigahertz) eller snabbare.

Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med `docker pull`

Använd den [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning.

| Container | Lagringsplats |
|-----------|------------|
| cognitive-services-anomaly-detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-anomaly-detector-container"></a>Docker pull för Avvikelseidentifiering detektor behållaren

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run), med de nödvändiga fakturering inställningar. Mer [exempel](anomaly-detector-container-configuration.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra någon av tre behållarna. Kommandot använder följande parametrar:

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgänglig på sidan för Azure-portalens Avvikelseidentifiering detektor nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på Azure portal Avvikelseidentifiering detektor översiktssidan.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector:latest \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Det här kommandot:

* Kör en behållare för Avvikelseidentifiering detektor från behållaravbildningen
* Allokerar en CPU-kärna och 4 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

### <a name="running-multiple-containers-on-the-same-host"></a>Flera behållare som körs på samma värd

Om du planerar att köra flera behållare med portar, se till att köra varje behållare med en annan port. Till exempel den första behållaren på port 5000 och andra behållare på port 5001.

Ersätt den `<container-registry>` och `<container-name>` med värdena för de behållare som du använder. De behöver inte vara samma behållare. Du kan ha Avvikelseidentifiering detektor behållaren och LUIS-behållaren som körs på värden tillsammans eller du kan ha flera Avvikelseidentifiering detektor behållare som körs. 

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Kör andra behållare på port 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Varje efterföljande behållare ska vara på en annan port. 

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

Behållaren innehåller REST-baserade frågan förutsägelse endpoint API: er. 

Använd värden https://localhost:5000, för behållaren API: er.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med ett utgående [montera](anomaly-detector-container-configuration.md#mount-settings) och loggning är aktiverat, behållaren genererar loggfiler som är användbara för att felsöka problem som kan inträffa när startas eller körs i behållaren. 

## <a name="billing"></a>Fakturering

Avvikelseidentifiering detektor behållare skicka faktureringsinformation till Azure, med en _Avvikelseidentifiering detektor_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](anomaly-detector-container-configuration.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och Avvikelseidentifiering detektor behållare som körs. Sammanfattningsvis:

* Avvikelseidentifiering detektor tillhandahåller en Linux-behållare för Docker, kapslar in avvikelseidentifiering med batch vs streaming, förväntat intervall inferens och justera känslighet.
* Behållaravbildningar laddas ned från en privat Azure Container Registry reserverade för behållare förhandsversion.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i Avvikelseidentifiering detektor behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. i time series-data som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](anomaly-detector-container-configuration.md) för konfigurationsinställningar
* [Mer information om Avvikelseidentifiering detektor API-tjänsten](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
