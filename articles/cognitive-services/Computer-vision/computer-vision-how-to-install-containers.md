---
title: Hur du installerar och kör behållare – för visuellt innehåll
titlesuffix: Azure Cognitive Services
description: Så här hämtar, installerar och kör behållare för visuellt innehåll i den här genomgången självstudien.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 02/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 58a22253efb6928c87937e4aba852dd93a3e4422
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978552"
---
# <a name="install-and-run-recognize-text-containers"></a>Installera och köra Identifiera Text behållare

Identifiera Text-delen av visuellt innehåll är också tillgängligt som en Docker-behållare. Det kan du identifiera och extrahera utskrivna text från bilder för olika objekt med olika ytor och bakgrunder, till exempel kvitton och affischer visitkort.  
> [!IMPORTANT]
> Behållaren identifiera Text fungerar för närvarande bara på engelska.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder identifiera Text behållare:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **På Windows**, Docker måste också konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.| 
|Identifiera Text resurs |För att kunna använda behållaren måste du ha:<br><br>En [ _identifiera Text_ ](vision-api-how-to-topics/howtosubscribe.md) Azure-resurs att hämta associerade krypteringsnyckeln och fakturering slutpunkt URI. Båda värdena är tillgängliga på Azure portal identifiera Text översikt och nycklar sidor och krävs för att starta behållaren.<br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är: `https://westus.api.cognitive.microsoft.com/vision/v2.0`|


## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade processorkärnor och minne för att allokera för varje behållare identifiera Text.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|Identifiera Text|1 kärna, 8 GB minne, 0,5 TPS|2 kärnor, 8 GB minne, 1 TPS|

Varje kärna måste vara minst 2,6 GHz (gigahertz) eller snabbare.

Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.


## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med `docker pull`

Behållaravbildningar för identifiera Text är tillgängliga. 

| Container | Lagringsplats |
|-----------|------------|
|Identifiera Text | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Använd den [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning.


### <a name="docker-pull-for-the-recognize-text-container"></a>Docker pull för behållaren identifiera Text

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-rocognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run), med de nödvändiga fakturering inställningar. Mer [exempel](computer-vision-resource-container-config.md) av den `docker run` kommandot är tillgängliga. 
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra behållaren. Kommandot använder följande parametrar:

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgänglig på sidan för Azure-portalens identifiera Text nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Det här kommandot:

* Kör en identifiera behållare från behållaravbildningen
* Allokerar en CPU-kärna och 4 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn. 

Mer [exempel](./computer-vision-resource-container-config.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

Behållaren innehåller REST-baserade frågan förutsägelse endpoint API: er. 

Använd värden https://localhost:5000, för behållaren API: er.

### <a name="asynchronous-text-recognition"></a>Asynkron textigenkänning

Du kan använda den `POST /vision/v2.0/recognizeText` och `GET /vision/v2.0/textOperations/*{id}*` operations tillsammans att asynkront känna igen utskrivna text i en bild, liknar hur tjänsten för visuellt innehåll använder de motsvarande REST-åtgärderna. Identifiera Text behållaren endast känner igen utskrivna text, inte handskriven text just nu, så den `mode` parametern normalt angetts för tjänståtgärd visuellt ignoreras av behållaren identifiera Text.

### <a name="synchronous-text-recognition"></a>Synkron textigenkänning

Du kan använda den `POST /vision/v2.0/recognizeTextDirect` åtgärden att synkront känna igen utskrivna text i en bild. Eftersom åtgärden sker synkront, förfrågans brödtext för den här åtgärden är samma som för den `POST /vision/v2.0/recognizeText` igen, men svaret brödtext för den här åtgärden är samma som returneras av den `GET /vision/v2.0/textOperations/*{id}*` igen.

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med ett utgående [montera](./computer-vision-resource-container-config.md#mount-settings) och loggning är aktiverat, behållaren genererar loggfiler som är användbara för att felsöka problem som kan inträffa när startas eller körs i behållaren. 

## <a name="containers-api-documentation"></a>Behållarens API-dokumentation

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fakturering

Identifiera Text behållare skicka faktureringsinformation till Azure, med en _identifiera Text_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](./computer-vision-resource-container-config.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och identifiera Text behållare som körs. Sammanfattningsvis:

* Identifiera Text innehåller en Linux-behållare för Docker, kapslar in identifiera text.
* Behållaravbildningar laddas ned från Microsoft Container Registry (MCR) i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i identifiera Text behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex, bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](computer-vision-resource-container-config.md) för konfigurationsinställningar
* Granska [visuellt översikt](Home.md) vill veta mer om att känna igen utskrivna och handskriven text  
* Referera till den [API för visuellt innehåll](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mer information om de metoder som stöds av behållaren.
* Referera till [vanliga frågor (och svar FAQ)](FAQ.md) att lösa problem som rör visuellt funktioner.
* Använder mer [Cognitive Services-behållare](../cognitive-services-container-support.md)
