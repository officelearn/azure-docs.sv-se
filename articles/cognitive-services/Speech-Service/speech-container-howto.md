---
title: Installera tal behållare – tal tjänst
titleSuffix: Azure Cognitive Services
description: Installera och kör tal behållare. Tal till text skickar ljud strömmar till text i real tid som dina program, verktyg eller enheter kan använda eller Visa. Text till tal konverterar inmatad text till mänskligt syntetiskt tal.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: dapine
ms.openlocfilehash: dbd4f2cd4691f9ae6d8e37f38d2b600ec4897e45
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326702"
---
# <a name="install-and-run-speech-service-containers"></a>Installera och kör tal tjänst behållare

Tal behållare gör det möjligt för kunder att bygga en tal program arkitektur som är optimerad för att dra nytta av både robusta moln funktioner och Edge-plats. 

De två tal behållarna är **tal till text** och **text till tal**. 

|Funktion|Funktioner|Senaste|
|-|-|--|
|Tal till text| <li>Beskrivar kontinuerliga tal i real tid eller batch-ljudinspelningar i text med mellanliggande resultat.|1.2.0|
|Text till tal| <li>Konverterar text till naturligt tal. med oformaterad text eller tal Syntess språk (SSML). |1.2.0|

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder tal behållare:

|Obligatorisk|Syfte|
|--|--|
|Docker-motor| Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br>|
|Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper `docker` om grundläggande kommandon.| 
|Tal resurs |Du måste ha följande för att kunna använda dessa behållare:<br><br>En Azure _tal_ -resurs för att hämta tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på Azure Portalens **tal** översikt och nycklar sidor. Båda krävs för att starta behållaren.<br><br>**{API_KEY}** : En av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}** : Slut punkten enligt vad som anges på sidan **Översikt**|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållar registret

Du måste först fylla i och skicka in [formuläret för begäran om Cognitive Services tal behållare](https://aka.ms/speechcontainerspreview/) för att begära åtkomst till behållaren. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Stöd för avancerad Vector-tillägg

**Värden** är den dator som kör Docker-behållaren. Värden måste ha stöd för [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan kontrol lera detta stöd på Linux-värdar med följande kommando: 

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade processor kärnor och minne som ska allokeras för varje tal-behållare.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|cognitive-services-speech-to-text | 2 kärnor<br>minne på 2 GB  | 4 kärnor<br>4 GB minne  |
|cognitive-services-text-to-speech | 1 kärna, 0,5 GB minne| 2 kärnor, 1 GB minne |

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` inställningarna och `--memory` som `docker run` används som en del av kommandot.

**Obs!** Minimum och Recommended baseras på Docker-gränser, *inte* värd datorns resurser. Till exempel används tal-till-text-behållare som minnes karta för en stor språk modell, och _vi rekommenderar_ att hela filen passar i minnet, vilket är ytterligare 4-6 GB. Dessutom kan den första körningen av en behållare ta längre tid, eftersom modeller växlas till minnet.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med`docker pull`

Behållar avbildningar för tal är tillgängliga.

| Container | Lagringsplats |
|-----------|------------|
| cognitive-services-speech-to-text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |
| cognitive-services-text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="language-locale-is-in-container-tag"></a>Språk språket är i container tag gen

Taggen hämtar `en-us` språket och`jessarus` rösten. `latest`

#### <a name="speech-to-text-locales"></a>Tal till text språk

Alla Taggar, förutom `latest` i, är i följande format, `<culture>` där anger den nationella behållaren:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
1.2.0-amd64-en-us-preview
```

I följande tabell visas de språk som stöds för **tal till text** i 1.2.0-versionen av behållaren:

|Språk språk|Tags|
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

#### <a name="text-to-speech-locales"></a>Text till tal-nationella inställningar

Alla Taggar, förutom `latest` i, är i följande format, `<culture>` där `<voice>` anger språket och anger i vilken typ av behållare som ska visas:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
1.2.0-amd64-en-us-jessarus-preview
```

I följande tabell visas de språk som stöds för **text till tal** i 1.2.0-versionen av behållaren:

|Språk språk|Tags|Röster som stöds|
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
|Spanska|`es-es`|elenarus<br>Lisa – Apollo<br>pablo-apollo<br>|
|Spanska|`es-mx`|hildarus<br>raul-apollo|

### <a name="docker-pull-for-the-speech-containers"></a>Docker-hämtning för tal behållarna

#### <a name="speech-to-text"></a>Tal till text

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

#### <a name="text-to-speech"></a>Text till tal

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på värddatorn [](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Läs om hur du [samlar in nödvändiga parametrar](#gathering-required-parameters) för information om hur `{ENDPOINT_URI}` du `{API_KEY}` hämtar och-värden.

[Exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.

> [!NOTE]
> **Under för hands versionen**måste fakturerings inställningarna vara giltiga för att starta behållaren, men du debiteras inte för användning.

### <a name="text-to-speech"></a>Text till tal

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="speech-to-text"></a>Tal till text

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 2 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en tal behållare från behållar avbildningen
* Allokerar 2 processor kärnor och 2 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren, i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelse slut punkt

|Container|Slutpunkt|
|--|--|
|Tal till text|ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1|
|Text till tal|http://localhost:5000/speech/synthesize/cognitiveservices/v1|

### <a name="speech-to-text"></a>Tal till text

Behållaren tillhandahåller WebSocket-baserade frågor för slut punkts-API: er som nås via [tal-SDK: n](index.yml).

Som standard använder tal-SDK online Speech Services. Om du vill använda behållaren måste du ändra initierings metoden. Se exemplen nedan.

#### <a name="for-c"></a>SökerC#

Ändra från att använda det här Azure-moln initierings anropet:

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

till det här anropet med behållarens slut punkt:

```csharp
var config = SpeechConfig.FromEndpoint(
    new Uri("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1"),
    "YourSubscriptionKey");
```

#### <a name="for-python"></a>För python

Ändra från att använda det här Azure-moln initierings anropet

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

till det här anropet med behållarens slut punkt:

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, endpoint="ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
```

### <a name="text-to-speech"></a>Text till tal

Behållaren innehåller API: er för REST-slutpunkt som du hittar [här](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech) och exempel hittar du [här](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/).

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med en utgående [montering](speech-container-configuration.md#mount-settings) och loggning aktive rad genererar behållaren loggfiler som är till hjälp vid fel sökning av problem som inträffar när du startar eller kör behållaren.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Tal behållarna skickar fakturerings information till Azure med hjälp av en _tal_ resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](speech-container-configuration.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra tal behållare. Sammanfattningsvis:

* Tal innehåller två Linux-behållare för Docker, inkapsling av tal till text och text till tal.
* Behållar avbildningar laddas ned från det privata behållar registret i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i tal behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
>  Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](speech-container-configuration.md) för konfigurationsinställningar
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
