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
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 647edcab5ec2925016e8a099ae43b6133037f8de
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491174"
---
# <a name="install-and-run-speech-service-containers"></a>Installera och kör tal tjänst behållare

Med behållare kan du köra vissa Speech service-API: er i din egen miljö. Behållare är fantastiska för särskilda säkerhets-och data styrnings krav. I den här artikeln får du lära dig hur du hämtar, installerar och kör en tal behållare.

Tal behållare gör det möjligt för kunderna att bygga en tal program arkitektur som är optimerad för både robusta moln funktioner och Edge-plats. Det finns fyra olika behållare tillgängliga. De två standard behållarna är **tal-till-text** och **text till tal**. De två anpassade behållarna är **Custom Speech till text** och **anpassad text till tal**.

> [!IMPORTANT]
> Alla tal behållare erbjuds för närvarande som en del av en [offentlig "gated"-förhands granskning](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Ett meddelande kommer att göras när tal behållare förloppet till allmän tillgänglighet (GA).

| Funktion | Funktioner | Nya |
|--|--|--|
| Tal till text | Beskrivar kontinuerliga tal i real tid eller batch-ljudinspelningar i text med mellanliggande resultat. | 2.0.0 |
| Custom Speech till text | Genom att använda en anpassad modell från [Custom Speech portalen](https://speech.microsoft.com/customspeech), kan du skriva över kontinuerliga tal i real tid eller köra ljud inspelningar i text med mellanliggande resultat. | 2.0.0 |
| Text till tal | Konverterar text till tal med naturligt ljud med text indata eller SSML (Speech syntes Markup Language). | 1.3.0 |
| Anpassad text till tal | Med hjälp av en anpassad modell från den [anpassade röst portalen](https://aka.ms/custom-voice-portal)konverteras text till tal med naturligt ljud med text-eller tal syntess språk (SSML). | 1.3.0 |

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Följande krav gäller innan du använder tal behållare:

| Krävs | Syfte |
|--|--|
| Docker-motor | Du behöver Docker-motorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). För en introduktion till Docker-och container-grunderna, se [Docker-översikten](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta att behållarna ansluter till och skicka fakturerings data till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br> |
| Bekant med Docker | Du bör ha grundläggande kunskaper om Docker-koncept, t. ex. register, databaser, behållare och behållar avbildningar, samt kunskaper om grundläggande `docker`-kommandon. |
| Tal resurs | Du måste ha följande för att kunna använda dessa behållare:<br><br>En Azure _tal_ -resurs för att hämta tillhör ande API-nyckel och slut punkts-URI. Båda värdena är tillgängliga på Azure Portalens **tal** översikt och nycklar sidor. Båda krävs för att starta behållaren.<br><br>**{Api_key}** : en av de två tillgängliga resurs nycklarna på sidan **nycklar**<br><br>**{ENDPOINT_URI}** : slut punkten enligt vad som anges på **översikts** Sidan |

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållar registret

Fyll i och skicka [formuläret för begäran om Cognitive Services tal behållare](https://aka.ms/speechcontainerspreview/) för att begära åtkomst till behållaren. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Stöd för avancerad Vector-tillägg

**Värden** är den dator som kör Docker-behållaren. Värden *måste ha stöd* för [Advanced Vector Extensions](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan söka efter AVX2-stöd på Linux-värdar med följande kommando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Värddatorn *krävs* för att stödja AVX2. Containern fungerar *inte* korrekt utan AVX2-stöd.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs den lägsta och rekommenderade fördelningen av resurser för varje tal behållare.

# <a name="speech-to-texttabstt"></a>[Tal till text](#tab/stt)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Tal till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech till text](#tab/cstt)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Custom Speech till text | 2 kärnor, 2 GB minne | 4 kärnor, 4 GB minne |

# <a name="text-to-speechtabtts"></a>[Text till tal](#tab/tts)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

# <a name="custom-text-to-speechtabctts"></a>[Anpassad text till tal](#tab/ctts)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Anpassad text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

***

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Core och minne motsvarar `--cpus` och `--memory` inställningar som används som en del av `docker run` kommandot.

> [!NOTE]
> Minimum och Recommended baseras på Docker-gränser, *inte* värd datorns resurser. Till exempel används tal-till-text-behållare som minnes karta för en stor språk modell, och *vi rekommenderar* att hela filen passar i minnet, vilket är ytterligare 4-6 GB. Dessutom kan den första körningen av en behållare ta längre tid, eftersom modeller växlas till minnet.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållar avbildningen med `docker pull`

Behållar avbildningar för tal finns i följande Container Registry.

# <a name="speech-to-texttabstt"></a>[Tal till text](#tab/stt)

| Container | Lagrings platsen |
|-----------|------------|
| Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech till text](#tab/cstt)

| Container | Lagrings platsen |
|-----------|------------|
| Custom Speech till text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speechtabtts"></a>[Text till tal](#tab/tts)

| Container | Lagrings platsen |
|-----------|------------|
| Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speechtabctts"></a>[Anpassad text till tal](#tab/ctts)

| Container | Lagrings platsen |
|-----------|------------|
| Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker-hämtning för tal behållarna

# <a name="speech-to-texttabstt"></a>[Tal till text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-hämtning för den tal-till-text-behållare

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Taggen `latest` hämtar `en-US` språk och `jessarus` röst. För ytterligare språk, se [tal-till-text-språk](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Tal till text-språk

Alla Taggar, förutom `latest` är i följande format, där `<culture>` anger den nationella behållaren:

```
<major>.<minor>.<patch>-<platform>-<culture>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
2.0.0-amd64-en-us-preview
```

I följande tabell visas de språk som stöds för **tal till text** i 2.0.0-versionen av behållaren:

| Språk språk | Taggar |
|--|--|
| Kinesiska | `zh-CN` |
| Svenska | `en-US`<br>`en-GB`<br>`en-AU`<br>`en-IN` |
| Franska | `fr-CA`<br>`fr-FR` |
| Tyska | `de-DE` |
| Italienska | `it-IT` |
| Japanska | `ja-JP` |
| Koreanska | `ko-KR` |
| Portugisiska | `pt-BR` |
| Spanska | `es-ES`<br>`es-MX` |

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech till text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-hämtning för Custom Speech-till-text-behållaren

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> `locale` och `voice` för anpassade tal behållare bestäms av den anpassade modellen som matas in av behållaren.

# <a name="text-to-speechtabtts"></a>[Text till tal](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-hämtning för text till tal-behållaren

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Taggen `latest` hämtar `en-US` språk och `jessarus` röst. För ytterligare språk, se [text till tal-språk](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Text till tal-språk

Alla Taggar, förutom `latest` är i följande format, där `<culture>` anger språkvarianten och `<voice>` anger den här behållarens röst:

```
<major>.<minor>.<patch>-<platform>-<culture>-<voice>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
1.3.0-amd64-en-us-jessarus-preview
```

I följande tabell visas de språk som stöds för **text till tal** i 1.3.0-versionen av behållaren:

| Språk språk | Taggar | Röster som stöds |
|--|--|--|
| Kinesiska | `zh-CN` | huihuirus<br>kangkang-apollo<br>yaoyao-apollo |
| Svenska | `en-AU` | catherine<br>hayleyrus |
| Svenska | `en-GB` | George – Apollo<br>hazelrus<br>Susan – Apollo |
| Svenska | `en-IN` | heera-apollo<br>priyarus<br>ravi-apollo<br> |
| Svenska | `en-US` | jessarus<br>benjaminrus<br>jessa24krus<br>zirarus<br>guy24krus |
| Franska | `fr-CA` | caroline<br>harmonierus |
| Franska | `fr-FR` | hortenserus<br>julie-apollo<br>Paul – Apollo |
| Tyska | `de-DE` | hedda<br>heddarus<br>Stefan – Apollo |
| Italienska | `it-IT` | cosimo-apollo<br>luciarus |
| Japanska | `ja-JP` | ayumi-apollo<br>harukarus<br>ichiro-apollo |
| Koreanska | `ko-KR` | heamirus |
| Portugisiska | `pt-BR` | Daniel – Apollo<br>heloisarus |
| Spanska | `es-ES` | elenarus<br>Lisa – Apollo<br>pablo-apollo<br> |
| Spanska | `es-MX` | hildarus<br>raul-apollo |

> [!IMPORTANT]
> När du konstruerar ett *standard text-till-tal* -http-post kräver [SSML-](speech-synthesis-markup.md) meddelandet ett `voice`-element med ett `name`-attribut. Värdet är motsvarande behållares nationella inställningar och röst, även kallat ["kort namn"](language-support.md#standard-voices). Till exempel skulle taggen `latest` ha röst namnet `en-US-JessaRUS`.

# <a name="custom-text-to-speechtabctts"></a>[Anpassad text till tal](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-hämtning för den anpassade text till tal-behållaren

Använd [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) -kommandot för att hämta en behållar avbildning från förhands gransknings registret för behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> `locale` och `voice` för anpassade tal behållare bestäms av den anpassade modellen som matas in av behållaren.

***

## <a name="how-to-use-the-container"></a>Använda behållaren

När behållaren är på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de fakturerings inställningar som krävs. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är tillgängliga.
1. [Fråga behållarens förutsägelse slut punkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med `docker run`

Använd kommandot [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Information om hur du hämtar `{Endpoint_URI}`-och `{API_Key}`-värden finns i avsnittet om [obligatoriska parametrar](#gathering-required-parameters) . Ytterligare [exempel](speech-container-configuration.md#example-docker-run-commands) på `docker run` kommandot är också tillgängliga.

# <a name="speech-to-texttabstt"></a>[Tal till text](#tab/stt)

Kör följande `docker run`-kommando om du vill köra en *tal-till-text-* behållare.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *tal-till-text-* behållare från behållar avbildningen.
* Allokerar 4 processor kärnor och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

# <a name="custom-speech-to-texttabcstt"></a>[Custom Speech till text](#tab/cstt)

*Custom Speech-till-text-* behållaren är beroende av en anpassad tal modell. Den anpassade modellen måste ha [tränats](how-to-custom-speech-train-model.md) med hjälp av den [anpassade tal portalen](https://speech.microsoft.com/customspeech). Det anpassade tal **modells-ID: t** krävs för att köra behållaren. Du hittar den på sidan **utbildning** i den anpassade tal portalen. Från den anpassade tal portalen navigerar du till sidan **utbildning** och väljer modellen.
<br>

![Inlärnings sidan för anpassat tal](media/custom-speech/custom-speech-model-training.png)

Hämta **modell-ID: t** som ska användas som argument till parametern `ModelId` i `docker run` kommandot.
<br>

![Information om anpassad tal modell](media/custom-speech/custom-speech-model-details.png)

Följande tabell visar de olika `docker run` parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värd datorns [volym montering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel, *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Custom Speech **modell-ID: t** från sidan **utbildning** i den anpassade tal portalen. |
| `{ENDPOINT_URI}` | Slut punkten krävs för mätning och fakturering. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |
| `{API_KEY}` | API-nyckeln måste anges. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |

Kör följande `docker run` kommando för att köra *Custom Speech-till-text-* behållaren:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *Custom Speech-till-text-* behållare från behållar avbildningen.
* Allokerar 4 processor kärnor och 4 GB minne.
* Läser in *Custom Speech-till-text-* modellen från monteringen av volym indatakälla, till exempel *C:\CustomSpeech*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Laddar ned modellen med `ModelId` (om den inte finns på volym monteringen).
* Om den anpassade modellen tidigare har hämtats ignoreras `ModelId`.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

# <a name="text-to-speechtabtts"></a>[Text till tal](#tab/tts)

Kör följande `docker run`-kommando om du vill köra en *text till tal* -behållare.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *text till tal* -behållare från behållar avbildningen.
* Allokerar 2 processor kärnor och en gigabyte (GB) minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

# <a name="custom-text-to-speechtabctts"></a>[Anpassad text till tal](#tab/ctts)

Den *anpassade text till tal* -behållaren är beroende av en anpassad röst modell. Den anpassade modellen måste ha [tränats](how-to-custom-voice-create-voice.md) med hjälp av den [anpassade röst portalen](https://aka.ms/custom-voice-portal). ID för anpassad röst **modell** krävs för att köra behållaren. Du hittar den på sidan **utbildning** i den anpassade röst portalen. Från den anpassade röst portalen går du till sidan **utbildning** och väljer modellen.
<br>

![Sidan anpassad röst utbildning](media/custom-voice/custom-voice-model-training.png)

Hämta **modell-ID: t** som ska användas som argument till `ModelId`-parametern för kommandot Docker Run.
<br>

![Information om anpassad röst modell](media/custom-voice/custom-voice-model-details.png)

Följande tabell visar de olika `docker run` parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värd datorns [volym montering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel, *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Custom Speech **modell-ID: t** från sidan **utbildning** i den anpassade röst portalen. |
| `{ENDPOINT_URI}` | Slut punkten krävs för mätning och fakturering. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |
| `{API_KEY}` | API-nyckeln måste anges. Mer information finns i avsnittet om att [samla in obligatoriska parametrar](#gathering-required-parameters). |

Kör följande `docker run` kommando om du vill köra en *anpassad text till tal* -behållare:

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *anpassad text till tal* -behållare från behållar avbildningen.
* Allokerar 2 processor kärnor och en gigabyte (GB) minne.
* Läser in den *anpassade text till tal-* modellen från monteringen av volym indataport, till exempel *C:\CustomVoice*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Laddar ned modellen med `ModelId` (om den inte finns på volym monteringen).
* Om den anpassade modellen tidigare har hämtats ignoreras `ModelId`.
* Tar automatiskt bort behållaren när den har avslut ATS. Behållar avbildningen är fortfarande tillgänglig på värddatorn.

***

> [!IMPORTANT]
> Alternativen `Eula`, `Billing`och `ApiKey` måste anges för att köra behållaren. annars startar inte behållaren.  Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelse slut punkt

| Container | Slutpunkt | Protokoll |
|--|--|--|
| Tal till text | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Custom Speech till text | `ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1` | WS |
| Text till tal | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |
| Anpassad text till tal | `http://localhost:5000/speech/synthesize/cognitiveservices/v1` | HTTP |

Mer information om hur du använder WSS-och HTTPS-protokoll finns i [behållar säkerhet](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Text till tal eller anpassad text till tal

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Kör flera behållare på samma värd

Om du tänker köra flera behållare med exponerade portar ska du se till att köra varje behållare med en annan exponerad port. Kör till exempel den första behållaren på port 5000 och den andra behållaren på port 5001.

Du kan ha den här behållaren och en annan Azure Cognitive Services-behållare som körs på värden tillsammans. Du kan också ha flera behållare av samma Cognitive Services-behållare som kör.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du startar eller kör behållaren kan det uppstå problem. Använd en utmatnings [montering](speech-container-configuration.md#mount-settings) och aktivera loggning. På så sätt kan behållaren generera loggfiler som kan vara till hjälp vid fel sökning av problem.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Tal behållarna skickar fakturerings information till Azure med hjälp av en *tal* resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Configure containers](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för att ladda ned, installera och köra tal behållare. Sammanfattningsvis:

* Tal innehåller fyra Linux-behållare för Docker, vilket kapslar olika funktioner:
  * *Tal till text*
  * *Custom Speech till text*
  * *Text till tal*
  * *Anpassad text till tal*
* Behållar avbildningar hämtas från behållar registret i Azure.
* Behållar avbildningar körs i Docker.
* Du kan använda antingen REST API eller SDK för att anropa åtgärder i tal behållare genom att ange behållarens värd-URI.
* Du måste ange fakturerings information när du instansierar en behållare.

> [!IMPORTANT]
>  Cognitive Services behållare är inte licensierade att köras utan att vara anslutna till Azure för mätning. Kunderna behöver göra det möjligt för behållarna att kommunicera fakturerings information med mät tjänsten hela tiden. Cognitive Services behållare skickar inte kund information (t. ex. den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [Konfigurera behållare](speech-container-configuration.md) för konfigurations inställningar
* Lär dig hur du [använder tal tjänst behållare med Kubernetes och Helm](speech-container-howto-on-premises.md)
* Använd fler [Cognitive Services behållare](../cognitive-services-container-support.md)
