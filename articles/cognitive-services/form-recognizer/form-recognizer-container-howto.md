---
title: Så här installerar och kör du behållare för Formulärconformerare
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs hur du använder behållaren för Azure Cognitive Services Form Recognizer för att tolka formulär- och tabelldata.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8cfa9114c5a5e57882cb84b604c1cf71be9acc52
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878348"
---
# <a name="install-and-run-form-recognizer-containers-preview"></a>Installera och kör Formulärconformeringsbehållare (förhandsversion)

Azure Form Recognizer tillämpar maskininlärningsteknik för att identifiera och extrahera nyckelvärdespar och tabeller från formulär. Den associerar värden och tabellposter med nyckelvärdesparen och matar sedan ut strukturerade data som innehåller relationerna i den ursprungliga filen. 

För att minska komplexiteten och enkelt integrera en anpassad formulärreparationsmodell i din automatiseringsprocess för arbetsflöde eller annat program kan du anropa modellen med hjälp av ett enkelt REST API. Endast fem formulärdokument (eller ett tomt formulär och två ifyllda formulär) behövs, så att du kan få resultat snabbt, korrekt och skräddarsytt för ditt specifika innehåll. Ingen tung manuell intervention eller omfattande datavetenskap expertis är nödvändig. Och det kräver inte datamärkning eller dataanotering.

> [!IMPORTANT]
> Formulärreformerarbehållarna använder för närvarande version 1.0 av API:et för formulärre recognizeer. Du kan komma åt den senaste versionen av API:et med hjälp av den hanterade tjänsten i stället.

| Funktion | Funktioner |
|----------|----------|
| Formigenkänning | <li>Bearbetar PDF-, PNG- och JPG-filer<li>Tränar anpassade modeller med minst fem former av samma layout <li>Extraherar nyckelvärdespar och tabellinformation <li>Använder azure cognitive services-api:et för datorseende identifiera text för att identifiera och extrahera utskriven text från bilder i formulär<li>Kräver inte anteckning eller märkning |

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Innan du använder Formulärconformorbehållare måste du uppfylla följande förutsättningar:

| Krävs | Syfte |
|----------|---------|
| Docker-motorn | Du måste dockermotorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> I Windows måste Docker också konfigureras för att stödja Linux-behållare.<br><br> |
| Förtrogenhet med Docker | Du bör ha en grundläggande förståelse för Docker-begrepp, till exempel register, databaser, behållare `docker` och behållaravbildningar och kunskap om grundläggande kommandon. |
| Azure CLI | Installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på din värd. |
| API-resurs för datorseende | Om du vill bearbeta skannade dokument och bilder behöver du en datorseenderesurs. Du kan komma åt funktionen Identifiera text som antingen en Azure-resurs (REST API eller SDK) eller en *cognitive-services-recognize-text-behållare* [container](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull). De vanliga faktureringsavgifterna tillkommer. <br><br>Skicka in både API-nyckel och slutpunkter för din Computer Vision-resurs (Azure-moln- eller Cognitive Services-behållare). Använd den här API-nyckeln och slutpunkten som **{COMPUTER_VISION_API_KEY}** och **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Om du använder behållaren *för kognitiva tjänster-igen-text* kontrollerar du att:<br><br>Datorns vision-nyckel för behållaren För formuläravkänning `docker run` är nyckeln som anges i kommandot Datorseende för behållaren för kognitiva tjänster.Your Computer Vision key for the Form Recognizer container is the key specified in the Computer Vision command for the *cognitive-services-recognize-text* container.<br>Din faktureringsslutpunkt är behållarens slutpunkt `http://localhost:5000`(till exempel ). Om du använder både behållaren För visuellt innehåll och formulärmedkänningsbehållaren tillsammans på samma värd kan de inte båda startas med standardporten *på 5000*. |
| Resurs för formulärkonformering | Om du vill använda dessa behållare måste du ha:<br><br>En Azure **Form Recognizer-resurs** för att hämta den associerade API-nyckeln och slutpunkts-URI. Båda värdena är tillgängliga på sidorna Azure Portal **Form Recognizer** Overview och Keys, och båda värdena krävs för att starta behållaren.<br><br>**{FORM_RECOGNIZER_API_KEY}**: En av de två tillgängliga resursnycklarna på sidan Nycklar<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: Slutpunkten som anges på sidan Översikt |

> [!NOTE]
> Resursnamnet För visuellt innehåll ska vara ett `-` enda ord, utan bindestreck eller andra specialtecken. Den här begränsningen finns på plats för att säkerställa kompatibilitet med formulärreform och igenkännlig textbehållare.

## <a name="gathering-required-parameters"></a>Samla in nödvändiga parametrar

Det finns tre primära parametrar för alla Cognitive Services behållare som krävs. Licensavtalet för slutanvändare (EULA) måste finnas med `accept`värdet . Dessutom behövs både en slutpunkts-URL och API-nyckel.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Slutpunkts-URI `{COMPUTER_VISION_ENDPOINT_URI}` och`{FORM_RECOGNIZER_ENDPOINT_URI}`

**URI-värdet endpoint** är tillgängligt på sidan Översikt *över* Azure-portalen för motsvarande Cognitive Service-resurs. Navigera till sidan *Översikt,* hovra över `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> slutpunkten och en ikon visas. Kopiera och använd där det behövs.

![Samla slutpunktsuri för senare användning](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Nycklar `{COMPUTER_VISION_API_KEY}` och`{FORM_RECOGNIZER_API_KEY}`

Den här nyckeln används för att starta behållaren och är tillgänglig på Azure-portalens keys-sida för motsvarande Cognitive Service-resurs. Navigera till sidan *Nycklar* och `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> klicka på ikonen.

![Skaffa en av de två nycklarna för senare användning](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Dessa prenumerationsnycklar används för att komma åt ditt Cognitive Service API. Dela inte dina nycklar. Lagra dem säkert, till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar dessa nycklar regelbundet. Endast en nyckel är nödvändig för att ringa ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först fylla i och skicka [formuläret Cognitive Services Form Recognizer Containers åtkomstbegäran](https://aka.ms/FormRecognizerContainerRequestAccess) för att begära åtkomst till behållaren. Om du gör det registrerar du dig också för Datorseende. Du behöver inte registrera dig för formuläret för begäran om datorseende separat. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

De minsta och rekommenderade CPU-kärnor och minne som allokerar för varje Forms Recognizer-behållare beskrivs i följande tabell:

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Formigenkänning | 2 kärnor, 4 GB minne | 4-kärniga, 8 GB minne |
| Känna igen text | 1 kärna, 8 GB minne | 2 kärnor, 8 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.
* Kärna och minne `--cpus` motsvarar `--memory` och inställningar, som används `docker run` som en del av kommandot.

> [!Note]
> De lägsta och rekommenderade värdena baseras på Docker-gränser och *inte* värddatorns resurser.

## <a name="get-the-container-images-with-the-docker-pull-command"></a>Hämta behållaravbildningarna med docker pull-kommandot

Behållaravbildningar för både **erbjudandena formuläravkännings-** och **igenkännningstext** finns i följande behållarregister:

| Container | Fullständigt kvalificerat bildnamn |
|-----------|------------|
| Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |
| Känna igen text | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Du behöver båda behållarna, observera att **behållaren för text recognizer** är [detaljerad utanför den här artikeln.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker-drag för behållaren För formulärre recognizeer

#### <a name="form-recognizer"></a>Formigenkänning

Så här hämtar du behållaren Formulärconformering:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```
### <a name="docker-pull-for-the-recognize-text-container"></a>Docker-dragning för behållaren Identifiera text

#### <a name="recognize-text"></a>Känna igen text

Så här hämtar du behållaren Identifiera text:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

## <a name="how-to-use-the-container"></a>Så här använder du behållaren

När behållaren finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-by-using-the-docker-run-command)med de faktureringsinställningar som krävs. Fler [exempel](form-recognizer-container-configuration.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga.
1. [Fråga behållarens förutsägelseslutpunkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Köra behållaren med kommandot docker run

Använd [kommandot docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Se [samla in obligatoriska parametrar](#gathering-required-parameters) för `{COMPUTER_VISION_ENDPOINT_URI}`information `{COMPUTER_VISION_API_KEY}` `{FORM_RECOGNIZER_ENDPOINT_URI}` om `{FORM_RECOGNIZER_API_KEY}` hur du hämtar , och värden.

[Exempel](form-recognizer-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

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

* Kör en behållare för formuläravkänning från behållaravbildningen.
* Allokerar 2 CPU-kärnor och 8 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn.
* Monterar en /input och en /output-volym till behållaren.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Kör separata behållare som separata dockerkörningskommandon

För kombinationen Formulärsammankänning och Text recognizer som finns lokalt på samma värd värd använder du följande två exempel på Docker CLI-kommandon:

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
Varje efterföljande behållare bör vara på en annan port. 

### <a name="run-separate-containers-with-docker-compose"></a>Kör separata behållare med Docker Compose

För kombinationen Formulärsammanstol och Text recognizer som finns lokalt på samma värd värd läser du följande exempel på Docker Compose YAML-fil. Textsammankänningsverktyget `{COMPUTER_VISION_API_KEY}` måste `formrecognizer` vara `ocr` samma för både behållare och behållare. Används `{COMPUTER_VISION_ENDPOINT_URI}` endast i `ocr` behållaren eftersom `formrecognizer` behållaren `ocr` använder namnet och porten. 

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
> `Eula`Den `Billing`, `ApiKey`, och , `FormRecognizer:ComputerVisionApiKey` `FormRecognizer:ComputerVisionEndpointUri` samt alternativen och, måste anges för att köra behållaren. Annars startar inte behållaren. Mer information finns i [Fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelseslutpunkt

|Container|Slutpunkt|
|--|--|
|formulär-igenkännare|http://localhost:5000

### <a name="form-recognizer"></a>Formigenkänning

Behållaren tillhandahåller websocket-baserade frågeslutpunkts-API:er, som du kommer åt via [SDK-dokumentation för Formulärreitertjänster](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Som standard använder Formulär recognizer SDK onlinetjänsterna. Om du vill använda behållaren måste du ändra initieringsmetoden. Se exemplen nedan.

#### <a name="for-c"></a>För C #

Ändra från att använda det här initieringsanropet i Azure-cloud:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
till det här anropet, som använder behållarslutpunkten:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>För Python

Ändra från att använda det här initieringsanropet i Azure-cloud:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

till det här anropet, som använder behållarslutpunkten:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Formigenkänning

Behållaren tillhandahåller REST-slutpunkts-API:er, som du hittar på [API-sidan För formulärre recognizeer.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel)


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en [utdatamontering](form-recognizer-container-configuration.md#mount-settings) och loggning aktiverad genererar behållaren loggfiler som är användbara för att felsöka problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Formulärkonekänningsbehållare skickar faktureringsinformation till Azure med hjälp av en _formulärkonekärerresurs_ på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Konfigurera behållare](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för att hämta, installera och köra Formulärconformeringsbehållare. Sammanfattningsvis:

* Form Recognizer tillhandahåller en Linux-behållare för Docker.
* Behållaravbildningar hämtas från det privata behållarregistret i Azure.
* Behållaravbildningar körs i Docker.
* Du kan använda antingen REST API eller REST SDK för att anropa åtgärder i Forms Recognizer-behållaren genom att ange behållarens värd-URI.
* Du måste ange faktureringsinformationen när du instansierar en behållare.

> [!IMPORTANT]
>  Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata (till exempel den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](form-recognizer-container-configuration.md) för konfigurationsinställningar.
* Använd fler [Cognitive Services-behållare](../cognitive-services-container-support.md).
