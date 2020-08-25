---
title: Taggar för Cognitive Services-containeravbildningar
titleSuffix: Azure Cognitive Services
description: En omfattande lista över alla avbildnings taggar för kognitiva tjänst behållare.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e0df3de5eadfd2cc5c00c52da5c4942b42a68b2b
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722576"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Avbildnings taggar för Azure Cognitive Services container

Azure Cognitive Services erbjuder många behållar avbildningar. Behållar registren och motsvarande databaser varierar mellan behållar avbildningar. Varje behållar avbildnings namn innehåller flera taggar. En behållar avbildnings tag är en mekanism för version av behållar avbildningen. Den här artikeln är avsedd att användas som en omfattande referens för att visa alla Cognitive Services behållar avbildningar och de tillgängliga taggarna.

> [!TIP]
> När du använder ska du vara noga med att tänka på [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) SKIFT läge för behållar registret, databasen, behållar avbildningens namn och motsvarande tagg – eftersom de är **SKIFT läges känsliga**.

## <a name="anomaly-detector"></a>Avvikelseidentifiering

Behållar avbildningen för [avvikelse detektor][ad-containers] finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `anomaly-detector` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |

## <a name="computer-vision"></a>Visuellt innehåll

Du kan hitta avbildningen [visuellt innehåll][cv-containers] Read OCR-behållare i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-read` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `containerpreview.azurecr.io/microsoft/cognitive-services-read` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest ( (2.0.013250001-amd64-preview)` | • Öka minnes användningen ytterligare för containern. |
|                                          | • Extern cache krävs för installation av multi-poddar. Du kan till exempel konfigurera Redis för cachelagring. |
|                                          | • Åtgärda eventuella problem som saknas när Redis cache är inställd och ResultExpirationPeriod = 0.  |
|                                          | • Ta bort text storleks begränsningen för begäran för 26MB. Container kan nu acceptera >26MB-filer.  |
|                                          | • Lägg till tidsstämpel och build-version till konsol loggning.  |
| `1.1.013050001-amd64-preview`            | * Har lagts till ReadEngineConfig: ResultExpirationPeriod container-initierings konfiguration för att ange när systemet ska rensa igenkännings resultat. |
|                                          | Inställningen är i timmar och standardvärdet är 48hr.   |
|                                          |   Inställningen kan minska minnes användningen för lagring av resultat, särskilt när behållare i minnet används.  |
|                                          |    * Exempel 1. ReadEngineConfig: ResultExpirationPeriod = 1 rensar systemet igenkännings resultatet 1 tim efter processen.   |
|                                          |    * Exempel 2. ReadEngineConfig: ResultExpirationPeriod = 0 rensar systemet igenkännings resultatet efter att resultatet har hämtats.  |
|                                          | Ett internt Server fel 500 har åtgärd ATS när ogiltigt bild format skickades till systemet. Det kommer nu att returnera ett 400-fel:   |
|                                          | `{`  |
|                                          | `"error": {`  |
|                                          |      `"code": "InvalidImageSize",`  |
|                                          |      `"message": "Image must be between 1024 and 209715200 bytes."`  |
|                                          |          `}`  |
|                                          | `}`  |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Ansikte

[Ansikts][fa-containers] behållar avbildningen finns i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-face` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `containerpreview.azurecr.io/microsoft/cognitive-services-face` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Formigenkänning

[Formulär tolkens][fr-containers] behållar avbildning finns i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-form-recognizer` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

[Luis][lu-containers] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `luis` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/luis` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Custom Speech till text

Behållar avbildningen [Custom Speech till text][sp-cstt] finns i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-custom-speech-to-text` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar            | Kommentarer |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Anpassad text till tal

Du hittar den [anpassade text till tal-][sp-ctts] behållar avbildningen i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-custom-text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar            | Kommentarer |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Tal till text

Du hittar en behållar avbildning från [tal till text][sp-stt] i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-speech-to-text` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                  | Kommentarer                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Behållar avbildning med `en-US` språkvarianten. |
| `2.2.0-amd64-ar-ae-preview` | Behållar avbildning med `ar-AE` språkvarianten. |
| `2.2.0-amd64-ar-eg-preview` | Behållar avbildning med `ar-EG` språkvarianten. |
| `2.2.0-amd64-ar-kw-preview` | Behållar avbildning med `ar-KW` språkvarianten. |
| `2.2.0-amd64-ar-qa-preview` | Behållar avbildning med `ar-QA` språkvarianten. |
| `2.2.0-amd64-ar-sa-preview` | Behållar avbildning med `ar-SA` språkvarianten. |
| `2.2.0-amd64-ca-es-preview` | Behållar avbildning med `ca-ES` språkvarianten. |
| `2.2.0-amd64-da-dk-preview` | Behållar avbildning med `da-DK` språkvarianten. |
| `2.2.0-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `2.2.0-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `2.2.0-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `2.2.0-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `2.2.0-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `2.2.0-amd64-en-nz-preview` | Behållar avbildning med `en-NZ` språkvarianten. |
| `2.2.0-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `2.2.0-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `2.2.0-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `2.2.0-amd64-fi-fi-preview` | Behållar avbildning med `fi-FI` språkvarianten. |
| `2.2.0-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `2.2.0-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `2.2.0-amd64-gu-in-preview` | Behållar avbildning med `gu-IN` språkvarianten. |
| `2.2.0-amd64-hi-in-preview` | Behållar avbildning med `hi-IN` språkvarianten. |
| `2.2.0-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `2.2.0-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `2.2.0-amd64-ko-kr-preview` | Behållar avbildning med `ko-KR` språkvarianten. |
| `2.2.0-amd64-mr-in-preview` | Behållar avbildning med `mr-IN` språkvarianten. |
| `2.2.0-amd64-nb-no-preview` | Behållar avbildning med `nb-NO` språkvarianten. |
| `2.2.0-amd64-nl-nl-preview` | Behållar avbildning med `nl-NL` språkvarianten. |
| `2.2.0-amd64-pl-pl-preview` | Behållar avbildning med `pl-PL` språkvarianten. |
| `2.2.0-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `2.2.0-amd64-pt-pt-preview` | Behållar avbildning med `pt-PT` språkvarianten. |
| `2.2.0-amd64-ru-ru-preview` | Behållar avbildning med `ru-RU` språkvarianten. |
| `2.2.0-amd64-sv-se-preview` | Behållar avbildning med `sv-SE` språkvarianten. |
| `2.2.0-amd64-ta-in-preview` | Behållar avbildning med `ta-IN` språkvarianten. |
| `2.2.0-amd64-te-in-preview` | Behållar avbildning med `te-IN` språkvarianten. |
| `2.2.0-amd64-th-th-preview` | Behållar avbildning med `th-TH` språkvarianten. |
| `2.2.0-amd64-tr-tr-preview` | Behållar avbildning med `tr-TR` språkvarianten. |
| `2.2.0-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `2.2.0-amd64-zh-hk-preview` | Behållar avbildning med `zh-HK` språkvarianten. |
| `2.2.0-amd64-zh-tw-preview` | Behållar avbildning med `zh-TW` språkvarianten. |
| `2.1.1-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `2.1.1-amd64-ar-ae-preview` | Behållar avbildning med `ar-AE` språkvarianten. |
| `2.1.1-amd64-ar-eg-preview` | Behållar avbildning med `ar-EG` språkvarianten. |
| `2.1.1-amd64-ar-kw-preview` | Behållar avbildning med `ar-KW` språkvarianten. |
| `2.1.1-amd64-ar-qa-preview` | Behållar avbildning med `ar-QA` språkvarianten. |
| `2.1.1-amd64-ar-sa-preview` | Behållar avbildning med `ar-SA` språkvarianten. |
| `2.1.1-amd64-ca-es-preview` | Behållar avbildning med `ca-ES` språkvarianten. |
| `2.1.1-amd64-da-dk-preview` | Behållar avbildning med `da-DK` språkvarianten. |
| `2.1.1-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `2.1.1-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `2.1.1-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `2.1.1-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `2.1.1-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `2.1.1-amd64-en-nz-preview` | Behållar avbildning med `en-NZ` språkvarianten. |
| `2.1.1-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `2.1.1-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `2.1.1-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `2.1.1-amd64-fi-fi-preview` | Behållar avbildning med `fi-FI` språkvarianten. |
| `2.1.1-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `2.1.1-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `2.1.1-amd64-gu-in-preview` | Behållar avbildning med `gu-IN` språkvarianten. |
| `2.1.1-amd64-hi-in-preview` | Behållar avbildning med `hi-IN` språkvarianten. |
| `2.1.1-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `2.1.1-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `2.1.1-amd64-ko-kr-preview` | Behållar avbildning med `ko-KR` språkvarianten. |
| `2.1.1-amd64-mr-in-preview` | Behållar avbildning med `mr-IN` språkvarianten. |
| `2.1.1-amd64-nb-no-preview` | Behållar avbildning med `nb-NO` språkvarianten. |
| `2.1.1-amd64-nl-nl-preview` | Behållar avbildning med `nl-NL` språkvarianten. |
| `2.1.1-amd64-pl-pl-preview` | Behållar avbildning med `pl-PL` språkvarianten. |
| `2.1.1-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `2.1.1-amd64-pt-pt-preview` | Behållar avbildning med `pt-PT` språkvarianten. |
| `2.1.1-amd64-ru-ru-preview` | Behållar avbildning med `ru-RU` språkvarianten. |
| `2.1.1-amd64-sv-se-preview` | Behållar avbildning med `sv-SE` språkvarianten. |
| `2.1.1-amd64-ta-in-preview` | Behållar avbildning med `ta-IN` språkvarianten. |
| `2.1.1-amd64-te-in-preview` | Behållar avbildning med `te-IN` språkvarianten. |
| `2.1.1-amd64-th-th-preview` | Behållar avbildning med `th-TH` språkvarianten. |
| `2.1.1-amd64-tr-tr-preview` | Behållar avbildning med `tr-TR` språkvarianten. |
| `2.1.1-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `2.1.1-amd64-zh-hk-preview` | Behållar avbildning med `zh-HK` språkvarianten. |
| `2.1.1-amd64-zh-tw-preview` | Behållar avbildning med `zh-TW` språkvarianten. |
| `2.1.0-amd64-ar-ae-preview` | Behållar avbildning med `ar-AE` språkvarianten. |
| `2.1.0-amd64-ar-eg-preview` | Behållar avbildning med `ar-EG` språkvarianten. |
| `2.1.0-amd64-ar-kw-preview` | Behållar avbildning med `ar-KW` språkvarianten. |
| `2.1.0-amd64-ar-qa-preview` | Behållar avbildning med `ar-QA` språkvarianten. |
| `2.1.0-amd64-ar-sa-preview` | Behållar avbildning med `ar-SA` språkvarianten. |
| `2.1.0-amd64-ca-es-preview` | Behållar avbildning med `ca-ES` språkvarianten. |
| `2.1.0-amd64-da-dk-preview` | Behållar avbildning med `da-DK` språkvarianten. |
| `2.1.0-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `2.1.0-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `2.1.0-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `2.1.0-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `2.1.0-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `2.1.0-amd64-en-nz-preview` | Behållar avbildning med `en-NZ` språkvarianten. |
| `2.1.0-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `2.1.0-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `2.1.0-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `2.1.0-amd64-fi-fi-preview` | Behållar avbildning med `fi-FI` språkvarianten. |
| `2.1.0-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `2.1.0-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `2.1.0-amd64-gu-in-preview` | Behållar avbildning med `gu-IN` språkvarianten. |
| `2.1.0-amd64-hi-in-preview` | Behållar avbildning med `hi-IN` språkvarianten. |
| `2.1.0-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `2.1.0-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `2.1.0-amd64-ko-kr-preview` | Behållar avbildning med `ko-KR` språkvarianten. |
| `2.1.0-amd64-mr-in-preview` | Behållar avbildning med `mr-IN` språkvarianten. |
| `2.1.0-amd64-nb-no-preview` | Behållar avbildning med `nb-NO` språkvarianten. |
| `2.1.0-amd64-nl-nl-preview` | Behållar avbildning med `nl-NL` språkvarianten. |
| `2.1.0-amd64-pl-pl-preview` | Behållar avbildning med `pl-PL` språkvarianten. |
| `2.1.0-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `2.1.0-amd64-pt-pt-preview` | Behållar avbildning med `pt-PT` språkvarianten. |
| `2.1.0-amd64-ru-ru-preview` | Behållar avbildning med `ru-RU` språkvarianten. |
| `2.1.0-amd64-sv-se-preview` | Behållar avbildning med `sv-SE` språkvarianten. |
| `2.1.0-amd64-ta-in-preview` | Behållar avbildning med `ta-IN` språkvarianten. |
| `2.1.0-amd64-te-in-preview` | Behållar avbildning med `te-IN` språkvarianten. |
| `2.1.0-amd64-th-th-preview` | Behållar avbildning med `th-TH` språkvarianten. |
| `2.1.0-amd64-tr-tr-preview` | Behållar avbildning med `tr-TR` språkvarianten. |
| `2.1.0-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `2.1.0-amd64-zh-hk-preview` | Behållar avbildning med `zh-HK` språkvarianten. |
| `2.1.0-amd64-zh-tw-preview` | Behållar avbildning med `zh-TW` språkvarianten. |
| `2.0.3-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `2.0.2-amd64-ar-ae-preview` | Behållar avbildning med `ar-AE` språkvarianten. |
| `2.0.2-amd64-ar-eg-preview` | Behållar avbildning med `ar-EG` språkvarianten. |
| `2.0.2-amd64-ar-kw-preview` | Behållar avbildning med `ar-KW` språkvarianten. |
| `2.0.2-amd64-ar-qa-preview` | Behållar avbildning med `ar-QA` språkvarianten. |
| `2.0.2-amd64-ar-sa-preview` | Behållar avbildning med `ar-SA` språkvarianten. |
| `2.0.2-amd64-ca-es-preview` | Behållar avbildning med `ca-ES` språkvarianten. |
| `2.0.2-amd64-da-dk-preview` | Behållar avbildning med `da-DK` språkvarianten. |
| `2.0.2-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `2.0.2-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `2.0.2-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `2.0.2-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `2.0.2-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `2.0.2-amd64-en-nz-preview` | Behållar avbildning med `en-NZ` språkvarianten. |
| `2.0.2-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `2.0.2-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `2.0.2-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `2.0.2-amd64-fi-fi-preview` | Behållar avbildning med `fi-FI` språkvarianten. |
| `2.0.2-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `2.0.2-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `2.0.2-amd64-gu-in-preview` | Behållar avbildning med `gu-IN` språkvarianten. |
| `2.0.2-amd64-hi-in-preview` | Behållar avbildning med `hi-IN` språkvarianten. |
| `2.0.2-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `2.0.2-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `2.0.2-amd64-ko-kr-preview` | Behållar avbildning med `ko-KR` språkvarianten. |
| `2.0.2-amd64-mr-in-preview` | Behållar avbildning med `mr-IN` språkvarianten. |
| `2.0.2-amd64-nb-no-preview` | Behållar avbildning med `nb-NO` språkvarianten. |
| `2.0.2-amd64-nl-nl-preview` | Behållar avbildning med `nl-NL` språkvarianten. |
| `2.0.2-amd64-pl-pl-preview` | Behållar avbildning med `pl-PL` språkvarianten. |
| `2.0.2-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `2.0.2-amd64-pt-pt-preview` | Behållar avbildning med `pt-PT` språkvarianten. |
| `2.0.2-amd64-ru-ru-preview` | Behållar avbildning med `ru-RU` språkvarianten. |
| `2.0.2-amd64-sv-se-preview` | Behållar avbildning med `sv-SE` språkvarianten. |
| `2.0.2-amd64-ta-in-preview` | Behållar avbildning med `ta-IN` språkvarianten. |
| `2.0.2-amd64-te-in-preview` | Behållar avbildning med `te-IN` språkvarianten. |
| `2.0.2-amd64-th-th-preview` | Behållar avbildning med `th-TH` språkvarianten. |
| `2.0.2-amd64-tr-tr-preview` | Behållar avbildning med `tr-TR` språkvarianten. |
| `2.0.2-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `2.0.2-amd64-zh-hk-preview` | Behållar avbildning med `zh-HK` språkvarianten. |
| `2.0.2-amd64-zh-tw-preview` | Behållar avbildning med `zh-TW` språkvarianten. |
| `2.0.1-amd64-ar-ae-preview` | Behållar avbildning med `ar-AE` språkvarianten. |
| `2.0.1-amd64-ar-eg-preview` | Behållar avbildning med `ar-EG` språkvarianten. |
| `2.0.1-amd64-ar-kw-preview` | Behållar avbildning med `ar-KW` språkvarianten. |
| `2.0.1-amd64-ar-qa-preview` | Behållar avbildning med `ar-QA` språkvarianten. |
| `2.0.1-amd64-ar-sa-preview` | Behållar avbildning med `ar-SA` språkvarianten. |
| `2.0.1-amd64-ca-es-preview` | Behållar avbildning med `ca-ES` språkvarianten. |
| `2.0.1-amd64-da-dk-preview` | Behållar avbildning med `da-DK` språkvarianten. |
| `2.0.1-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `2.0.1-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `2.0.1-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `2.0.1-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `2.0.1-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `2.0.1-amd64-en-nz-preview` | Behållar avbildning med `en-NZ` språkvarianten. |
| `2.0.1-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `2.0.1-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `2.0.1-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `2.0.1-amd64-fi-fi-preview` | Behållar avbildning med `fi-FI` språkvarianten. |
| `2.0.1-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `2.0.1-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `2.0.1-amd64-gu-in-preview` | Behållar avbildning med `gu-IN` språkvarianten. |
| `2.0.1-amd64-hi-in-preview` | Behållar avbildning med `hi-IN` språkvarianten. |
| `2.0.1-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `2.0.1-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `2.0.1-amd64-ko-kr-preview` | Behållar avbildning med `ko-KR` språkvarianten. |
| `2.0.1-amd64-mr-in-preview` | Behållar avbildning med `mr-IN` språkvarianten. |
| `2.0.1-amd64-nb-no-preview` | Behållar avbildning med `nb-NO` språkvarianten. |
| `2.0.1-amd64-nl-nl-preview` | Behållar avbildning med `nl-NL` språkvarianten. |
| `2.0.1-amd64-pl-pl-preview` | Behållar avbildning med `pl-PL` språkvarianten. |
| `2.0.1-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `2.0.1-amd64-pt-pt-preview` | Behållar avbildning med `pt-PT` språkvarianten. |
| `2.0.1-amd64-ru-ru-preview` | Behållar avbildning med `ru-RU` språkvarianten. |
| `2.0.1-amd64-sv-se-preview` | Behållar avbildning med `sv-SE` språkvarianten. |
| `2.0.1-amd64-ta-in-preview` | Behållar avbildning med `ta-IN` språkvarianten. |
| `2.0.1-amd64-te-in-preview` | Behållar avbildning med `te-IN` språkvarianten. |
| `2.0.1-amd64-th-th-preview` | Behållar avbildning med `th-TH` språkvarianten. |
| `2.0.1-amd64-tr-tr-preview` | Behållar avbildning med `tr-TR` språkvarianten. |
| `2.0.1-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `2.0.1-amd64-zh-hk-preview` | Behållar avbildning med `zh-HK` språkvarianten. |
| `2.0.1-amd64-zh-tw-preview` | Behållar avbildning med `zh-TW` språkvarianten. |
| `2.0.0-amd64-ar-eg-preview` | Behållar avbildning med `ar-EG` språkvarianten. |
| `2.0.0-amd64-ca-es-preview` | Behållar avbildning med `ca-ES` språkvarianten. |
| `2.0.0-amd64-da-dk-preview` | Behållar avbildning med `da-DK` språkvarianten. |
| `2.0.0-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `2.0.0-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `2.0.0-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `2.0.0-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `2.0.0-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `2.0.0-amd64-en-nz-preview` | Behållar avbildning med `en-NZ` språkvarianten. |
| `2.0.0-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `2.0.0-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `2.0.0-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `2.0.0-amd64-fi-fi-preview` | Behållar avbildning med `fi-FI` språkvarianten. |
| `2.0.0-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `2.0.0-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `2.0.0-amd64-hi-in-preview` | Behållar avbildning med `hi-IN` språkvarianten. |
| `2.0.0-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `2.0.0-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `2.0.0-amd64-ko-kr-preview` | Behållar avbildning med `ko-KR` språkvarianten. |
| `2.0.0-amd64-nb-no-preview` | Behållar avbildning med `nb-NO` språkvarianten. |
| `2.0.0-amd64-nl-nl-preview` | Behållar avbildning med `nl-NL` språkvarianten. |
| `2.0.0-amd64-pl-pl-preview` | Behållar avbildning med `pl-PL` språkvarianten. |
| `2.0.0-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `2.0.0-amd64-pt-pt-preview` | Behållar avbildning med `pt-PT` språkvarianten. |
| `2.0.0-amd64-ru-ru-preview` | Behållar avbildning med `ru-RU` språkvarianten. |
| `2.0.0-amd64-sv-se-preview` | Behållar avbildning med `sv-SE` språkvarianten. |
| `2.0.0-amd64-th-th-preview` | Behållar avbildning med `th-TH` språkvarianten. |
| `2.0.0-amd64-tr-tr-preview` | Behållar avbildning med `tr-TR` språkvarianten. |
| `2.0.0-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `2.0.0-amd64-zh-hk-preview` | Behållar avbildning med `zh-HK` språkvarianten. |
| `2.0.0-amd64-zh-tw-preview` | Behållar avbildning med `zh-TW` språkvarianten. |
| `1.2.0-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `1.2.0-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `1.2.0-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `1.2.0-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `1.2.0-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `1.2.0-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `1.2.0-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `1.2.0-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `1.2.0-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `1.2.0-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `1.2.0-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `1.2.0-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `1.2.0-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `1.2.0-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `1.1.3-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `1.1.3-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `1.1.3-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `1.1.3-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `1.1.3-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `1.1.3-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `1.1.3-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `1.1.3-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `1.1.3-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `1.1.3-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `1.1.3-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `1.1.3-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `1.1.3-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `1.1.3-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `1.1.2-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `1.1.2-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `1.1.2-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `1.1.2-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `1.1.2-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `1.1.2-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `1.1.2-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `1.1.2-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `1.1.2-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `1.1.2-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `1.1.2-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `1.1.2-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `1.1.2-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `1.1.2-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `1.1.1-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `1.1.1-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `1.1.1-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `1.1.1-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `1.1.1-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `1.1.1-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `1.1.1-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `1.1.1-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `1.1.1-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `1.1.1-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `1.1.1-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `1.1.1-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `1.1.1-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `1.1.1-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `1.1.0-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `1.1.0-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `1.1.0-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `1.1.0-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `1.1.0-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `1.1.0-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `1.1.0-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `1.1.0-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `1.1.0-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `1.1.0-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `1.1.0-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `1.1.0-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `1.1.0-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `1.1.0-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |
| `1.0.0-amd64-de-de-preview` | Behållar avbildning med `de-DE` språkvarianten. |
| `1.0.0-amd64-en-au-preview` | Behållar avbildning med `en-AU` språkvarianten. |
| `1.0.0-amd64-en-ca-preview` | Behållar avbildning med `en-CA` språkvarianten. |
| `1.0.0-amd64-en-gb-preview` | Behållar avbildning med `en-GB` språkvarianten. |
| `1.0.0-amd64-en-in-preview` | Behållar avbildning med `en-IN` språkvarianten. |
| `1.0.0-amd64-en-us-preview` | Behållar avbildning med `en-US` språkvarianten. |
| `1.0.0-amd64-es-es-preview` | Behållar avbildning med `es-ES` språkvarianten. |
| `1.0.0-amd64-es-mx-preview` | Behållar avbildning med `es-MX` språkvarianten. |
| `1.0.0-amd64-fr-ca-preview` | Behållar avbildning med `fr-CA` språkvarianten. |
| `1.0.0-amd64-fr-fr-preview` | Behållar avbildning med `fr-FR` språkvarianten. |
| `1.0.0-amd64-it-it-preview` | Behållar avbildning med `it-IT` språkvarianten. |
| `1.0.0-amd64-ja-jp-preview` | Behållar avbildning med `ja-JP` språkvarianten. |
| `1.0.0-amd64-pt-br-preview` | Behållar avbildning med `pt-BR` språkvarianten. |
| `1.0.0-amd64-zh-cn-preview` | Behållar avbildning med `zh-CN` språkvarianten. |

## <a name="text-to-speech"></a>Text till tal

Du hittar [text till tal-][sp-tts] behållar avbildningen i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-text-to-speech` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                                  | Kommentarer                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning med `en-US` språket och `en-US-JessaRUS` rösten.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Behållar avbildning med `ar-EG` språket och `ar-EG-Hoda` rösten.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Behållar avbildning med `ar-SA` språket och `ar-SA-Naayf` rösten.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Behållar avbildning med `bg-BG` språket och `bg-BG-Ivan` rösten.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Behållar avbildning med `ca-ES` språket och `ca-ES-HerenaRUS` rösten.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Behållar avbildning med `cs-CZ` språket och `cs-CZ-Jakub` rösten.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Behållar avbildning med `da-DK` språket och `da-DK-HelleRUS` rösten.        |
| `1.3.0-amd64-de-at-michael-preview`         | Behållar avbildning med `de-AT` språket och `de-AT-Michael` rösten.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Behållar avbildning med `de-CH` språket och `de-CH-Karsten` rösten.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Behållar avbildning med `de-DE` språket och `de-DE-HeddaRUS` rösten.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Behållar avbildning med `el-GR` språket och `el-GR-Stefanos` rösten.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Behållar avbildning med `en-CA` språket och `en-CA-HeatherRUS` rösten.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Behållar avbildning med `en-CA` språket och `en-CA-Linda` rösten.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Behållar avbildning med `en-IE` språket och `en-IE-Sean` rösten.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning med `en-US` språket och `en-US-Jessa24kRUS` rösten.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Behållar avbildning med `en-US` språket och `en-US-JessaRUS` rösten.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Behållar avbildning med `fi-FI` språket och `fi-FI-HeidiRUS` rösten.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Behållar avbildning med `fr-CH` språket och `fr-CH-Guillaume` rösten.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Behållar avbildning med `he-IL` språket och `he-IL-Asaf` rösten.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Behållar avbildning med `hi-IN` språket och `hi-IN-Hemant` rösten.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana-Apollo` rösten.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Behållar avbildning med `hi-IN` språket och `hi-IN-Kalpana` rösten.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Behållar avbildning med `hr-HR` språket och `hr-HR-Matej` rösten.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Behållar avbildning med `hu-HU` språket och `hu-HU-Szabolcs` rösten.        |
| `1.3.0-amd64-id-id-andika-preview`          | Behållar avbildning med `id-ID` språket och `id-ID-Andika` rösten.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Behållar avbildning med `ms-MY` språket och `ms-MY-Rizwan` rösten.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Behållar avbildning med `nb-NO` språket och `nb-NO-HuldaRUS` rösten.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Behållar avbildning med `nl-NL` språket och `nl-NL-HannaRUS` rösten.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Behållar avbildning med `pl-PL` språket och `pl-PL-PaulinaRUS` rösten.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Behållar avbildning med `pt-PT` språket och `pt-PT-HeliaRUS` rösten.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Behållar avbildning med `ro-RO` språket och `ro-RO-Andrei` rösten.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Behållar avbildning med `ru-RU` språket och `ru-RU-EkaterinaRUS` rösten.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Behållar avbildning med `ru-RU` språket och `ru-RU-Irina-Apollo` rösten.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Behållar avbildning med `ru-RU` språket och `ru-RU-Pavel-Apollo` rösten.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Behållar avbildning med `sk-SK` språket och `sk-SK-Filip` rösten.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Behållar avbildning med `sl-SI` språket och `sl-SI-Lado` rösten.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Behållar avbildning med `sv-SE` språket och `sv-SE-HedvigRUS` rösten.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Behållar avbildning med `ta-IN` språket och `ta-IN-Valluvar` rösten.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Behållar avbildning med `te-IN` språket och `te-IN-Chitra` rösten.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Behållar avbildning med `th-TH` språket och `th-TH-Pattara` rösten.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Behållar avbildning med `tr-TR` språket och `tr-TR-SedaRUS` rösten.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Behållar avbildning med `vi-VN` språket och `vi-VN-An` rösten.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Behållar avbildning med `zh-HK` språket och `zh-HK-Danny-Apollo` rösten.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Behållar avbildning med `zh-HK` språket och `zh-HK-Tracy-Apollo` rösten.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Behållar avbildning med `zh-HK` språket och `zh-HK-TracyRUS` rösten.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Behållar avbildning med `zh-TW` språket och `zh-TW-HanHanRUS` rösten.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Behållar avbildning med `zh-TW` språket och `zh-TW-Yating-Apollo` rösten.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Behållar avbildning med `zh-TW` språket och `zh-TW-Zhiwei-Apollo` rösten.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Behållar avbildning med `de-DE` språket och `de-DE-HeddaRUS` rösten.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning med `en-US` språket och `en-US-Jessa24kRUS` rösten.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Behållar avbildning med `en-US` språket och `en-US-JessaRUS` rösten.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Behållar avbildning med `de-DE` språket och `de-DE-Hedda` rösten.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Behållar avbildning med `de-DE` språket och `de-DE-HeddaRUS` rösten.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Behållar avbildning med `de-DE` språket och `de-DE-Stefan-Apollo` rösten.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Behållar avbildning med `en-AU` språket och `en-AU-Catherine` rösten.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Behållar avbildning med `en-AU` språket och `en-AU-HayleyRUS` rösten.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Behållar avbildning med `en-GB` språket och `en-GB-George-Apollo` rösten.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Behållar avbildning med `en-GB` språket och `en-GB-HazelRUS` rösten.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Behållar avbildning med `en-GB` språket och `en-GB-Susan-Apollo` rösten.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Behållar avbildning med `en-IN` språket och `en-IN-Heera-Apollo` rösten.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Behållar avbildning med `en-IN` språket och `en-IN-PriyaRUS` rösten.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Behållar avbildning med `en-IN` språket och `en-IN-Ravi-Apollo` rösten.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning med `en-US` språket och `en-US-Jessa24kRUS` rösten.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Behållar avbildning med `en-US` språket och `en-US-JessaRUS` rösten.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Behållar avbildning med `es-ES` språket och `es-ES-HelenaRUS` rösten.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Behållar avbildning med `es-ES` språket och `es-ES-Laura-Apollo` rösten.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Behållar avbildning med `es-ES` språket och `es-ES-Pablo-Apollo` rösten.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Behållar avbildning med `es-MX` språket och `es-MX-HildaRUS` rösten.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Behållar avbildning med `es-MX` språket och `es-MX-Raul-Apollo` rösten.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Behållar avbildning med `fr-CA` språket och `fr-CA-Caroline` rösten.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Behållar avbildning med `fr-CA` språket och `fr-CA-HarmonieRUS` rösten.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Behållar avbildning med `fr-FR` språket och `fr-FR-HortenseRUS` rösten.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Behållar avbildning med `fr-FR` språket och `fr-FR-Julie-Apollo` rösten.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Behållar avbildning med `fr-FR` språket och `fr-FR-Paul-Apollo` rösten.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Behållar avbildning med `it-IT` språket och `it-IT-Cosimo-Apollo` rösten.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Behållar avbildning med `it-IT` språket och `it-IT-LuciaRUS` rösten.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållar avbildning med `ja-JP` språket och `ja-JP-Ayumi-Apollo` rösten.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Behållar avbildning med `ja-JP` språket och `ja-JP-HarukaRUS` rösten.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållar avbildning med `ja-JP` språket och `ja-JP-Ichiro-Apollo` rösten.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Behållar avbildning med `ko-KR` språket och `ko-KR-HeamiRUS` rösten.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Behållar avbildning med `pt-BR` språket och `pt-BR-Daniel-Apollo` rösten.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Behållar avbildning med `pt-BR` språket och `pt-BR-HeloisaRUS` rösten.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning med `en-US` språket och `en-US-BenjaminRUS` rösten.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning med `en-US` språket och `en-US-Guy24kRUS` rösten.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning med `en-US` språket och `en-US-Jessa24kRUS` rösten.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Behållar avbildning med `en-US` språket och `en-US-JessaRUS` rösten.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Behållar avbildning med `en-US` språket och `en-US-ZiraRUS` rösten.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning med `zh-CN` språket och `zh-CN-HuihuiRUS` rösten.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning med `zh-CN` språket och `zh-CN-Kangkang-Apollo` rösten. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning med `zh-CN` språket och `zh-CN-Yaoyao-Apollo` rösten.   |

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

[Extrahering av diskussionsämne][ta-kp] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `keyphrase` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/keyphrase` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Språkidentifiering

[Språkidentifiering][ta-la] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `language` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/language` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Kommentarer |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Attitydanalys

[Attitydanalys][ta-se] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `sentiment` . Det fullständigt kvalificerade namnet på behållar avbildningen är, `mcr.microsoft.com/azure-cognitive-services/sentiment` .

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar | Kommentarer                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Attitydanalys v3 (engelska)               |
| `3.0-es`   | Attitydanalys v3 (spanska)               |
| `3.0-fr`   | Attitydanalys v3 (franska)                |
| `3.0-it`   | Attitydanalys v3 (italiensk)               |
| `3.0-de`   | Attitydanalys v3 (tyska)                |
| `3.0-zh`   | Attitydanalys v3 (kinesiskt-förenklat)  |
| `3.0-zht`  | Attitydanalys v3 (kinesiska – traditionell) |
| `3.0-ja`   | Attitydanalys v3 (japanska)              |
| `3.0-pt`   | Attitydanalys v3 (portugisiska)            |
| `3.0-nl`   | Attitydanalys v3 (nederländska)                 |
| `1.1.009301-amd64-preview`    | Attitydanalys v2      |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
