---
title: Installera och köra behållare – formuläret Igenkännande
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder formigenkänningscontainern för att parsa formulär- och tabelldata.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: pafarley
ms.openlocfilehash: a7159fccc9c4ef232cfca08b173e712e268343ea
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507820"
---
# <a name="install-and-run-form-recognizer-containers"></a>Installera och köra formuläret Igenkännande behållare
Formuläret Igenkännande gäller maskininlärningsteknik för att identifiera och extrahera nyckel / värde-par och tabeller från formulär. Det kopplar värden och tabellposter till dem och sedan returnerar strukturerade data som innehåller relationerna i den ursprungliga filen. Du kan anropa anpassade formulär Igenkännande modellen med hjälp av ett enkelt REST API för att minska komplexiteten och integrera enkelt i ditt automation-arbetsflödesprocessen eller annat program. Endast fem dokument (eller ett tomt formulär) behövs, så du kan få resultat snabbt, korrekt och anpassats efter dina specifika innehåll, utan tunga manuella åtgärder eller omfattande data science-expertis. Det kräver inte märkning av data eller data anteckning.

|Funktion|Funktioner|
|-|-|
|Formigenkänning| <li>Filer och processer skriver PDF, PNG och JPG.<li>Träna anpassade modeller med lägsta 5 former av samma layout. <li>Extraherar nyckel / värde-par och tabellinformation. <li>Använder Cognitive Service datorn Vision API RecognizeText för att identifiera och extrahera utskrivna text från bilder i formulär.<li>Kräver inte anteckning eller etikettering.|

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste uppfylla följande krav innan du använder formuläret Igenkännande behållare:

|Obligatoriskt|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **På Windows**, Docker måste också konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.|
|Azure CLI| Du måste installera den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på värden.|
|Datorresurs för API för visuellt innehåll| För att bearbeta skannade dokument och bilder, en **visuellt resource** krävs. Du kan komma åt den **identifiera Text** funktion som antingen en Azure-resurs (REST API eller SDK) eller som en `cognitive-services-recognize-text` [behållare](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Vanliga fakturering avgiften gäller. <br><br>Du måste skicka in både nyckel och fakturering slutpunkt för din specifika visuellt resurs (Azure-molnet eller Cognitive Services-behållare). Använd den här nyckeln och fakturering slutpunkten som {COMPUTER_VISION_API_KEY} och {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Om du använder den  **`cognitive-services-recognize-text` behållare**, se till att:<br><br>* Din nyckel för visuellt innehåll för formuläret Igenkännande behållaren är nyckeln som anges i visuellt `docker run` kommandot för den `cognitive-services-recognize-text` behållare.<br>* Faktureringen slutpunkten är behållarens slutpunkt, till exempel `https://localhost:5000`. Om du använder både för visuellt innehåll och formuläret Igenkännande behållare tillsammans på samma värd de inte båda startas med standardporten `5000`.  |  
|Formuläret Igenkännande resurs |För att kunna använda de här behållarna, måste du ha:<br><br>En _formuläret Igenkännande_ Azure-resurs att hämta associerade krypteringsnyckeln och fakturering slutpunkt URI. Båda värdena är tillgängliga på Azure portal **formuläret Igenkännande** översikt och nycklar sidor och är krävs för att starta behållaren.<br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är: `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först slutför och skicka den [Cognitive Services formuläret Igenkännande behållare formulär](https://aka.ms/FormRecognizerRequestAccess) att begära åtkomst till behållaren. Detta kommer också att registrera dig för visuellt innehåll. Du behöver inte registrera dig för formuläret visuellt separat. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade processorkärnor och minne för att allokera för varje behållare i formuläret Igenkännande.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 kärnor, 4 GB minne | 4 kärnor, 8 GB minne |

* Varje kärna måste vara minst 2,6 GHz (gigahertz) eller snabbare.
* TPS - transaktioner per sekund

Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.

> [!Note]
> De minsta och rekommenderade värdena är baserade på Docker gränser och *inte* värden machine resurser.

## <a name="get-the-container-image-with-docker-pull-command"></a>Hämta behållaravbildningen med docker pull-kommando

Behållaravbildningar för formuläret Igenkännande är tillgängliga.

| Container | Lagringsplats |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Om du planerar att använda den `cognitive-services-recognize-text` [behållare](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull), istället för formatet Igenkännande-tjänsten, kontrollera att du använder den `docker pull` med rätt behållarens namn: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull för formuläret Igenkännande behållaren

#### <a name="form-recognizer"></a>Formigenkänning

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run), med fakturering inställningar krävs men används inte. Mer [exempel](form-recognizer-container-configuration.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga.
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra någon av tre behållarna. Kommandot använder följande parametrar:

| Platshållare | Value |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgänglig på sidan för Azure-portalens formuläret Igenkännande nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på Azure portal formuläret Igenkännande översiktssidan.|
|{COMPUTER_VISION_API_KEY}| Nyckeln är tillgänglig på Azure portal datornycklar Vision API-sidan.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturering slutpunkten. Om du använder en molnbaserad visuellt resurs är URI-värdet tillgängliga på Azure portal datorn Vision API översiktssidan. Om du använder en `cognitive-services-recognize-text` behållare, Använd fakturering slutpunkts-URL som skickades till behållaren i den `docker run` kommando.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

### <a name="form-recognizer"></a>Formigenkänning

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Det här kommandot:

* Kör en behållare för formuläret Igenkännande från behållaravbildningen
* Allokerar 2 CPU-kärnor och 8 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn.
* Monterar en /input och en/Output-volym till behållaren

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Kör separat behållare som separata docker kör kommandon

För identifierare för formuläret och Text Igenkännande kombinationen finns lokalt på samma värd, följer två exempel Docker CLI-kommandon.

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Kör andra behållare på port 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Varje efterföljande behållare ska vara på en annan port. 

### <a name="run-separate-containers-with-docker-compose"></a>Kör separat behållare med Docker Compose

För identifierare för formuläret och Text Igenkännande kombinationen finns lokalt på samma värd, en Docker-Compose YAML-exempelfil finns nedan. Text Igenkännande `{COMPUTER_VISION_API_KEY}` måste vara samma för både den `formrecognizer` och `ocr` behållare. Den `{COMPUTER_VISION_ENDPOINT_URI}` endast används i den `ocr` behållare eftersom den `formrecognizer` behållare använder den `ocr` namn och port. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` samt `FormRecognizer:ComputerVisionApiKey` och `FormRecognizer:ComputerVisionEndpointUri` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

|Container|Slutpunkt|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller websocket-baserade frågan endpoint API: er, som kan nås via [formuläret Igenkännande services SDK-dokumentation](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Som standard använder SDK: N för formuläret Igenkännande onlinetjänsterna. Om du vill använda behållaren som du behöver ändra initieringsmetoden. Se följande exempel.

#### <a name="for-c"></a>FörC#

Byter från att använda det här anropet för initiering av Azure-molnet:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

med hjälp av behållarslutpunkten på det här anropet:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>För Python

Byter från att använda det här anropet för initiering av Azure-molnet

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

med hjälp av behållarslutpunkten på det här anropet:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller REST-slutpunkt API: er, som du hittar [här](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du kör behållaren behållaren använder **stdout** och **stderr** utdata information som är bra att felsöka problem som kan inträffa när startas eller körs i behållaren.

## <a name="billing"></a>Fakturering

Formuläret Igenkännande behållare skicka faktureringsinformation till Azure, med en _formuläret Igenkännande_ resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](form-recognizer-container-configuration.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och formuläret Igenkännande behållare som körs. Sammanfattningsvis:

* Formuläret Igenkännande tillhandahåller en Linux-behållare för Docker.
* Behållaravbildningar laddas ned från det privata behållarregistret i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i formuläret Igenkännande behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
>  Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](form-recognizer-container-configuration.md) för konfigurationsinställningar
* Använder mer [Cognitive Services-behållare](../cognitive-services-container-support.md)
