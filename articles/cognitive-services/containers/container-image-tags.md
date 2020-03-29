---
title: Taggar för Cognitive Services-containeravbildningar
titleSuffix: Azure Cognitive Services
description: En omfattande lista över alla cognitive service-behållaravbildningstaggar.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219449"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Azure Cognitive Services-behållaravbildningstaggar

Azure Cognitive Services erbjuder många behållaravbildningar. Behållarregistren och motsvarande databaser varierar mellan behållaravbildningar. Varje behållaravbildningsnamn har flera taggar. En behållarbildtagg är en mekanism för versionshantering av behållaravbildningen. Den här artikeln är avsedd att användas som en omfattande referens för att lista alla Cognitive Services-behållaravbildningar och deras tillgängliga taggar.

> [!TIP]
> När [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)du använder , var uppmärksam på höljet i behållarregistret, databasen, behållaravbildningsnamnet och motsvarande tagg - eftersom de är **skiftlägeskänsliga**.

## <a name="anomaly-detector"></a>Avvikelseidentifiering

[Avbildningsdetektorns avbildning][ad-containers] finns `containerpreview.azurecr.io` i behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-anomaly-detector`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Visuellt innehåll

Behållaren [För datorseende][cv-containers] finns `containerpreview.azurecr.io` i behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-read`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Ansikte

Avbildningen [av ansiktsbehållaren][fa-containers] finns i `containerpreview.azurecr.io` behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-face`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
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

Behållaren [För att identifiera formulär][fr-containers] `containerpreview.azurecr.io` finns i behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-form-recognizer`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Språkförståelse (LUIS)

Luis-behållaravbildningen finns `mcr.microsoft.com` i behållarens registersyndikat. [LUIS][lu-containers] Den finns i `azure-cognitive-services` databasen och `luis`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `mcr.microsoft.com/azure-cognitive-services/luis`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
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

## <a name="custom-speech-to-text"></a>Anpassad tal-till-text

Den anpassade behållaren [för tal till][sp-cstt] text `containerpreview.azurecr.io` finns i behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-custom-speech-to-text`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar            | Anteckningar |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Anpassad text till tal

Den anpassade avbildningen av behållaren för `containerpreview.azurecr.io` anpassad text till [tal][sp-ctts] finns i behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-custom-text-to-speech`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar            | Anteckningar |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Tal till text

Behållaren [Tal-till-text-behållaren][sp-stt] finns `containerpreview.azurecr.io` i behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-speech-to-text`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                  | Anteckningar                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Behållaravbildning `en-US` med språk. |
| `2.1.1-amd64-ar-ae-preview` | Behållaravbildning `ar-AE` med språk. |
| `2.1.1-amd64-ar-eg-preview` | Behållaravbildning `ar-EG` med språk. |
| `2.1.1-amd64-ar-kw-preview` | Behållaravbildning `ar-KW` med språk. |
| `2.1.1-amd64-ar-qa-preview` | Behållaravbildning `ar-QA` med språk. |
| `2.1.1-amd64-ar-sa-preview` | Behållaravbildning `ar-SA` med språk. |
| `2.1.1-amd64-ca-es-preview` | Behållaravbildning `ca-ES` med språk. |
| `2.1.1-amd64-da-dk-preview` | Behållaravbildning `da-DK` med språk. |
| `2.1.1-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `2.1.1-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `2.1.1-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `2.1.1-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `2.1.1-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `2.1.1-amd64-en-nz-preview` | Behållaravbildning `en-NZ` med språk. |
| `2.1.1-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `2.1.1-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `2.1.1-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `2.1.1-amd64-fi-fi-preview` | Behållaravbildning `fi-FI` med språk. |
| `2.1.1-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `2.1.1-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `2.1.1-amd64-gu-in-preview` | Behållaravbildning `gu-IN` med språk. |
| `2.1.1-amd64-hi-in-preview` | Behållaravbildning `hi-IN` med språk. |
| `2.1.1-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `2.1.1-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `2.1.1-amd64-ko-kr-preview` | Behållaravbildning `ko-KR` med språk. |
| `2.1.1-amd64-mr-in-preview` | Behållaravbildning `mr-IN` med språk. |
| `2.1.1-amd64-nb-no-preview` | Behållaravbildning `nb-NO` med språk. |
| `2.1.1-amd64-nl-nl-preview` | Behållaravbildning `nl-NL` med språk. |
| `2.1.1-amd64-pl-pl-preview` | Behållaravbildning `pl-PL` med språk. |
| `2.1.1-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `2.1.1-amd64-pt-pt-preview` | Behållaravbildning `pt-PT` med språk. |
| `2.1.1-amd64-ru-ru-preview` | Behållaravbildning `ru-RU` med språk. |
| `2.1.1-amd64-sv-se-preview` | Behållaravbildning `sv-SE` med språk. |
| `2.1.1-amd64-ta-in-preview` | Behållaravbildning `ta-IN` med språk. |
| `2.1.1-amd64-te-in-preview` | Behållaravbildning `te-IN` med språk. |
| `2.1.1-amd64-th-th-preview` | Behållaravbildning `th-TH` med språk. |
| `2.1.1-amd64-tr-tr-preview` | Behållaravbildning `tr-TR` med språk. |
| `2.1.1-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `2.1.1-amd64-zh-hk-preview` | Behållaravbildning `zh-HK` med språk. |
| `2.1.1-amd64-zh-tw-preview` | Behållaravbildning `zh-TW` med språk. |
| `2.1.0-amd64-ar-ae-preview` | Behållaravbildning `ar-AE` med språk. |
| `2.1.0-amd64-ar-eg-preview` | Behållaravbildning `ar-EG` med språk. |
| `2.1.0-amd64-ar-kw-preview` | Behållaravbildning `ar-KW` med språk. |
| `2.1.0-amd64-ar-qa-preview` | Behållaravbildning `ar-QA` med språk. |
| `2.1.0-amd64-ar-sa-preview` | Behållaravbildning `ar-SA` med språk. |
| `2.1.0-amd64-ca-es-preview` | Behållaravbildning `ca-ES` med språk. |
| `2.1.0-amd64-da-dk-preview` | Behållaravbildning `da-DK` med språk. |
| `2.1.0-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `2.1.0-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `2.1.0-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `2.1.0-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `2.1.0-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `2.1.0-amd64-en-nz-preview` | Behållaravbildning `en-NZ` med språk. |
| `2.1.0-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `2.1.0-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `2.1.0-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `2.1.0-amd64-fi-fi-preview` | Behållaravbildning `fi-FI` med språk. |
| `2.1.0-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `2.1.0-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `2.1.0-amd64-gu-in-preview` | Behållaravbildning `gu-IN` med språk. |
| `2.1.0-amd64-hi-in-preview` | Behållaravbildning `hi-IN` med språk. |
| `2.1.0-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `2.1.0-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `2.1.0-amd64-ko-kr-preview` | Behållaravbildning `ko-KR` med språk. |
| `2.1.0-amd64-mr-in-preview` | Behållaravbildning `mr-IN` med språk. |
| `2.1.0-amd64-nb-no-preview` | Behållaravbildning `nb-NO` med språk. |
| `2.1.0-amd64-nl-nl-preview` | Behållaravbildning `nl-NL` med språk. |
| `2.1.0-amd64-pl-pl-preview` | Behållaravbildning `pl-PL` med språk. |
| `2.1.0-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `2.1.0-amd64-pt-pt-preview` | Behållaravbildning `pt-PT` med språk. |
| `2.1.0-amd64-ru-ru-preview` | Behållaravbildning `ru-RU` med språk. |
| `2.1.0-amd64-sv-se-preview` | Behållaravbildning `sv-SE` med språk. |
| `2.1.0-amd64-ta-in-preview` | Behållaravbildning `ta-IN` med språk. |
| `2.1.0-amd64-te-in-preview` | Behållaravbildning `te-IN` med språk. |
| `2.1.0-amd64-th-th-preview` | Behållaravbildning `th-TH` med språk. |
| `2.1.0-amd64-tr-tr-preview` | Behållaravbildning `tr-TR` med språk. |
| `2.1.0-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `2.1.0-amd64-zh-hk-preview` | Behållaravbildning `zh-HK` med språk. |
| `2.1.0-amd64-zh-tw-preview` | Behållaravbildning `zh-TW` med språk. |
| `2.0.3-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `2.0.2-amd64-ar-ae-preview` | Behållaravbildning `ar-AE` med språk. |
| `2.0.2-amd64-ar-eg-preview` | Behållaravbildning `ar-EG` med språk. |
| `2.0.2-amd64-ar-kw-preview` | Behållaravbildning `ar-KW` med språk. |
| `2.0.2-amd64-ar-qa-preview` | Behållaravbildning `ar-QA` med språk. |
| `2.0.2-amd64-ar-sa-preview` | Behållaravbildning `ar-SA` med språk. |
| `2.0.2-amd64-ca-es-preview` | Behållaravbildning `ca-ES` med språk. |
| `2.0.2-amd64-da-dk-preview` | Behållaravbildning `da-DK` med språk. |
| `2.0.2-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `2.0.2-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `2.0.2-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `2.0.2-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `2.0.2-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `2.0.2-amd64-en-nz-preview` | Behållaravbildning `en-NZ` med språk. |
| `2.0.2-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `2.0.2-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `2.0.2-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `2.0.2-amd64-fi-fi-preview` | Behållaravbildning `fi-FI` med språk. |
| `2.0.2-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `2.0.2-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `2.0.2-amd64-gu-in-preview` | Behållaravbildning `gu-IN` med språk. |
| `2.0.2-amd64-hi-in-preview` | Behållaravbildning `hi-IN` med språk. |
| `2.0.2-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `2.0.2-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `2.0.2-amd64-ko-kr-preview` | Behållaravbildning `ko-KR` med språk. |
| `2.0.2-amd64-mr-in-preview` | Behållaravbildning `mr-IN` med språk. |
| `2.0.2-amd64-nb-no-preview` | Behållaravbildning `nb-NO` med språk. |
| `2.0.2-amd64-nl-nl-preview` | Behållaravbildning `nl-NL` med språk. |
| `2.0.2-amd64-pl-pl-preview` | Behållaravbildning `pl-PL` med språk. |
| `2.0.2-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `2.0.2-amd64-pt-pt-preview` | Behållaravbildning `pt-PT` med språk. |
| `2.0.2-amd64-ru-ru-preview` | Behållaravbildning `ru-RU` med språk. |
| `2.0.2-amd64-sv-se-preview` | Behållaravbildning `sv-SE` med språk. |
| `2.0.2-amd64-ta-in-preview` | Behållaravbildning `ta-IN` med språk. |
| `2.0.2-amd64-te-in-preview` | Behållaravbildning `te-IN` med språk. |
| `2.0.2-amd64-th-th-preview` | Behållaravbildning `th-TH` med språk. |
| `2.0.2-amd64-tr-tr-preview` | Behållaravbildning `tr-TR` med språk. |
| `2.0.2-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `2.0.2-amd64-zh-hk-preview` | Behållaravbildning `zh-HK` med språk. |
| `2.0.2-amd64-zh-tw-preview` | Behållaravbildning `zh-TW` med språk. |
| `2.0.1-amd64-ar-ae-preview` | Behållaravbildning `ar-AE` med språk. |
| `2.0.1-amd64-ar-eg-preview` | Behållaravbildning `ar-EG` med språk. |
| `2.0.1-amd64-ar-kw-preview` | Behållaravbildning `ar-KW` med språk. |
| `2.0.1-amd64-ar-qa-preview` | Behållaravbildning `ar-QA` med språk. |
| `2.0.1-amd64-ar-sa-preview` | Behållaravbildning `ar-SA` med språk. |
| `2.0.1-amd64-ca-es-preview` | Behållaravbildning `ca-ES` med språk. |
| `2.0.1-amd64-da-dk-preview` | Behållaravbildning `da-DK` med språk. |
| `2.0.1-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `2.0.1-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `2.0.1-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `2.0.1-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `2.0.1-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `2.0.1-amd64-en-nz-preview` | Behållaravbildning `en-NZ` med språk. |
| `2.0.1-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `2.0.1-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `2.0.1-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `2.0.1-amd64-fi-fi-preview` | Behållaravbildning `fi-FI` med språk. |
| `2.0.1-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `2.0.1-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `2.0.1-amd64-gu-in-preview` | Behållaravbildning `gu-IN` med språk. |
| `2.0.1-amd64-hi-in-preview` | Behållaravbildning `hi-IN` med språk. |
| `2.0.1-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `2.0.1-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `2.0.1-amd64-ko-kr-preview` | Behållaravbildning `ko-KR` med språk. |
| `2.0.1-amd64-mr-in-preview` | Behållaravbildning `mr-IN` med språk. |
| `2.0.1-amd64-nb-no-preview` | Behållaravbildning `nb-NO` med språk. |
| `2.0.1-amd64-nl-nl-preview` | Behållaravbildning `nl-NL` med språk. |
| `2.0.1-amd64-pl-pl-preview` | Behållaravbildning `pl-PL` med språk. |
| `2.0.1-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `2.0.1-amd64-pt-pt-preview` | Behållaravbildning `pt-PT` med språk. |
| `2.0.1-amd64-ru-ru-preview` | Behållaravbildning `ru-RU` med språk. |
| `2.0.1-amd64-sv-se-preview` | Behållaravbildning `sv-SE` med språk. |
| `2.0.1-amd64-ta-in-preview` | Behållaravbildning `ta-IN` med språk. |
| `2.0.1-amd64-te-in-preview` | Behållaravbildning `te-IN` med språk. |
| `2.0.1-amd64-th-th-preview` | Behållaravbildning `th-TH` med språk. |
| `2.0.1-amd64-tr-tr-preview` | Behållaravbildning `tr-TR` med språk. |
| `2.0.1-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `2.0.1-amd64-zh-hk-preview` | Behållaravbildning `zh-HK` med språk. |
| `2.0.1-amd64-zh-tw-preview` | Behållaravbildning `zh-TW` med språk. |
| `2.0.0-amd64-ar-eg-preview` | Behållaravbildning `ar-EG` med språk. |
| `2.0.0-amd64-ca-es-preview` | Behållaravbildning `ca-ES` med språk. |
| `2.0.0-amd64-da-dk-preview` | Behållaravbildning `da-DK` med språk. |
| `2.0.0-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `2.0.0-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `2.0.0-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `2.0.0-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `2.0.0-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `2.0.0-amd64-en-nz-preview` | Behållaravbildning `en-NZ` med språk. |
| `2.0.0-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `2.0.0-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `2.0.0-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `2.0.0-amd64-fi-fi-preview` | Behållaravbildning `fi-FI` med språk. |
| `2.0.0-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `2.0.0-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `2.0.0-amd64-hi-in-preview` | Behållaravbildning `hi-IN` med språk. |
| `2.0.0-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `2.0.0-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `2.0.0-amd64-ko-kr-preview` | Behållaravbildning `ko-KR` med språk. |
| `2.0.0-amd64-nb-no-preview` | Behållaravbildning `nb-NO` med språk. |
| `2.0.0-amd64-nl-nl-preview` | Behållaravbildning `nl-NL` med språk. |
| `2.0.0-amd64-pl-pl-preview` | Behållaravbildning `pl-PL` med språk. |
| `2.0.0-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `2.0.0-amd64-pt-pt-preview` | Behållaravbildning `pt-PT` med språk. |
| `2.0.0-amd64-ru-ru-preview` | Behållaravbildning `ru-RU` med språk. |
| `2.0.0-amd64-sv-se-preview` | Behållaravbildning `sv-SE` med språk. |
| `2.0.0-amd64-th-th-preview` | Behållaravbildning `th-TH` med språk. |
| `2.0.0-amd64-tr-tr-preview` | Behållaravbildning `tr-TR` med språk. |
| `2.0.0-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `2.0.0-amd64-zh-hk-preview` | Behållaravbildning `zh-HK` med språk. |
| `2.0.0-amd64-zh-tw-preview` | Behållaravbildning `zh-TW` med språk. |
| `1.2.0-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `1.2.0-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `1.2.0-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `1.2.0-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `1.2.0-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `1.2.0-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `1.2.0-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `1.2.0-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `1.2.0-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `1.2.0-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `1.2.0-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `1.2.0-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `1.2.0-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `1.2.0-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `1.1.3-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `1.1.3-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `1.1.3-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `1.1.3-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `1.1.3-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `1.1.3-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `1.1.3-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `1.1.3-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `1.1.3-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `1.1.3-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `1.1.3-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `1.1.3-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `1.1.3-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `1.1.3-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `1.1.2-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `1.1.2-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `1.1.2-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `1.1.2-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `1.1.2-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `1.1.2-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `1.1.2-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `1.1.2-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `1.1.2-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `1.1.2-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `1.1.2-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `1.1.2-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `1.1.2-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `1.1.2-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `1.1.1-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `1.1.1-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `1.1.1-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `1.1.1-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `1.1.1-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `1.1.1-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `1.1.1-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `1.1.1-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `1.1.1-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `1.1.1-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `1.1.1-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `1.1.1-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `1.1.1-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `1.1.1-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `1.1.0-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `1.1.0-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `1.1.0-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `1.1.0-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `1.1.0-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `1.1.0-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `1.1.0-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `1.1.0-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `1.1.0-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `1.1.0-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `1.1.0-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `1.1.0-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `1.1.0-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `1.1.0-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |
| `1.0.0-amd64-de-de-preview` | Behållaravbildning `de-DE` med språk. |
| `1.0.0-amd64-en-au-preview` | Behållaravbildning `en-AU` med språk. |
| `1.0.0-amd64-en-ca-preview` | Behållaravbildning `en-CA` med språk. |
| `1.0.0-amd64-en-gb-preview` | Behållaravbildning `en-GB` med språk. |
| `1.0.0-amd64-en-in-preview` | Behållaravbildning `en-IN` med språk. |
| `1.0.0-amd64-en-us-preview` | Behållaravbildning `en-US` med språk. |
| `1.0.0-amd64-es-es-preview` | Behållaravbildning `es-ES` med språk. |
| `1.0.0-amd64-es-mx-preview` | Behållaravbildning `es-MX` med språk. |
| `1.0.0-amd64-fr-ca-preview` | Behållaravbildning `fr-CA` med språk. |
| `1.0.0-amd64-fr-fr-preview` | Behållaravbildning `fr-FR` med språk. |
| `1.0.0-amd64-it-it-preview` | Behållaravbildning `it-IT` med språk. |
| `1.0.0-amd64-ja-jp-preview` | Behållaravbildning `ja-JP` med språk. |
| `1.0.0-amd64-pt-br-preview` | Behållaravbildning `pt-BR` med språk. |
| `1.0.0-amd64-zh-cn-preview` | Behållaravbildning `zh-CN` med språk. |

## <a name="text-to-speech"></a>Text till tal

Avbildningen av behållaren [Text-till-tal][sp-tts] finns i `containerpreview.azurecr.io` behållarregistret. Den finns i `microsoft` databasen och `cognitive-services-text-to-speech`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                                  | Anteckningar                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållarbild `en-US` med språk `en-US-JessaRUS` och röst.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Behållarbild `ar-EG` med språk `ar-EG-Hoda` och röst.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Behållarbild `ar-SA` med språk `ar-SA-Naayf` och röst.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Behållarbild `bg-BG` med språk `bg-BG-Ivan` och röst.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Behållarbild `ca-ES` med språk `ca-ES-HerenaRUS` och röst.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Behållarbild `cs-CZ` med språk `cs-CZ-Jakub` och röst.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Behållarbild `da-DK` med språk `da-DK-HelleRUS` och röst.        |
| `1.3.0-amd64-de-at-michael-preview`         | Behållarbild `de-AT` med språk `de-AT-Michael` och röst.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Behållarbild `de-CH` med språk `de-CH-Karsten` och röst.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Behållarbild `de-DE` med språk `de-DE-Hedda` och röst.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Behållarbild `de-DE` med språk `de-DE-Hedda` och röst.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Behållarbild `de-DE` med språk `de-DE-HeddaRUS` och röst.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Behållarbild `de-DE` med språk `de-DE-Stefan-Apollo` och röst.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Behållarbild `el-GR` med språk `el-GR-Stefanos` och röst.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Behållarbild `en-AU` med språk `en-AU-Catherine` och röst.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Behållarbild `en-AU` med språk `en-AU-HayleyRUS` och röst.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Behållarbild `en-CA` med språk `en-CA-HeatherRUS` och röst.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Behållarbild `en-CA` med språk `en-CA-Linda` och röst.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Behållarbild `en-GB` med språk `en-GB-George-Apollo` och röst.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Behållarbild `en-GB` med språk `en-GB-HazelRUS` och röst.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Behållarbild `en-GB` med språk `en-GB-Susan-Apollo` och röst.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Behållarbild `en-IE` med språk `en-IE-Sean` och röst.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Behållarbild `en-IN` med språk `en-IN-Heera-Apollo` och röst.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Behållarbild `en-IN` med språk `en-IN-PriyaRUS` och röst.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Behållarbild `en-IN` med språk `en-IN-Ravi-Apollo` och röst.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Behållarbild `en-US` med språk `en-US-BenjaminRUS` och röst.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Behållarbild `en-US` med språk `en-US-Guy24kRUS` och röst.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Behållarbild `en-US` med språk `en-US-Jessa24kRUS` och röst.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Behållarbild `en-US` med språk `en-US-JessaRUS` och röst.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Behållarbild `en-US` med språk `en-US-ZiraRUS` och röst.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Behållarbild `es-ES` med språk `es-ES-HelenaRUS` och röst.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Behållarbild `es-ES` med språk `es-ES-Laura-Apollo` och röst.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Behållarbild `es-ES` med språk `es-ES-Pablo-Apollo` och röst.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Behållarbild `es-MX` med språk `es-MX-HildaRUS` och röst.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Behållarbild `es-MX` med språk `es-MX-Raul-Apollo` och röst.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Behållarbild `fi-FI` med språk `fi-FI-HeidiRUS` och röst.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Behållarbild `fr-CA` med språk `fr-CA-Caroline` och röst.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Behållarbild `fr-CA` med språk `fr-CA-HarmonieRUS` och röst.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Behållarbild `fr-CH` med språk `fr-CH-Guillaume` och röst.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Behållarbild `fr-FR` med språk `fr-FR-HortenseRUS` och röst.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Behållarbild `fr-FR` med språk `fr-FR-Julie-Apollo` och röst.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Behållarbild `fr-FR` med språk `fr-FR-Paul-Apollo` och röst.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Behållarbild `he-IL` med språk `he-IL-Asaf` och röst.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Behållarbild `hi-IN` med språk `hi-IN-Hemant` och röst.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Behållarbild `hi-IN` med språk `hi-IN-Kalpana-Apollo` och röst.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Behållarbild `hi-IN` med språk `hi-IN-Kalpana` och röst.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Behållarbild `hi-IN` med språk `hi-IN-Kalpana` och röst.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Behållarbild `hr-HR` med språk `hr-HR-Matej` och röst.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Behållarbild `hu-HU` med språk `hu-HU-Szabolcs` och röst.        |
| `1.3.0-amd64-id-id-andika-preview`          | Behållarbild `id-ID` med språk `id-ID-Andika` och röst.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Behållarbild `it-IT` med språk `it-IT-Cosimo-Apollo` och röst.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Behållarbild `it-IT` med språk `it-IT-LuciaRUS` och röst.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållarbild `ja-JP` med språk `ja-JP-Ayumi-Apollo` och röst.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Behållarbild `ja-JP` med språk `ja-JP-HarukaRUS` och röst.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållarbild `ja-JP` med språk `ja-JP-Ichiro-Apollo` och röst.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Behållarbild `ko-KR` med språk `ko-KR-HeamiRUS` och röst.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Behållarbild `ms-MY` med språk `ms-MY-Rizwan` och röst.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Behållarbild `nb-NO` med språk `nb-NO-HuldaRUS` och röst.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Behållarbild `nl-NL` med språk `nl-NL-HannaRUS` och röst.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Behållarbild `pl-PL` med språk `pl-PL-PaulinaRUS` och röst.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Behållarbild `pt-BR` med språk `pt-BR-Daniel-Apollo` och röst.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Behållarbild `pt-BR` med språk `pt-BR-HeloisaRUS` och röst.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Behållarbild `pt-PT` med språk `pt-PT-HeliaRUS` och röst.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Behållarbild `ro-RO` med språk `ro-RO-Andrei` och röst.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Behållarbild `ru-RU` med språk `ru-RU-EkaterinaRUS` och röst.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Behållarbild `ru-RU` med språk `ru-RU-Irina-Apollo` och röst.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Behållarbild `ru-RU` med språk `ru-RU-Pavel-Apollo` och röst.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Behållarbild `sk-SK` med språk `sk-SK-Filip` och röst.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Behållarbild `sl-SI` med språk `sl-SI-Lado` och röst.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Behållarbild `sv-SE` med språk `sv-SE-HedvigRUS` och röst.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Behållarbild `ta-IN` med språk `ta-IN-Valluvar` och röst.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Behållarbild `te-IN` med språk `te-IN-Chitra` och röst.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Behållarbild `th-TH` med språk `th-TH-Pattara` och röst.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Behållarbild `tr-TR` med språk `tr-TR-SedaRUS` och röst.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Behållarbild `vi-VN` med språk `vi-VN-An` och röst.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Behållarbild `zh-CN` med språk `zh-CN-HuihuiRUS` och röst.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Behållarbild `zh-CN` med språk `zh-CN-Kangkang-Apollo` och röst. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållarbild `zh-CN` med språk `zh-CN-Yaoyao-Apollo` och röst.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Behållarbild `zh-HK` med språk `zh-HK-Danny-Apollo` och röst.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Behållarbild `zh-HK` med språk `zh-HK-Tracy-Apollo` och röst.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Behållarbild `zh-HK` med språk `zh-HK-TracyRUS` och röst.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Behållarbild `zh-TW` med språk `zh-TW-HanHanRUS` och röst.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Behållarbild `zh-TW` med språk `zh-TW-Yating-Apollo` och röst.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Behållarbild `zh-TW` med språk `zh-TW-Zhiwei-Apollo` och röst.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Behållarbild `de-DE` med språk `de-DE-Hedda` och röst.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Behållarbild `de-DE` med språk `de-DE-HeddaRUS` och röst.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Behållarbild `de-DE` med språk `de-DE-Stefan-Apollo` och röst.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Behållarbild `en-AU` med språk `en-AU-Catherine` och röst.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Behållarbild `en-AU` med språk `en-AU-HayleyRUS` och röst.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Behållarbild `en-GB` med språk `en-GB-George-Apollo` och röst.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Behållarbild `en-GB` med språk `en-GB-HazelRUS` och röst.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Behållarbild `en-GB` med språk `en-GB-Susan-Apollo` och röst.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Behållarbild `en-IN` med språk `en-IN-Heera-Apollo` och röst.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Behållarbild `en-IN` med språk `en-IN-PriyaRUS` och röst.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Behållarbild `en-IN` med språk `en-IN-Ravi-Apollo` och röst.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Behållarbild `en-US` med språk `en-US-BenjaminRUS` och röst.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Behållarbild `en-US` med språk `en-US-Guy24kRUS` och röst.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Behållarbild `en-US` med språk `en-US-Jessa24kRUS` och röst.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Behållarbild `en-US` med språk `en-US-JessaRUS` och röst.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Behållarbild `en-US` med språk `en-US-ZiraRUS` och röst.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Behållarbild `es-ES` med språk `es-ES-HelenaRUS` och röst.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Behållarbild `es-ES` med språk `es-ES-Laura-Apollo` och röst.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Behållarbild `es-ES` med språk `es-ES-Pablo-Apollo` och röst.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Behållarbild `es-MX` med språk `es-MX-HildaRUS` och röst.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Behållarbild `es-MX` med språk `es-MX-Raul-Apollo` och röst.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Behållarbild `fr-CA` med språk `fr-CA-Caroline` och röst.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Behållarbild `fr-CA` med språk `fr-CA-HarmonieRUS` och röst.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Behållarbild `fr-FR` med språk `fr-FR-HortenseRUS` och röst.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Behållarbild `fr-FR` med språk `fr-FR-Julie-Apollo` och röst.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Behållarbild `fr-FR` med språk `fr-FR-Paul-Apollo` och röst.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Behållarbild `it-IT` med språk `it-IT-Cosimo-Apollo` och röst.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Behållarbild `it-IT` med språk `it-IT-LuciaRUS` och röst.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållarbild `ja-JP` med språk `ja-JP-Ayumi-Apollo` och röst.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Behållarbild `ja-JP` med språk `ja-JP-HarukaRUS` och röst.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållarbild `ja-JP` med språk `ja-JP-Ichiro-Apollo` och röst.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Behållarbild `ko-KR` med språk `ko-KR-HeamiRUS` och röst.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Behållarbild `pt-BR` med språk `pt-BR-Daniel-Apollo` och röst.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Behållarbild `pt-BR` med språk `pt-BR-HeloisaRUS` och röst.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Behållarbild `zh-CN` med språk `zh-CN-HuihuiRUS` och röst.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Behållarbild `zh-CN` med språk `zh-CN-Kangkang-Apollo` och röst. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållarbild `zh-CN` med språk `zh-CN-Yaoyao-Apollo` och röst.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Behållarbild `de-DE` med språk `de-DE-Hedda` och röst.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Behållarbild `de-DE` med språk `de-DE-Hedda` och röst.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Behållarbild `de-DE` med språk `de-DE-HeddaRUS` och röst.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Behållarbild `de-DE` med språk `de-DE-Stefan-Apollo` och röst.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Behållarbild `en-AU` med språk `en-AU-Catherine` och röst.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Behållarbild `en-AU` med språk `en-AU-HayleyRUS` och röst.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Behållarbild `en-GB` med språk `en-GB-George-Apollo` och röst.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Behållarbild `en-GB` med språk `en-GB-HazelRUS` och röst.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Behållarbild `en-GB` med språk `en-GB-Susan-Apollo` och röst.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Behållarbild `en-IN` med språk `en-IN-Heera-Apollo` och röst.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Behållarbild `en-IN` med språk `en-IN-PriyaRUS` och röst.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Behållarbild `en-IN` med språk `en-IN-Ravi-Apollo` och röst.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Behållarbild `en-US` med språk `en-US-BenjaminRUS` och röst.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Behållarbild `en-US` med språk `en-US-Guy24kRUS` och röst.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Behållarbild `en-US` med språk `en-US-Jessa24kRUS` och röst.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Behållarbild `en-US` med språk `en-US-JessaRUS` och röst.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Behållarbild `en-US` med språk `en-US-ZiraRUS` och röst.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Behållarbild `es-ES` med språk `es-ES-HelenaRUS` och röst.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Behållarbild `es-ES` med språk `es-ES-Laura-Apollo` och röst.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Behållarbild `es-ES` med språk `es-ES-Pablo-Apollo` och röst.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Behållarbild `es-MX` med språk `es-MX-HildaRUS` och röst.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Behållarbild `es-MX` med språk `es-MX-Raul-Apollo` och röst.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Behållarbild `fr-CA` med språk `fr-CA-Caroline` och röst.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Behållarbild `fr-CA` med språk `fr-CA-HarmonieRUS` och röst.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Behållarbild `fr-FR` med språk `fr-FR-HortenseRUS` och röst.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Behållarbild `fr-FR` med språk `fr-FR-Julie-Apollo` och röst.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Behållarbild `fr-FR` med språk `fr-FR-Paul-Apollo` och röst.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Behållarbild `it-IT` med språk `it-IT-Cosimo-Apollo` och röst.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Behållarbild `it-IT` med språk `it-IT-LuciaRUS` och röst.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållarbild `ja-JP` med språk `ja-JP-Ayumi-Apollo` och röst.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Behållarbild `ja-JP` med språk `ja-JP-HarukaRUS` och röst.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållarbild `ja-JP` med språk `ja-JP-Ichiro-Apollo` och röst.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Behållarbild `ko-KR` med språk `ko-KR-HeamiRUS` och röst.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Behållarbild `pt-BR` med språk `pt-BR-Daniel-Apollo` och röst.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Behållarbild `pt-BR` med språk `pt-BR-HeloisaRUS` och röst.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Behållarbild `zh-CN` med språk `zh-CN-HuihuiRUS` och röst.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Behållarbild `zh-CN` med språk `zh-CN-Kangkang-Apollo` och röst. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållarbild `zh-CN` med språk `zh-CN-Yaoyao-Apollo` och röst.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Behållarbild `en-US` med språk `en-US-BenjaminRUS` och röst.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Behållarbild `en-US` med språk `en-US-Guy24kRUS` och röst.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Behållarbild `en-US` med språk `en-US-Jessa24kRUS` och röst.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Behållarbild `en-US` med språk `en-US-JessaRUS` och röst.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Behållarbild `en-US` med språk `en-US-ZiraRUS` och röst.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Behållarbild `zh-CN` med språk `zh-CN-HuihuiRUS` och röst.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Behållarbild `zh-CN` med språk `zh-CN-Kangkang-Apollo` och röst. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållarbild `zh-CN` med språk `zh-CN-Yaoyao-Apollo` och röst.   |

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

Behållaren [För extrahering av nyckelfraser][ta-kp] finns i `mcr.microsoft.com` behållarens registersyndikat. Den finns i `azure-cognitive-services` databasen och `keyphrase`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Språkidentifiering

Behållaren [för språkidentifiering][ta-la] `mcr.microsoft.com` finns i behållarens registersyndikat. Den finns i `azure-cognitive-services` databasen och `language`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `mcr.microsoft.com/azure-cognitive-services/language`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Attitydanalys

Avbildningen [av sentimentanalysen][ta-se] `mcr.microsoft.com` finns i behållarens registersyndikat. Den finns i `azure-cognitive-services` databasen och `sentiment`heter . Det fullständigt kvalificerade behållaravbildningsnamnet är `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Den här behållaravbildningen har följande taggar tillgängliga:

| Bildtaggar                    | Anteckningar |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
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
