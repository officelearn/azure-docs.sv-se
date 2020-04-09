---
title: Installera talbehållare - Taltjänst
titleSuffix: Azure Cognitive Services
description: Installera och kör talbehållare. Tal-till-text transkriberar ljudströmmar till text i realtid som dina program, verktyg eller enheter kan använda eller visa. Text-till-tal konverterar indatatext till människoliknande syntetiserat tal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2caae4fecdf13a1833f23cf9423cf3ded67f6f72
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879041"
---
# <a name="install-and-run-speech-service-containers-preview"></a>Installera och köra taltjänstbehållare (förhandsversion)

Med behållare kan du köra några av taltjänst-API:erna i din egen miljö. Behållare är bra för specifika säkerhets- och datastyrningskrav. I den här artikeln får du lära dig hur du hämtar, installerar och kör en talbehållare.

Talbehållare gör det möjligt för kunder att skapa en talprogramarkitektur som är optimerad för både robusta molnfunktioner och kantlokalitet. Det finns fyra olika behållare tillgängliga. De två standardbehållarna är **Tal-till-text** och **Text-till-tal**. De två anpassade behållarna är **Anpassad tal-till-text** och **Anpassad text-till-tal.**

> [!IMPORTANT]
> Alla talbehållare erbjuds för närvarande som en del av en [offentlig "Gated" Preview](../cognitive-services-container-support.md#public-gated-preview-container-registry-containerpreviewazurecrio). Ett meddelande kommer att göras när talbehållarna går vidare till allmän tillgänglighet (GA).

| Funktion | Funktioner | Senaste |
|--|--|--|
| Tal till text | Transkriberar kontinuerlig tal- eller batchljudinspelningar i realtid till text med mellanliggande resultat. | 2.1.1 |
| Anpassad tal-till-text | Med hjälp av en anpassad modell från [portalen Anpassat tal](https://speech.microsoft.com/customspeech)transkriberas kontinuerliga tal- eller batchljudinspelningar i realtid till text med mellanliggande resultat. | 2.1.1 |
| Text till tal | Konverterar text till naturligt klingande tal med oformaterad textinmatning eller SSML (Speech Synthesis Markup Language). | 1.3.0 |
| Anpassad text till tal | Med hjälp av en anpassad modell från [Custom Voice-portalen](https://aka.ms/custom-voice-portal)konverteras text till naturligt klingande tal med oformaterad textinmatning eller SSML (Speech Synthesis Markup Language). | 1.3.0 |

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Följande förutsättningar innan du använder Talbehållare:

| Krävs | Syfte |
|--|--|
| Docker-motorn | Du måste dockermotorn installerad på en [värddator](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras så att behållarna kan ansluta till och skicka faktureringsdata till Azure. <br><br> **I Windows**måste Docker också konfigureras för att stödja Linux-behållare.<br><br> |
| Förtrogenhet med Docker | Du bör ha en grundläggande förståelse för Docker-begrepp, till exempel register, databaser, behållare och `docker` behållaravbildningar, samt kunskap om grundläggande kommandon. |
| Talresurs | För att kunna använda dessa behållare måste du ha:<br><br>En Azure _Speech-resurs_ för att hämta den associerade API-nyckeln och slutpunkts-URI. Båda värdena är tillgängliga på Azure-portalens **talöversikt** och nycklar. De är båda skyldiga att starta behållaren.<br><br>**{API_KEY}**: En av de två tillgängliga resursnycklarna på sidan **Nycklar**<br><br>**{ENDPOINT_URI}**: Slutpunkten som anges på **sidan Översikt** |

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Fyll i och skicka formuläret Begäran om begäran om begäran om åtkomst till behållaren för [kognitiva tjänster.](https://aka.ms/speechcontainerspreview/) 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Avancerat stöd för vektortillägg

Värden **host** är den dator som kör docker-behållaren. Värden *måste ha stöd för* [avancerade vektortillägg](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2). Du kan söka efter AVX2-stöd på Linux-värdar med följande kommando:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> Värddatorn *krävs* för att stödja AVX2. Behållaren *fungerar inte* korrekt utan AVX2-stöd.

### <a name="container-requirements-and-recommendations"></a>Krav och rekommendationer för behållare

I följande tabell beskrivs den minsta och rekommenderade fördelningen av resurser för varje talbehållare.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Tal till text | 2 kärnor, 2 GB minne | 4-kärniga, 4 GB minne |

# <a name="custom-speech-to-text"></a>[Anpassad tal-till-text](#tab/cstt)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Anpassad tal-till-text | 2 kärnor, 2 GB minne | 4-kärniga, 4 GB minne |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
| Anpassad text till tal | 1 kärna, 2 GB minne | 2 kärnor, 3 GB minne |

***

* Varje kärna måste vara minst 2,6 gigahertz (GHz) eller snabbare.

Kärna och minne `--cpus` motsvarar `--memory` och inställningar, som används `docker run` som en del av kommandot.

> [!NOTE]
> Det minsta och rekommenderade baseras bort från Docker-gränser, *inte* värddatorresurserna. Tal-till-text-behållare minnesmappningsdelar av en stor språkmodell, och det *rekommenderas* att hela filen får plats i minnet, vilket är ytterligare 4-6 GB. Dessutom kan den första körningen av endera behållaren ta längre tid, eftersom modeller söks in i minnet.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med`docker pull`

Behållaravbildningar för Tal finns i följande behållarregister.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

| Container | Lagringsplats |
|-----------|------------|
| Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest` |

# <a name="custom-speech-to-text"></a>[Anpassad tal-till-text](#tab/cstt)

| Container | Lagringsplats |
|-----------|------------|
| Anpassad tal-till-text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest` |

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

| Container | Lagringsplats |
|-----------|------------|
| Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest` |

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

| Container | Lagringsplats |
|-----------|------------|
| Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest` |

***

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-speech-containers"></a>Docker dra för talbehållarna

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

#### <a name="docker-pull-for-the-speech-to-text-container"></a>Docker-dragningskraft för behållaren tal-till-text

Använd [docker pull-kommandot](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta en behållaravbildning från registret Förhandsgranskning av behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text:latest
```

> [!IMPORTANT]
> Taggen `latest` drar `en-US` i språken. Ytterligare språk finns i [Tal-till-text-språk](#speech-to-text-locales).

#### <a name="speech-to-text-locales"></a>Tal-till-text-språk

Alla taggar, `latest` förutom är i följande format och är skiftlägeskänsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
2.1.1-amd64-en-us-preview
```

Information om alla språk som stöds i **behållaren för tal-till-text** finns i [bildtaggar för tal till text](../containers/container-image-tags.md#speech-to-text).

# <a name="custom-speech-to-text"></a>[Anpassad tal-till-text](#tab/cstt)

#### <a name="docker-pull-for-the-custom-speech-to-text-container"></a>Docker-dragningskraft för behållaren för anpassat tal till text

Använd [docker pull-kommandot](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta en behållaravbildning från registret Förhandsgranskning av behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text:latest
```

> [!NOTE]
> Och `locale` `voice` för anpassade talbehållare bestäms av den anpassade modellen som intas av behållaren.

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

#### <a name="docker-pull-for-the-text-to-speech-container"></a>Docker-dragningskraft för text-till-tal-behållaren

Använd [docker pull-kommandot](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta en behållaravbildning från registret Förhandsgranskning av behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech:latest
```

> [!IMPORTANT]
> Taggen `latest` drar `en-US` i språken och `jessarus` rösten. Ytterligare språk finns i [Text-till-tal-språk](#text-to-speech-locales).

#### <a name="text-to-speech-locales"></a>Text-till-tal-språk

Alla taggar, `latest` förutom är i följande format och är skiftlägeskänsliga:

```
<major>.<minor>.<patch>-<platform>-<locale>-<voice>-<prerelease>
```

Följande tagg är ett exempel på formatet:

```
1.3.0-amd64-en-us-jessarus-preview
```

Information om alla språk och motsvarande röster som stöds i **text-till-tal-behållaren** finns i [text-till-tal-bildtaggar](../containers/container-image-tags.md#text-to-speech).

> [!IMPORTANT]
> När du konstruerar ett *STANDARD-TAL* HTTP POST kräver [SSML-meddelandet (Speech Synthesis Markup Language)](speech-synthesis-markup.md) ett `voice` element med ett `name` attribut. Värdet är motsvarande behållarlokal och röst, även känd som ["kortnamn".](language-support.md#standard-voices) Taggen `latest` skulle till exempel ha `en-US-JessaRUS`ett röstnamn på .

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

#### <a name="docker-pull-for-the-custom-text-to-speech-container"></a>Docker-dragningskraft för behållaren för anpassad text till tal

Använd [docker pull-kommandot](https://docs.docker.com/engine/reference/commandline/pull/) för att hämta en behållaravbildning från registret Förhandsgranskning av behållare.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech:latest
```

> [!NOTE]
> Och `locale` `voice` för anpassade talbehållare bestäms av den anpassade modellen som intas av behållaren.

***

## <a name="how-to-use-the-container"></a>Så här använder du behållaren

När behållaren finns på [värddatorn](#the-host-computer)använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run)med de faktureringsinställningar som krävs. Fler [exempel](speech-container-configuration.md#example-docker-run-commands) `docker run` på kommandot är tillgängliga.
1. [Fråga behållarens förutsägelseslutpunkt](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med`docker run`

Använd [kommandot docker run](https://docs.docker.com/engine/reference/commandline/run/) för att köra behållaren. Se [samla in nödvändiga parametrar](#gathering-required-parameters) för `{Endpoint_URI}` information `{API_Key}` om hur du får och värden. Ytterligare [exempel](speech-container-configuration.md#example-docker-run-commands) `docker run` på kommandot finns också tillgängliga.

# <a name="speech-to-text"></a>[Tal till text](#tab/stt)

Kör följande `docker run` kommando om du vill köra behållaren *Tal-till-text.*

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *tal-till-text-behållare* från behållaravbildningen.
* Allokerar 4 CPU-kärnor och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn.

# <a name="custom-speech-to-text"></a>[Anpassad tal-till-text](#tab/cstt)

Behållaren *Anpassad tal-till-text* är beroende av en anpassad talmodell. Den anpassade modellen måste ha [tränats](how-to-custom-speech-train-model.md) med hjälp av den [anpassade talportalen](https://speech.microsoft.com/customspeech).

> [!IMPORTANT]
> Custom Speech-modellen måste tränas från någon av följande modellversioner:
> * **20181201 (v3.3 Unified)**
> * **20190520 (v4.14 Enhetlig)**
> * **20190701 (v4.17 Enhetlig)**<br>
> ![Anpassad behållaremodell för taltåg](media/custom-speech/custom-speech-train-model-container-scoped.png)

Det anpassade **talmodell-ID:et** krävs för att köra behållaren. Den finns på **sidan Utbildning** i den anpassade talportalen. Från den anpassade talportalen navigerar du till sidan **Utbildning** och väljer modellen.
<br>

![Anpassad sida för talträning](media/custom-speech/custom-speech-model-training.png)

Hämta **modell-ID:et** som `ModelId` ska användas `docker run` som argument till parametern för kommandot.
<br>

![Information om anpassad talmodell](media/custom-speech/custom-speech-model-details.png)

Följande tabell representerar `docker run` de olika parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värddatorns [volymmontering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Anpassade **talmodell-ID** från **sidan Utbildning** i den anpassade talportalen. |
| `{ENDPOINT_URI}` | Slutpunkten krävs för mätning och fakturering. Mer information finns i [samla in nödvändiga parametrar](#gathering-required-parameters). |
| `{API_KEY}` | API-nyckeln krävs. Mer information finns i [samla in nödvändiga parametrar](#gathering-required-parameters). |

Om du vill köra behållaren *Anpassad tal-till-text* kör du följande `docker run` kommando:

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

* Kör en *anpassad tal-till-text-behållare* från behållaravbildningen.
* Allokerar 4 CPU-kärnor och 4 GB minne.
* Läser in modellen *Anpassad tal-till-text* från volyminmatningsfästet, till exempel *C:\CustomSpeech*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Hämtar modellen med `ModelId` tanke på (om den inte finns på volymfästet).
* Om den anpassade modellen har `ModelId` hämtats tidigare ignoreras den.
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn.

# <a name="text-to-speech"></a>[Text till tal](#tab/tts)

Om du vill köra behållaren *Text-till-tal* kör du följande `docker run` kommando.

```bash
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Det här kommandot:

* Kör en *text-till-tal-behållare* från behållaravbildningen.
* Allokerar 2 CPU-kärnor och en gigabyte (GB) minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn.

# <a name="custom-text-to-speech"></a>[Anpassad text till tal](#tab/ctts)

Behållaren *Anpassad text till tal* är beroende av en anpassad röstmodell. Den anpassade modellen måste ha [tränats](how-to-custom-voice-create-voice.md) med hjälp av den [anpassade röstportalen](https://aka.ms/custom-voice-portal). Det anpassade **röstmodell-ID:et** krävs för att köra behållaren. Den finns på **sidan Utbildning** i den anpassade röstportalen. Från den anpassade röstportalen navigerar du till sidan **Utbildning** och väljer modellen.
<br>

![Anpassad röstträningssida](media/custom-voice/custom-voice-model-training.png)

Hämta **modell-ID:et** som `ModelId` ska användas som argument till parametern för dockerkörningskommandot.
<br>

![Detaljer för anpassad röstmodell](media/custom-voice/custom-voice-model-details.png)

Följande tabell representerar `docker run` de olika parametrarna och deras motsvarande beskrivningar:

| Parameter | Beskrivning |
|---------|---------|
| `{VOLUME_MOUNT}` | Värddatorns [volymmontering](https://docs.docker.com/storage/volumes/), som docker använder för att bevara den anpassade modellen. Till exempel *C:\CustomSpeech* där *C-enheten* finns på värddatorn. |
| `{MODEL_ID}` | Anpassade **talmodell-ID** från **sidan Utbildning** i den anpassade röstportalen. |
| `{ENDPOINT_URI}` | Slutpunkten krävs för mätning och fakturering. Mer information finns i [samla in nödvändiga parametrar](#gathering-required-parameters). |
| `{API_KEY}` | API-nyckeln krävs. Mer information finns i [samla in nödvändiga parametrar](#gathering-required-parameters). |

Om du vill köra behållaren Anpassad text `docker run` till *tal* kör du följande kommando:

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

* Kör en *anpassad text-till-tal-behållare* från behållaravbildningen.
* Allokerar 2 CPU-kärnor och en gigabyte (GB) minne.
* Läser in modellen *Anpassad text till tal* från volyminmatningsfästet, till exempel *C:\CustomVoice*.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Hämtar modellen med `ModelId` tanke på (om den inte finns på volymfästet).
* Om den anpassade modellen har `ModelId` hämtats tidigare ignoreras den.
* Tar automatiskt bort behållaren när den har avslutats. Behållaravbildningen är fortfarande tillgänglig på värddatorn.

***

> [!IMPORTANT]
> Alternativen `Eula` `Billing`, `ApiKey` och måste anges för att behållaren ska kunna köras. Annars startar inte behållaren.  Mer information finns i [Fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarens förutsägelseslutpunkt

| Containrar | Url till SDK-värd | Protokoll |
|--|--|--|
| Tal-till-text och anpassad tal-till-text | `ws://localhost:5000` | WS |
| Text-till-tal och Anpassad text-till-tal | `http://localhost:5000` | HTTP |

Mer information om hur du använder WSS- och HTTPS-protokoll finns i [containersäkerhet](../cognitive-services-container-support.md#azure-cognitive-services-container-security).

[!INCLUDE [Query Speech-to-text container endpoint](includes/speech-to-text-container-query-endpoint.md)]

### <a name="text-to-speech-or-custom-text-to-speech"></a>Text-till-tal eller Anpassad text-till-tal

[!INCLUDE [Query Text-to-speech container endpoint](includes/text-to-speech-container-query-endpoint.md)]

### <a name="run-multiple-containers-on-the-same-host"></a>Köra flera behållare på samma värd

Om du tänker köra flera behållare med exponerade portar, se till att köra varje behållare med en annan exponerad port. Kör till exempel den första behållaren på port 5000 och den andra behållaren på port 5001.

Du kan ha den här behållaren och en annan Azure Cognitive Services-behållare som körs på värdvärden tillsammans. Du kan också ha flera behållare med samma Cognitive Services-behållare som körs.

[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa containern

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du startar eller kör behållaren kan det uppstå problem. Använd ett [utdatafäste](speech-container-configuration.md#mount-settings) och aktivera loggning. Om du gör det kan behållaren generera loggfiler som är användbara vid felsökning av problem.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturering

Talbehållarna skickar faktureringsinformation till Azure med hjälp av en *talresurs* på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om dessa alternativ finns i [Konfigurera behållare](speech-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbetsflöden för att hämta, installera och köra Talbehållare. Sammanfattningsvis:

* Tal ger fyra Linux-behållare för Docker, kapsla in olika funktioner:
  * *Tal till text*
  * *Anpassad tal-till-text*
  * *Text till tal*
  * *Anpassad text till tal*
* Behållaravbildningar hämtas från behållarregistret i Azure.
* Behållaravbildningar körs i Docker.
* Oavsett om du använder REST API (endast text-till-tal) eller SDK (tal-till-text eller text-till-tal) anger du behållarens värd-URI. 
* Du måste ange faktureringsinformation när du instansierar en behållare.

> [!IMPORTANT]
>  Cognitive Services-behållare är inte licensierade för att köras utan att vara anslutna till Azure för mätning. Kunderna måste alltid kunna kommunicera faktureringsinformation med mätartjänsten. Cognitive Services-behållare skickar inte kunddata (t.ex. den bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](speech-container-configuration.md) för konfigurationsinställningar
* Lär dig hur du [använder talservicebehållare med Kubernetes och Helm](speech-container-howto-on-premises.md)
* Använda fler [Cognitive Services-behållare](../cognitive-services-container-support.md)
