---
title: Installera och köra behållare för formulär tolken
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder formigenkänningscontainern för att parsa formulär- och tabelldata.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 3c0129275ecf78e6a4e6b9286f975ded2b6f9ae3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051201"
---
# <a name="install-and-run-form-recognizer-containers"></a>Installera och kör formulär igenkännings behållare

Azure formulär tolken använder Machine Learning-teknik för att identifiera och extrahera nyckel/värde-par och tabeller från formulär. Den associerar värden och tabell poster med nyckel/värde-par och matar sedan ut strukturerade data som innehåller relationerna i original filen. 

För att minska komplexiteten och enkelt integrera en anpassad formulär igenkännings modell i automatiserings processen för arbets flödet eller ett annat program, kan du anropa modellen med hjälp av en enkel REST API. Endast fem formulär dokument (eller ett tomt formulär och två ifyllda formulär) behövs, så att du kan få resultat snabbt, exakt och skräddarsys efter ditt eget innehåll. Ingen kraftig manuell åtgärd eller omfattande data vetenskaps expertis krävs. Och det kräver inte data etiketter eller data anteckningar.

|Funktion|Funktioner|
|-|-|
|Formigenkänning| <li>Bearbetar PDF-, PNG-och JPG-filer<li>Tågen anpassade modeller med minst fem former av samma layout <li>Extraherar nyckel/värde-par och tabell information <li>Använder Identifiera text funktionen Azure Cognitive Services API för visuellt innehåll för att identifiera och extrahera utskriven text från bilder i formulär<li>Kräver inte anteckningar eller etiketter|

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du använder formulär igenkännings behållare måste du uppfylla följande krav:

|Obligatorisk|Syfte|
|--|--|
|Docker-motor| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> I Windows måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, till exempel register, databaser, behållare och behållar avbildningar samt kunskaper om grundläggande `docker` kommandon.|
|Azure CLI| Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på värden.|
|API för visuellt innehåll resurs| Om du vill bearbeta skannade dokument och avbildningar behöver du en Visuellt innehåll-resurs. Du kan komma åt Identifiera text-funktionen antingen som en Azure-resurs (REST API eller SDK) eller en *kognitiv-tjänster-Recognizer-text* - [behållare](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). De vanliga fakturerings avgifterna gäller. <br><br>Skicka både API-nyckeln och slut punkterna för din Visuellt innehåll resurs (Azure-moln eller Cognitive Services-behållare). Använd den här API-nyckeln och slut punkten som **{COMPUTER_VISION_API_KEY}** och **{COMPUTER_VISION_ENDPOINT_URI}** .<br><br> Om du använder funktionen *kognitiv-Services-recognize-text* , se till att:<br><br>Din visuellt innehåll nyckel för formulär tolkens behållare är den nyckel som anges i kommandot visuellt innehåll `docker run` för filen *kognitiv-Services-recognize-text* .<br>Din fakturerings slut punkt är behållarens slut punkt (till exempel `http://localhost:5000`). Om du använder både Visuellt innehåll container-och formulär igenkännings behållare på samma värd, kan de inte startas både med standard porten *5000*.  |
|Formulär igenkännings resurs |Om du vill använda dessa behållare måste du ha:<br><br>En Azure **formulär igenkännings** resurs för att hämta den associerade API-nyckeln och slut punkts-URI: n. Båda värdena är tillgängliga på sidorna igenkännings översikt och nycklar för Azure Portal **formulär** och båda värdena krävs för att starta behållaren.<br><br>**{FORM_RECOGNIZER_API_KEY}** : En av de två tillgängliga resurs nycklarna på sidan nycklar<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}** : Slut punkten enligt vad som anges på sidan Översikt|

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållar registret

Du måste först fylla i och skicka in [formuläret för formulär för Cognitive Services som identifierar behållar formuläret](https://aka.ms/FormRecognizerRequestAccess) för att begära åtkomst till behållaren. När du gör det loggas du också in för Visuellt innehåll. Du behöver inte registrera dig för formuläret för Visuellt innehåll begäran separat. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

De minsta och rekommenderade processor kärnor och minne som ska allokeras för varje formulär igenkännings behållare beskrivs i följande tabell:

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 kärnor, 4 GB minne | 4 kärnor, 8 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* TPS-transaktioner per sekund
* Core och minne motsvarar `--cpus` inställningarna och `--memory` som `docker run` används som en del av kommandot.

> [!Note]
> De lägsta och rekommenderade värdena baseras på Docker-gränser och *inte* värd datorns resurser.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Hämta behållar avbildningen med Docker pull-kommandot

Behållar avbildningar för formulär igenkänning är tillgängliga i följande lagrings plats:

| Container | Lagringsplats |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Om du tänker använda `cognitive-services-recognize-text` [behållaren](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull), i stället för formulär tolknings tjänsten, kontrollerar du `docker pull` att du använder kommandot med rätt behållar namn: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker-hämtning för formulär igenkännings behållare

#### <a name="form-recognizer"></a>Formigenkänning

Använd följande kommando för att hämta formulär tolkens behållare:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på värddatorn [](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-by-using-the-docker-run-command)med de fakturerings inställningar som krävs. Fler [exempel](form-recognizer-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Kör behållaren med kommandot Docker Run

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra någon av de tre behållarna. Kommandot använder följande parametrar:

| Platshållare | Value |
|-------------|-------|
|{FORM_RECOGNIZER_API_KEY} | Den här nyckeln används för att starta behållaren. Den finns på sidan Azure Portal **formulär igenkännings nycklar** .  |
|{FORM_RECOGNIZER_ENDPOINT_URI} | URI-värdet för fakturerings slut punkten är tillgängligt på översikts sidan för Azure Portal **formulär igenkänning** .|
|{COMPUTER_VISION_API_KEY}| Nyckeln finns på sidan Azure Portal **API för visuellt innehåll nycklar** .|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturerings slut punkten. Om du använder en molnbaserad Visuellt innehåll resurs finns URI-värdet på översikts sidan för Azure Portal **API för visuellt innehåll** . Om du använder en `cognitive-services-recognize-text` behållare använder du URL: en för fakturerings slut punkt som skickas till behållaren `docker run` i kommandot.|

Ersätt dessa parametrar med dina egna värden i följande exempel `docker run` kommando.

### <a name="form-recognizer"></a>Formigenkänning

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Det här kommandot:

* Kör en formulär tolks behållare från behållar avbildningen.
* Allokerar 2 processor kärnor och 8 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.
* Monterar en/input och en/output-volym till behållaren.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Kör separata behållare som separata Docker-körnings kommandon

Använd följande två exempel på Docker CLI-kommandon för formulär igenkännings-och text igenkännings kombinationen som finns lokalt på samma värd:

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Kör den andra behållaren på port 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Varje efterföljande behållare bör finnas på en annan port. 

### <a name="run-separate-containers-with-docker-compose"></a>Kör separata behållare med Docker Compose

För formulär tolken och text igenkännings kombinationen som finns lokalt på samma värd, se följande exempel Docker Compose YAML-fil. Text tolken `{COMPUTER_VISION_API_KEY}` måste vara densamma för `formrecognizer` både behållaren och `ocr` . Används endast `formrecognizer` i behållaren, eftersom behållaren använder namnetochporten.`ocr` `ocr` `{COMPUTER_VISION_ENDPOINT_URI}` 

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
      apikey: "{COMPUTER_VISION_API_KEY}"

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
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
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
> `Eula`, ,`Billing`Och ,`ApiKey`samt alternativen och`FormRecognizer:ComputerVisionEndpointUri`måste anges för att köra behållaren, annars startar inte behållaren. `FormRecognizer:ComputerVisionApiKey` Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelse slut punkt

|Container|Slutpunkt|
|--|--|
|form-recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller WebSocket-baserade frågor om slut punkts-API: er som du kommer åt via [dokumentation för formulär igenkännings tjänstens SDK](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Som standard använder formulär igenkännings-SDK onlinetjänster. Om du vill använda behållaren måste du ändra initierings metoden. Se exemplen nedan.

#### <a name="for-c"></a>SökerC#

Ändra från att använda det här Azure-moln initierings anropet:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
till det här anropet, som använder behållarens slut punkt:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>För python

Ändra från att använda det här Azure-moln initierings anropet:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

till det här anropet, som använder behållarens slut punkt:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller API: er för REST-slutpunkt, som du hittar på sidan [API för formulär igenkänning](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) .


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du kör behållaren använder behållaren **STDOUT** och **stderr** för att spara information som är till hjälp vid fel sökning av problem som uppstår när du startar eller kör behållaren.

## <a name="billing"></a>Fakturering

Formulär identifierarens behållare skickar fakturerings information till Azure med hjälp av en _formulär igenkännings_ resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra formulär igenkännings behållare. Sammanfattningsvis:

* Formulär tolken innehåller en Linux-behållare för Docker.
* Behållar avbildningar laddas ned från det privata behållar registret i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen REST API eller REST SDK för att anropa åtgärder i formulär igenkännings behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
>  Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services behållare skickar inte kund information (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](form-recognizer-container-configuration.md) för konfigurations inställningar.
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md).
