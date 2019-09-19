---
title: Installera och köra behållare – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Så här hämtar, installerar och kör behållare för visuellt innehåll i den här genomgången självstudien.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d3a36615109383074833e9af634eb611fb863339
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103657"
---
# <a name="install-and-run-recognize-text-containers"></a>Installera och Kör Identifiera text behållare

Identifiera Text-delen av visuellt innehåll är också tillgängligt som en Docker-behållare. Det kan du identifiera och extrahera utskrivna text från bilder för olika objekt med olika ytor och bakgrunder, till exempel kvitton och affischer visitkort.

> [!IMPORTANT]
> Behållaren identifiera Text fungerar för närvarande bara på engelska.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder Identifiera text behållare:

|Obligatorisk|Syfte|
|--|--|
|Docker-motor| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper `docker` om grundläggande kommandon.| 
|Visuellt innehåll resurs |För att du ska kunna använda behållaren måste du ha:<br><br>En Azure **visuellt innehåll** -resurs och den tillhör ande API-nyckeln slut punkts-URI. Båda värdena är tillgängliga på sidorna översikt och nycklar för resursen och krävs för att starta behållaren.<br><br>**{API_KEY}** : En av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}** : Slut punkten enligt vad som anges på sidan **Översikt**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med`docker pull`

Behållar avbildningar för Identifiera text är tillgängliga. 

| Container | Lagringsplats |
|-----------|------------|
|Identifiera text | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Använd kommandot för att ladda ned en behållar avbildning.


### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-hämtning för Identifiera text container

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på värddatorn [](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](computer-vision-resource-container-config.md) på `docker run` kommandot är tillgängliga. 
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur `{ENDPOINT_URI}` du `{API_KEY}` hämtar och-värden.

[Exempel](computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en identifierande behållare från behållar avbildningen
* Allokerar en processor kärna och 4 GB minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn. 

Fler [exempel](./computer-vision-resource-container-config.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelse slut punkt

Behållaren innehåller REST-baserade slut punkts-API: er för frågor förutsägelse. 

Använd värden, `http://localhost:5000`för behållar-API: er.

### <a name="asynchronous-text-recognition"></a>Asynkron textigenkänning

Du kan använda den `POST /vision/v2.0/recognizeText` och `GET /vision/v2.0/textOperations/*{id}*` operations tillsammans att asynkront känna igen utskrivna text i en bild, liknar hur tjänsten för visuellt innehåll använder de motsvarande REST-åtgärderna. Identifiera Text behållaren endast känner igen utskrivna text, inte handskriven text just nu, så den `mode` parametern normalt angetts för tjänståtgärd visuellt ignoreras av behållaren identifiera Text.

### <a name="synchronous-text-recognition"></a>Synkron textigenkänning

Du kan använda den `POST /vision/v2.0/recognizeTextDirect` åtgärden att synkront känna igen utskrivna text i en bild. Eftersom den här åtgärden är synkron, är begär ande texten för den här åtgärden densamma som `POST /vision/v2.0/recognizeText` åtgärden, men svars texten för den här åtgärden är densamma som den som returnerades `GET /vision/v2.0/textOperations/*{id}*` av åtgärden.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](./computer-vision-resource-container-config.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren. 


## <a name="billing"></a>Fakturering

Identifiera text behållare skickar fakturerings information till Azure med hjälp av en _identifiera text_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra Identifiera text behållare. Sammanfattningsvis:

* Identifiera text tillhandahåller en Linux-behållare för Docker och identifierar text.
* Behållaravbildningar laddas ned från Microsoft Container Registry (MCR) i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i Identifiera text behållare genom att ange behållarens värd-URI.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services behållare skickar inte kund information (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](computer-vision-resource-container-config.md) för konfigurationsinställningar
* Granska [visuellt översikt](Home.md) vill veta mer om att känna igen utskrivna och handskriven text  
* Referera till den [API för visuellt innehåll](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mer information om de metoder som stöds av behållaren.
* Referera till [vanliga frågor (och svar FAQ)](FAQ.md) att lösa problem som rör visuellt funktioner.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
