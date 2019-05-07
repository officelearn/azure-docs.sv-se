---
title: Installera tal behållare
titleSuffix: Azure Cognitive Services
description: Installera och köra tal behållare. Tal till text transkriberar ljudströmmar till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Text till tal konverterar indata-text till människoliknande syntetiskt tal.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2adcbad55236917685ddcdbabe4809f36ab5a730
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153050"
---
# <a name="install-and-run-speech-service-containers"></a>Installera och köra Speech Service-behållare

Tal-behållare kan kunder skapa en programarkitektur med tal som är optimerad för att dra nytta av både robusta funktioner och edge ort. Vi stöder nu två tal behållarna är **tal till text** och **text till tal**. 

Två tal-behållare är **tal till text** och **text till tal**. 

|Funktion|Funktioner|Senaste|
|-|-|--|
|Tal till text| <li>Transkriberar kontinuerlig realtidsöversättning av tal till text.<li>Kan masstranskribera tal från ljudinspelningar. <li>Stöder mellanliggande resultat, identifiering av talslut, automatisk textformatering och svordomsmaskering. <li>Kan anropa [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) för att härleda användaravsikten från transkriberat tal.\*|1.1.1|
|Text till tal| <li>Konverterar text till naturligt tal. <li>Erbjuder flera kön eller dialekter för många språk. <li>Stöder inmatning av oformaterad text eller Speech Synthesis Markup Language (SSML). |1.1.0|

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du måste uppfylla följande krav innan du använder tal behållare:

|Krävs|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **På Windows**, Docker måste också konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.| 
|Tal-resurs |För att kunna använda de här behållarna, måste du ha:<br><br>En _tal_ Azure-resurs att hämta associerade krypteringsnyckeln och fakturering slutpunkt URI. Båda värdena är tillgängliga på Azure portal **tal** översikt och nycklar sidor och är krävs för att starta behållaren.<br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först slutför och skicka den [Cognitive Services tal behållare formulär](https://aka.ms/speechcontainerspreview/) att begära åtkomst till behållaren. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Avancerad support för vektor-tillägg

Den **värden** är den dator som kör docker-behållaren. Värden måste stödja [avancerade vektor tillägg](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan kontrollera det här stödet på Linux-värdar med följande kommando: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade processorkärnor och minne för att allokera för varje tal-behållare.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 kärnor<br>2 GB minne  | 4 kärnor<br>4 GB minne  |
|cognitive-services-text-to-speech | 1 kärna, 0,5 GB minne| 2 kärnor, 1 GB minne |

* Varje kärna måste vara minst 2,6 GHz (gigahertz) eller snabbare.


Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.

**Obs**; Minsta och rekommenderade baseras på Docker gränser *inte* värden machine resurser. Tal till text behållare minne mappa delar av en stor språkmodell och det är till exempel _rekommenderas_ att hela filen ryms i minnet, som är ytterligare 4 – 6 GB. Den första körningen av antingen behållaren kan också ta längre tid, eftersom modeller är att växlat minne i minnet.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med `docker pull`

Behållaravbildningar för tal är tillgängliga. 

| Container | Lagringsplats |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Språkinställningen finns i behållartagg

Den `latest` tagga hämtar den `en-us` nationella inställningar och `jessarus` röst. 

#### <a name="speech-to-text-locales"></a>Tal till text-språk

Alla taggar, förutom för `latest` finns i följande format, där den `<culture>` anger behållaren nationella inställningar:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Följande kod är ett exempel på format:

```
1.0.0-amd64-en-us-preview
```

I följande tabell visas de nationella inställningarna som stöds för **tal till text** 1.1.1 version av behållaren:

|Språkinställningen|Taggar|
|--|--|
|Kinesiska|`zh-cn`|
|Svenska |`en-us`<br>`en-gb`<br>`en-au`<br>`en-in`|
|Franska |`fr-ca`<br>`fr-fr`|
|Tyska|`de-de`|
|Italienska|`it-it`|
|Japanska|`ja-jp`|
|Koreanska|`ko-kr`|
|Portugisiska|`pt-br`|
|Spanska|`es-es`<br>`es-mx`|


#### <a name="text-to-speech-locales"></a>Text till tal-språk

Alla taggar, förutom för `latest` finns i följande format, där den `<culture>` anger de nationella inställningarna och `<voice>` anger röst behållaren:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Följande kod är ett exempel på format:

```
1.0.0-amd64-en-us-jessarus-preview
```

I följande tabell visas de nationella inställningarna som stöds för **text till tal** i 1.1.0 versionen av behållaren:

|Språkinställningen|Taggar|Stöds röster|
|--|--|--|
|Kinesiska|`zh-cn`|huihuirus<br>kangkang-apollo<br>yaoyao-apollo|
|Svenska |`en-au`|catherine<br>hayleyrus|
|Svenska |`en-gb`|george-apollo<br>hazelrus<br>susan-apollo|
|Svenska |`en-in`|heera-apollo<br>priyarus<br>ravi-apollo<br>|
|Svenska |`en-us`|jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus|
|Franska|`fr-ca`|caroline<br>harmonierus|
|Franska|`fr-fr`|hortenserus<br>julie-apollo<br>paul-apollo|
|Tyska|`de-de`|hedda<br>heddarus<br>stefan-apollo|
|Italienska|`it-it`|cosimo-apollo<br>luciarus|
|Japanska|`ja-jp`|ayumi-apollo<br>harukarus<br>ichiro-apollo|
|Koreanska|`ko-kr`|heamirus|
|Portugisiska|`pt-br`|daniel-apollo<br>heloisarus|
|Spanska|`es-es`|elenarus<br>laura-apollo<br>pablo-apollo<br>|
|Spanska|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Docker pull för tal-behållare

#### <a name="speech-to-text"></a>Tal till text

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Text till tal

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run), med fakturering inställningar krävs men används inte. Mer [exempel](speech-container-configuration.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra någon av tre behållarna. Kommandot använder följande parametrar:

**I förhandsversionen**, fakturering inställningarna måste vara giltig för att starta behållaren, men du inte debiteras för användning.

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgänglig på sidan för Azure-portalens tal nycklar.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på översiktssidan för Azure-portalens tal.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

### <a name="text-to-speech"></a>Text till tal

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

### <a name="speech-to-text"></a>Tal till text

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} 
```

Det här kommandot:

* Kör en tal-behållare från behållaravbildningen
* Allokerar 2 CPU-kärnor och 2 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

|Container|Slutpunkt|
|--|--|
|Tal till text|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Text till tal|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Tal till text

Behållaren innehåller websocket-baserade frågan endpoint API: er, som kan nås via den [tal SDK](index.yml).

Som standard använder SDK: N för tal online taltjänster. Om du vill använda behållaren som du behöver ändra initieringsmetoden. Se följande exempel.

#### <a name="for-c"></a>FörC#

Byter från att använda det här anropet för initiering av Azure-molnet:

```C#
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

med hjälp av behållarslutpunkten på det här anropet:

```C#
var config = SpeechConfig.FromEndpoint("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1", "YourSubscriptionKey");
```

#### <a name="for-python"></a>För Python

Byter från att använda det här anropet för initiering av Azure-molnet

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)
```

med hjälp av behållarslutpunkten på det här anropet:

```python
speech_config = speechsdk.SpeechConfig(subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Text till tal

Behållaren innehåller REST-slutpunkt API: er som du hittar [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api) och exempel finns [här](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du kör behållaren behållaren använder **stdout** och **stderr** utdata information som är bra att felsöka problem som kan inträffa när startas eller körs i behållaren. 

## <a name="billing"></a>Fakturering

Tal behållare skicka faktureringsinformation till Azure, med en _tal_ resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](speech-container-configuration.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och köra tal behållare. Sammanfattningsvis:

* Tal innehåller två Linux-behållare för Docker, som kapslar in tal till text och text till tal.
* Behållaravbildningar laddas ned från det privata behållarregistret i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i tal behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
>  Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](speech-container-configuration.md) för konfigurationsinställningar
* Använder mer [Cognitive Services-behållare](../cognitive-services-container-support.md)
