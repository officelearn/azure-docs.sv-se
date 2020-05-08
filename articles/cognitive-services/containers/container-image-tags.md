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
ms.openlocfilehash: 850d7b9732ad547d502b042e7eaad5d3b471030e
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926008"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Avbildnings taggar för Azure Cognitive Services container

Azure Cognitive Services erbjuder många behållar avbildningar. Behållar registren och motsvarande databaser varierar mellan behållar avbildningar. Varje behållar avbildnings namn innehåller flera taggar. En behållar avbildnings tag är en mekanism för version av behållar avbildningen. Den här artikeln är avsedd att användas som en omfattande referens för att visa alla Cognitive Services behållar avbildningar och de tillgängliga taggarna.

> [!TIP]
> När du [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)använder ska du vara noga med att tänka på SKIFT läge för behållar registret, databasen, behållar avbildningens namn och motsvarande tagg – eftersom de är **SKIFT läges känsliga**.

## <a name="anomaly-detector"></a>Avvikelseidentifiering

Du hittar behållar avbildningen för [avvikelse detektor][ad-containers] i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-anomaly-detector`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Visuellt innehåll

[Visuellt innehåll][cv-containers] behållar avbildningen finns i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-read`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-read`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Ansikte

[Ansikts][fa-containers] behållar avbildningen finns i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-face`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-face`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
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

[Formulär tolkens][fr-containers] behållar avbildning finns i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-form-recognizer`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

[Luis][lu-containers] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `luis`. Det fullständigt kvalificerade namnet på behållar `mcr.microsoft.com/azure-cognitive-services/luis`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
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

Behållar avbildningen [Custom Speech till text][sp-cstt] finns i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-custom-speech-to-text`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar            | Obs! |
|-----------------------|:------|
| `latest`              |       |
| `2.2.0-amd64-preview` |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Anpassad text till tal

Du hittar den [anpassade text till tal-][sp-ctts] behållar avbildningen i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-custom-text-to-speech`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar            | Obs! |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Tal till text

Du hittar en behållar avbildning från [tal till text][sp-stt] i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-speech-to-text`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                  | Obs!                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Behållar avbildning `en-US` med språkvarianten. |
| `2.2.0-amd64-ar-ae-preview` | Behållar avbildning `ar-AE` med språkvarianten. |
| `2.2.0-amd64-ar-eg-preview` | Behållar avbildning `ar-EG` med språkvarianten. |
| `2.2.0-amd64-ar-kw-preview` | Behållar avbildning `ar-KW` med språkvarianten. |
| `2.2.0-amd64-ar-qa-preview` | Behållar avbildning `ar-QA` med språkvarianten. |
| `2.2.0-amd64-ar-sa-preview` | Behållar avbildning `ar-SA` med språkvarianten. |
| `2.2.0-amd64-ca-es-preview` | Behållar avbildning `ca-ES` med språkvarianten. |
| `2.2.0-amd64-da-dk-preview` | Behållar avbildning `da-DK` med språkvarianten. |
| `2.2.0-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `2.2.0-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `2.2.0-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `2.2.0-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `2.2.0-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `2.2.0-amd64-en-nz-preview` | Behållar avbildning `en-NZ` med språkvarianten. |
| `2.2.0-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `2.2.0-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `2.2.0-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `2.2.0-amd64-fi-fi-preview` | Behållar avbildning `fi-FI` med språkvarianten. |
| `2.2.0-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `2.2.0-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `2.2.0-amd64-gu-in-preview` | Behållar avbildning `gu-IN` med språkvarianten. |
| `2.2.0-amd64-hi-in-preview` | Behållar avbildning `hi-IN` med språkvarianten. |
| `2.2.0-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `2.2.0-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `2.2.0-amd64-ko-kr-preview` | Behållar avbildning `ko-KR` med språkvarianten. |
| `2.2.0-amd64-mr-in-preview` | Behållar avbildning `mr-IN` med språkvarianten. |
| `2.2.0-amd64-nb-no-preview` | Behållar avbildning `nb-NO` med språkvarianten. |
| `2.2.0-amd64-nl-nl-preview` | Behållar avbildning `nl-NL` med språkvarianten. |
| `2.2.0-amd64-pl-pl-preview` | Behållar avbildning `pl-PL` med språkvarianten. |
| `2.2.0-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `2.2.0-amd64-pt-pt-preview` | Behållar avbildning `pt-PT` med språkvarianten. |
| `2.2.0-amd64-ru-ru-preview` | Behållar avbildning `ru-RU` med språkvarianten. |
| `2.2.0-amd64-sv-se-preview` | Behållar avbildning `sv-SE` med språkvarianten. |
| `2.2.0-amd64-ta-in-preview` | Behållar avbildning `ta-IN` med språkvarianten. |
| `2.2.0-amd64-te-in-preview` | Behållar avbildning `te-IN` med språkvarianten. |
| `2.2.0-amd64-th-th-preview` | Behållar avbildning `th-TH` med språkvarianten. |
| `2.2.0-amd64-tr-tr-preview` | Behållar avbildning `tr-TR` med språkvarianten. |
| `2.2.0-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `2.2.0-amd64-zh-hk-preview` | Behållar avbildning `zh-HK` med språkvarianten. |
| `2.2.0-amd64-zh-tw-preview` | Behållar avbildning `zh-TW` med språkvarianten. |
| `2.1.1-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `2.1.1-amd64-ar-ae-preview` | Behållar avbildning `ar-AE` med språkvarianten. |
| `2.1.1-amd64-ar-eg-preview` | Behållar avbildning `ar-EG` med språkvarianten. |
| `2.1.1-amd64-ar-kw-preview` | Behållar avbildning `ar-KW` med språkvarianten. |
| `2.1.1-amd64-ar-qa-preview` | Behållar avbildning `ar-QA` med språkvarianten. |
| `2.1.1-amd64-ar-sa-preview` | Behållar avbildning `ar-SA` med språkvarianten. |
| `2.1.1-amd64-ca-es-preview` | Behållar avbildning `ca-ES` med språkvarianten. |
| `2.1.1-amd64-da-dk-preview` | Behållar avbildning `da-DK` med språkvarianten. |
| `2.1.1-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `2.1.1-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `2.1.1-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `2.1.1-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `2.1.1-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `2.1.1-amd64-en-nz-preview` | Behållar avbildning `en-NZ` med språkvarianten. |
| `2.1.1-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `2.1.1-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `2.1.1-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `2.1.1-amd64-fi-fi-preview` | Behållar avbildning `fi-FI` med språkvarianten. |
| `2.1.1-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `2.1.1-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `2.1.1-amd64-gu-in-preview` | Behållar avbildning `gu-IN` med språkvarianten. |
| `2.1.1-amd64-hi-in-preview` | Behållar avbildning `hi-IN` med språkvarianten. |
| `2.1.1-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `2.1.1-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `2.1.1-amd64-ko-kr-preview` | Behållar avbildning `ko-KR` med språkvarianten. |
| `2.1.1-amd64-mr-in-preview` | Behållar avbildning `mr-IN` med språkvarianten. |
| `2.1.1-amd64-nb-no-preview` | Behållar avbildning `nb-NO` med språkvarianten. |
| `2.1.1-amd64-nl-nl-preview` | Behållar avbildning `nl-NL` med språkvarianten. |
| `2.1.1-amd64-pl-pl-preview` | Behållar avbildning `pl-PL` med språkvarianten. |
| `2.1.1-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `2.1.1-amd64-pt-pt-preview` | Behållar avbildning `pt-PT` med språkvarianten. |
| `2.1.1-amd64-ru-ru-preview` | Behållar avbildning `ru-RU` med språkvarianten. |
| `2.1.1-amd64-sv-se-preview` | Behållar avbildning `sv-SE` med språkvarianten. |
| `2.1.1-amd64-ta-in-preview` | Behållar avbildning `ta-IN` med språkvarianten. |
| `2.1.1-amd64-te-in-preview` | Behållar avbildning `te-IN` med språkvarianten. |
| `2.1.1-amd64-th-th-preview` | Behållar avbildning `th-TH` med språkvarianten. |
| `2.1.1-amd64-tr-tr-preview` | Behållar avbildning `tr-TR` med språkvarianten. |
| `2.1.1-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `2.1.1-amd64-zh-hk-preview` | Behållar avbildning `zh-HK` med språkvarianten. |
| `2.1.1-amd64-zh-tw-preview` | Behållar avbildning `zh-TW` med språkvarianten. |
| `2.1.0-amd64-ar-ae-preview` | Behållar avbildning `ar-AE` med språkvarianten. |
| `2.1.0-amd64-ar-eg-preview` | Behållar avbildning `ar-EG` med språkvarianten. |
| `2.1.0-amd64-ar-kw-preview` | Behållar avbildning `ar-KW` med språkvarianten. |
| `2.1.0-amd64-ar-qa-preview` | Behållar avbildning `ar-QA` med språkvarianten. |
| `2.1.0-amd64-ar-sa-preview` | Behållar avbildning `ar-SA` med språkvarianten. |
| `2.1.0-amd64-ca-es-preview` | Behållar avbildning `ca-ES` med språkvarianten. |
| `2.1.0-amd64-da-dk-preview` | Behållar avbildning `da-DK` med språkvarianten. |
| `2.1.0-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `2.1.0-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `2.1.0-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `2.1.0-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `2.1.0-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `2.1.0-amd64-en-nz-preview` | Behållar avbildning `en-NZ` med språkvarianten. |
| `2.1.0-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `2.1.0-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `2.1.0-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `2.1.0-amd64-fi-fi-preview` | Behållar avbildning `fi-FI` med språkvarianten. |
| `2.1.0-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `2.1.0-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `2.1.0-amd64-gu-in-preview` | Behållar avbildning `gu-IN` med språkvarianten. |
| `2.1.0-amd64-hi-in-preview` | Behållar avbildning `hi-IN` med språkvarianten. |
| `2.1.0-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `2.1.0-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `2.1.0-amd64-ko-kr-preview` | Behållar avbildning `ko-KR` med språkvarianten. |
| `2.1.0-amd64-mr-in-preview` | Behållar avbildning `mr-IN` med språkvarianten. |
| `2.1.0-amd64-nb-no-preview` | Behållar avbildning `nb-NO` med språkvarianten. |
| `2.1.0-amd64-nl-nl-preview` | Behållar avbildning `nl-NL` med språkvarianten. |
| `2.1.0-amd64-pl-pl-preview` | Behållar avbildning `pl-PL` med språkvarianten. |
| `2.1.0-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `2.1.0-amd64-pt-pt-preview` | Behållar avbildning `pt-PT` med språkvarianten. |
| `2.1.0-amd64-ru-ru-preview` | Behållar avbildning `ru-RU` med språkvarianten. |
| `2.1.0-amd64-sv-se-preview` | Behållar avbildning `sv-SE` med språkvarianten. |
| `2.1.0-amd64-ta-in-preview` | Behållar avbildning `ta-IN` med språkvarianten. |
| `2.1.0-amd64-te-in-preview` | Behållar avbildning `te-IN` med språkvarianten. |
| `2.1.0-amd64-th-th-preview` | Behållar avbildning `th-TH` med språkvarianten. |
| `2.1.0-amd64-tr-tr-preview` | Behållar avbildning `tr-TR` med språkvarianten. |
| `2.1.0-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `2.1.0-amd64-zh-hk-preview` | Behållar avbildning `zh-HK` med språkvarianten. |
| `2.1.0-amd64-zh-tw-preview` | Behållar avbildning `zh-TW` med språkvarianten. |
| `2.0.3-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `2.0.2-amd64-ar-ae-preview` | Behållar avbildning `ar-AE` med språkvarianten. |
| `2.0.2-amd64-ar-eg-preview` | Behållar avbildning `ar-EG` med språkvarianten. |
| `2.0.2-amd64-ar-kw-preview` | Behållar avbildning `ar-KW` med språkvarianten. |
| `2.0.2-amd64-ar-qa-preview` | Behållar avbildning `ar-QA` med språkvarianten. |
| `2.0.2-amd64-ar-sa-preview` | Behållar avbildning `ar-SA` med språkvarianten. |
| `2.0.2-amd64-ca-es-preview` | Behållar avbildning `ca-ES` med språkvarianten. |
| `2.0.2-amd64-da-dk-preview` | Behållar avbildning `da-DK` med språkvarianten. |
| `2.0.2-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `2.0.2-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `2.0.2-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `2.0.2-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `2.0.2-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `2.0.2-amd64-en-nz-preview` | Behållar avbildning `en-NZ` med språkvarianten. |
| `2.0.2-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `2.0.2-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `2.0.2-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `2.0.2-amd64-fi-fi-preview` | Behållar avbildning `fi-FI` med språkvarianten. |
| `2.0.2-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `2.0.2-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `2.0.2-amd64-gu-in-preview` | Behållar avbildning `gu-IN` med språkvarianten. |
| `2.0.2-amd64-hi-in-preview` | Behållar avbildning `hi-IN` med språkvarianten. |
| `2.0.2-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `2.0.2-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `2.0.2-amd64-ko-kr-preview` | Behållar avbildning `ko-KR` med språkvarianten. |
| `2.0.2-amd64-mr-in-preview` | Behållar avbildning `mr-IN` med språkvarianten. |
| `2.0.2-amd64-nb-no-preview` | Behållar avbildning `nb-NO` med språkvarianten. |
| `2.0.2-amd64-nl-nl-preview` | Behållar avbildning `nl-NL` med språkvarianten. |
| `2.0.2-amd64-pl-pl-preview` | Behållar avbildning `pl-PL` med språkvarianten. |
| `2.0.2-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `2.0.2-amd64-pt-pt-preview` | Behållar avbildning `pt-PT` med språkvarianten. |
| `2.0.2-amd64-ru-ru-preview` | Behållar avbildning `ru-RU` med språkvarianten. |
| `2.0.2-amd64-sv-se-preview` | Behållar avbildning `sv-SE` med språkvarianten. |
| `2.0.2-amd64-ta-in-preview` | Behållar avbildning `ta-IN` med språkvarianten. |
| `2.0.2-amd64-te-in-preview` | Behållar avbildning `te-IN` med språkvarianten. |
| `2.0.2-amd64-th-th-preview` | Behållar avbildning `th-TH` med språkvarianten. |
| `2.0.2-amd64-tr-tr-preview` | Behållar avbildning `tr-TR` med språkvarianten. |
| `2.0.2-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `2.0.2-amd64-zh-hk-preview` | Behållar avbildning `zh-HK` med språkvarianten. |
| `2.0.2-amd64-zh-tw-preview` | Behållar avbildning `zh-TW` med språkvarianten. |
| `2.0.1-amd64-ar-ae-preview` | Behållar avbildning `ar-AE` med språkvarianten. |
| `2.0.1-amd64-ar-eg-preview` | Behållar avbildning `ar-EG` med språkvarianten. |
| `2.0.1-amd64-ar-kw-preview` | Behållar avbildning `ar-KW` med språkvarianten. |
| `2.0.1-amd64-ar-qa-preview` | Behållar avbildning `ar-QA` med språkvarianten. |
| `2.0.1-amd64-ar-sa-preview` | Behållar avbildning `ar-SA` med språkvarianten. |
| `2.0.1-amd64-ca-es-preview` | Behållar avbildning `ca-ES` med språkvarianten. |
| `2.0.1-amd64-da-dk-preview` | Behållar avbildning `da-DK` med språkvarianten. |
| `2.0.1-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `2.0.1-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `2.0.1-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `2.0.1-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `2.0.1-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `2.0.1-amd64-en-nz-preview` | Behållar avbildning `en-NZ` med språkvarianten. |
| `2.0.1-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `2.0.1-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `2.0.1-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `2.0.1-amd64-fi-fi-preview` | Behållar avbildning `fi-FI` med språkvarianten. |
| `2.0.1-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `2.0.1-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `2.0.1-amd64-gu-in-preview` | Behållar avbildning `gu-IN` med språkvarianten. |
| `2.0.1-amd64-hi-in-preview` | Behållar avbildning `hi-IN` med språkvarianten. |
| `2.0.1-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `2.0.1-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `2.0.1-amd64-ko-kr-preview` | Behållar avbildning `ko-KR` med språkvarianten. |
| `2.0.1-amd64-mr-in-preview` | Behållar avbildning `mr-IN` med språkvarianten. |
| `2.0.1-amd64-nb-no-preview` | Behållar avbildning `nb-NO` med språkvarianten. |
| `2.0.1-amd64-nl-nl-preview` | Behållar avbildning `nl-NL` med språkvarianten. |
| `2.0.1-amd64-pl-pl-preview` | Behållar avbildning `pl-PL` med språkvarianten. |
| `2.0.1-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `2.0.1-amd64-pt-pt-preview` | Behållar avbildning `pt-PT` med språkvarianten. |
| `2.0.1-amd64-ru-ru-preview` | Behållar avbildning `ru-RU` med språkvarianten. |
| `2.0.1-amd64-sv-se-preview` | Behållar avbildning `sv-SE` med språkvarianten. |
| `2.0.1-amd64-ta-in-preview` | Behållar avbildning `ta-IN` med språkvarianten. |
| `2.0.1-amd64-te-in-preview` | Behållar avbildning `te-IN` med språkvarianten. |
| `2.0.1-amd64-th-th-preview` | Behållar avbildning `th-TH` med språkvarianten. |
| `2.0.1-amd64-tr-tr-preview` | Behållar avbildning `tr-TR` med språkvarianten. |
| `2.0.1-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `2.0.1-amd64-zh-hk-preview` | Behållar avbildning `zh-HK` med språkvarianten. |
| `2.0.1-amd64-zh-tw-preview` | Behållar avbildning `zh-TW` med språkvarianten. |
| `2.0.0-amd64-ar-eg-preview` | Behållar avbildning `ar-EG` med språkvarianten. |
| `2.0.0-amd64-ca-es-preview` | Behållar avbildning `ca-ES` med språkvarianten. |
| `2.0.0-amd64-da-dk-preview` | Behållar avbildning `da-DK` med språkvarianten. |
| `2.0.0-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `2.0.0-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `2.0.0-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `2.0.0-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `2.0.0-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `2.0.0-amd64-en-nz-preview` | Behållar avbildning `en-NZ` med språkvarianten. |
| `2.0.0-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `2.0.0-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `2.0.0-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `2.0.0-amd64-fi-fi-preview` | Behållar avbildning `fi-FI` med språkvarianten. |
| `2.0.0-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `2.0.0-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `2.0.0-amd64-hi-in-preview` | Behållar avbildning `hi-IN` med språkvarianten. |
| `2.0.0-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `2.0.0-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `2.0.0-amd64-ko-kr-preview` | Behållar avbildning `ko-KR` med språkvarianten. |
| `2.0.0-amd64-nb-no-preview` | Behållar avbildning `nb-NO` med språkvarianten. |
| `2.0.0-amd64-nl-nl-preview` | Behållar avbildning `nl-NL` med språkvarianten. |
| `2.0.0-amd64-pl-pl-preview` | Behållar avbildning `pl-PL` med språkvarianten. |
| `2.0.0-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `2.0.0-amd64-pt-pt-preview` | Behållar avbildning `pt-PT` med språkvarianten. |
| `2.0.0-amd64-ru-ru-preview` | Behållar avbildning `ru-RU` med språkvarianten. |
| `2.0.0-amd64-sv-se-preview` | Behållar avbildning `sv-SE` med språkvarianten. |
| `2.0.0-amd64-th-th-preview` | Behållar avbildning `th-TH` med språkvarianten. |
| `2.0.0-amd64-tr-tr-preview` | Behållar avbildning `tr-TR` med språkvarianten. |
| `2.0.0-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `2.0.0-amd64-zh-hk-preview` | Behållar avbildning `zh-HK` med språkvarianten. |
| `2.0.0-amd64-zh-tw-preview` | Behållar avbildning `zh-TW` med språkvarianten. |
| `1.2.0-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `1.2.0-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `1.2.0-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `1.2.0-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `1.2.0-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `1.2.0-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `1.2.0-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `1.2.0-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `1.2.0-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `1.2.0-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `1.2.0-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `1.2.0-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `1.2.0-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `1.2.0-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `1.1.3-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `1.1.3-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `1.1.3-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `1.1.3-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `1.1.3-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `1.1.3-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `1.1.3-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `1.1.3-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `1.1.3-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `1.1.3-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `1.1.3-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `1.1.3-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `1.1.3-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `1.1.3-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `1.1.2-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `1.1.2-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `1.1.2-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `1.1.2-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `1.1.2-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `1.1.2-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `1.1.2-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `1.1.2-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `1.1.2-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `1.1.2-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `1.1.2-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `1.1.2-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `1.1.2-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `1.1.2-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `1.1.1-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `1.1.1-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `1.1.1-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `1.1.1-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `1.1.1-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `1.1.1-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `1.1.1-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `1.1.1-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `1.1.1-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `1.1.1-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `1.1.1-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `1.1.1-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `1.1.1-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `1.1.1-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `1.1.0-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `1.1.0-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `1.1.0-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `1.1.0-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `1.1.0-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `1.1.0-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `1.1.0-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `1.1.0-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `1.1.0-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `1.1.0-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `1.1.0-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `1.1.0-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `1.1.0-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `1.1.0-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |
| `1.0.0-amd64-de-de-preview` | Behållar avbildning `de-DE` med språkvarianten. |
| `1.0.0-amd64-en-au-preview` | Behållar avbildning `en-AU` med språkvarianten. |
| `1.0.0-amd64-en-ca-preview` | Behållar avbildning `en-CA` med språkvarianten. |
| `1.0.0-amd64-en-gb-preview` | Behållar avbildning `en-GB` med språkvarianten. |
| `1.0.0-amd64-en-in-preview` | Behållar avbildning `en-IN` med språkvarianten. |
| `1.0.0-amd64-en-us-preview` | Behållar avbildning `en-US` med språkvarianten. |
| `1.0.0-amd64-es-es-preview` | Behållar avbildning `es-ES` med språkvarianten. |
| `1.0.0-amd64-es-mx-preview` | Behållar avbildning `es-MX` med språkvarianten. |
| `1.0.0-amd64-fr-ca-preview` | Behållar avbildning `fr-CA` med språkvarianten. |
| `1.0.0-amd64-fr-fr-preview` | Behållar avbildning `fr-FR` med språkvarianten. |
| `1.0.0-amd64-it-it-preview` | Behållar avbildning `it-IT` med språkvarianten. |
| `1.0.0-amd64-ja-jp-preview` | Behållar avbildning `ja-JP` med språkvarianten. |
| `1.0.0-amd64-pt-br-preview` | Behållar avbildning `pt-BR` med språkvarianten. |
| `1.0.0-amd64-zh-cn-preview` | Behållar avbildning `zh-CN` med språkvarianten. |

## <a name="text-to-speech"></a>Text till tal

Du hittar [text till tal-][sp-tts] behållar avbildningen i `containerpreview.azurecr.io` behållar registret. Den finns i `microsoft` lagrings platsen och får namnet `cognitive-services-text-to-speech`. Det fullständigt kvalificerade namnet på behållar `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                                  | Obs!                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Behållar avbildning `en-US` med språket `en-US-JessaRUS` och rösten.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Behållar avbildning `ar-EG` med språket `ar-EG-Hoda` och rösten.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Behållar avbildning `ar-SA` med språket `ar-SA-Naayf` och rösten.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Behållar avbildning `bg-BG` med språket `bg-BG-Ivan` och rösten.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Behållar avbildning `ca-ES` med språket `ca-ES-HerenaRUS` och rösten.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Behållar avbildning `cs-CZ` med språket `cs-CZ-Jakub` och rösten.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Behållar avbildning `da-DK` med språket `da-DK-HelleRUS` och rösten.        |
| `1.3.0-amd64-de-at-michael-preview`         | Behållar avbildning `de-AT` med språket `de-AT-Michael` och rösten.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Behållar avbildning `de-CH` med språket `de-CH-Karsten` och rösten.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Behållar avbildning `de-DE` med språket `de-DE-Hedda` och rösten.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Behållar avbildning `de-DE` med språket `de-DE-Hedda` och rösten.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Behållar avbildning `de-DE` med språket `de-DE-HeddaRUS` och rösten.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Behållar avbildning `de-DE` med språket `de-DE-Stefan-Apollo` och rösten.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Behållar avbildning `el-GR` med språket `el-GR-Stefanos` och rösten.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Behållar avbildning `en-AU` med språket `en-AU-Catherine` och rösten.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Behållar avbildning `en-AU` med språket `en-AU-HayleyRUS` och rösten.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Behållar avbildning `en-CA` med språket `en-CA-HeatherRUS` och rösten.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Behållar avbildning `en-CA` med språket `en-CA-Linda` och rösten.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Behållar avbildning `en-GB` med språket `en-GB-George-Apollo` och rösten.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Behållar avbildning `en-GB` med språket `en-GB-HazelRUS` och rösten.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Behållar avbildning `en-GB` med språket `en-GB-Susan-Apollo` och rösten.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Behållar avbildning `en-IE` med språket `en-IE-Sean` och rösten.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Behållar avbildning `en-IN` med språket `en-IN-Heera-Apollo` och rösten.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Behållar avbildning `en-IN` med språket `en-IN-PriyaRUS` och rösten.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Behållar avbildning `en-IN` med språket `en-IN-Ravi-Apollo` och rösten.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning `en-US` med språket `en-US-BenjaminRUS` och rösten.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning `en-US` med språket `en-US-Guy24kRUS` och rösten.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning `en-US` med språket `en-US-Jessa24kRUS` och rösten.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Behållar avbildning `en-US` med språket `en-US-JessaRUS` och rösten.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Behållar avbildning `en-US` med språket `en-US-ZiraRUS` och rösten.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Behållar avbildning `es-ES` med språket `es-ES-HelenaRUS` och rösten.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Behållar avbildning `es-ES` med språket `es-ES-Laura-Apollo` och rösten.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Behållar avbildning `es-ES` med språket `es-ES-Pablo-Apollo` och rösten.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Behållar avbildning `es-MX` med språket `es-MX-HildaRUS` och rösten.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Behållar avbildning `es-MX` med språket `es-MX-Raul-Apollo` och rösten.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Behållar avbildning `fi-FI` med språket `fi-FI-HeidiRUS` och rösten.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Behållar avbildning `fr-CA` med språket `fr-CA-Caroline` och rösten.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Behållar avbildning `fr-CA` med språket `fr-CA-HarmonieRUS` och rösten.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Behållar avbildning `fr-CH` med språket `fr-CH-Guillaume` och rösten.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Behållar avbildning `fr-FR` med språket `fr-FR-HortenseRUS` och rösten.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Behållar avbildning `fr-FR` med språket `fr-FR-Julie-Apollo` och rösten.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Behållar avbildning `fr-FR` med språket `fr-FR-Paul-Apollo` och rösten.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Behållar avbildning `he-IL` med språket `he-IL-Asaf` och rösten.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Behållar avbildning `hi-IN` med språket `hi-IN-Hemant` och rösten.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Behållar avbildning `hi-IN` med språket `hi-IN-Kalpana-Apollo` och rösten.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Behållar avbildning `hi-IN` med språket `hi-IN-Kalpana` och rösten.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Behållar avbildning `hi-IN` med språket `hi-IN-Kalpana` och rösten.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Behållar avbildning `hr-HR` med språket `hr-HR-Matej` och rösten.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Behållar avbildning `hu-HU` med språket `hu-HU-Szabolcs` och rösten.        |
| `1.3.0-amd64-id-id-andika-preview`          | Behållar avbildning `id-ID` med språket `id-ID-Andika` och rösten.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Behållar avbildning `it-IT` med språket `it-IT-Cosimo-Apollo` och rösten.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Behållar avbildning `it-IT` med språket `it-IT-LuciaRUS` och rösten.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållar avbildning `ja-JP` med språket `ja-JP-Ayumi-Apollo` och rösten.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Behållar avbildning `ja-JP` med språket `ja-JP-HarukaRUS` och rösten.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållar avbildning `ja-JP` med språket `ja-JP-Ichiro-Apollo` och rösten.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Behållar avbildning `ko-KR` med språket `ko-KR-HeamiRUS` och rösten.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Behållar avbildning `ms-MY` med språket `ms-MY-Rizwan` och rösten.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Behållar avbildning `nb-NO` med språket `nb-NO-HuldaRUS` och rösten.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Behållar avbildning `nl-NL` med språket `nl-NL-HannaRUS` och rösten.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Behållar avbildning `pl-PL` med språket `pl-PL-PaulinaRUS` och rösten.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Behållar avbildning `pt-BR` med språket `pt-BR-Daniel-Apollo` och rösten.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Behållar avbildning `pt-BR` med språket `pt-BR-HeloisaRUS` och rösten.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Behållar avbildning `pt-PT` med språket `pt-PT-HeliaRUS` och rösten.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Behållar avbildning `ro-RO` med språket `ro-RO-Andrei` och rösten.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Behållar avbildning `ru-RU` med språket `ru-RU-EkaterinaRUS` och rösten.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Behållar avbildning `ru-RU` med språket `ru-RU-Irina-Apollo` och rösten.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Behållar avbildning `ru-RU` med språket `ru-RU-Pavel-Apollo` och rösten.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Behållar avbildning `sk-SK` med språket `sk-SK-Filip` och rösten.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Behållar avbildning `sl-SI` med språket `sl-SI-Lado` och rösten.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Behållar avbildning `sv-SE` med språket `sv-SE-HedvigRUS` och rösten.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Behållar avbildning `ta-IN` med språket `ta-IN-Valluvar` och rösten.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Behållar avbildning `te-IN` med språket `te-IN-Chitra` och rösten.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Behållar avbildning `th-TH` med språket `th-TH-Pattara` och rösten.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Behållar avbildning `tr-TR` med språket `tr-TR-SedaRUS` och rösten.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Behållar avbildning `vi-VN` med språket `vi-VN-An` och rösten.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning `zh-CN` med språket `zh-CN-HuihuiRUS` och rösten.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning `zh-CN` med språket `zh-CN-Kangkang-Apollo` och rösten. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning `zh-CN` med språket `zh-CN-Yaoyao-Apollo` och rösten.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Behållar avbildning `zh-HK` med språket `zh-HK-Danny-Apollo` och rösten.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Behållar avbildning `zh-HK` med språket `zh-HK-Tracy-Apollo` och rösten.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Behållar avbildning `zh-HK` med språket `zh-HK-TracyRUS` och rösten.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Behållar avbildning `zh-TW` med språket `zh-TW-HanHanRUS` och rösten.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Behållar avbildning `zh-TW` med språket `zh-TW-Yating-Apollo` och rösten.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Behållar avbildning `zh-TW` med språket `zh-TW-Zhiwei-Apollo` och rösten.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Behållar avbildning `de-DE` med språket `de-DE-Hedda` och rösten.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Behållar avbildning `de-DE` med språket `de-DE-HeddaRUS` och rösten.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Behållar avbildning `de-DE` med språket `de-DE-Stefan-Apollo` och rösten.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Behållar avbildning `en-AU` med språket `en-AU-Catherine` och rösten.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Behållar avbildning `en-AU` med språket `en-AU-HayleyRUS` och rösten.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Behållar avbildning `en-GB` med språket `en-GB-George-Apollo` och rösten.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Behållar avbildning `en-GB` med språket `en-GB-HazelRUS` och rösten.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Behållar avbildning `en-GB` med språket `en-GB-Susan-Apollo` och rösten.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Behållar avbildning `en-IN` med språket `en-IN-Heera-Apollo` och rösten.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Behållar avbildning `en-IN` med språket `en-IN-PriyaRUS` och rösten.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Behållar avbildning `en-IN` med språket `en-IN-Ravi-Apollo` och rösten.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning `en-US` med språket `en-US-BenjaminRUS` och rösten.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning `en-US` med språket `en-US-Guy24kRUS` och rösten.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning `en-US` med språket `en-US-Jessa24kRUS` och rösten.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Behållar avbildning `en-US` med språket `en-US-JessaRUS` och rösten.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Behållar avbildning `en-US` med språket `en-US-ZiraRUS` och rösten.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Behållar avbildning `es-ES` med språket `es-ES-HelenaRUS` och rösten.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Behållar avbildning `es-ES` med språket `es-ES-Laura-Apollo` och rösten.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Behållar avbildning `es-ES` med språket `es-ES-Pablo-Apollo` och rösten.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Behållar avbildning `es-MX` med språket `es-MX-HildaRUS` och rösten.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Behållar avbildning `es-MX` med språket `es-MX-Raul-Apollo` och rösten.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Behållar avbildning `fr-CA` med språket `fr-CA-Caroline` och rösten.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Behållar avbildning `fr-CA` med språket `fr-CA-HarmonieRUS` och rösten.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Behållar avbildning `fr-FR` med språket `fr-FR-HortenseRUS` och rösten.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Behållar avbildning `fr-FR` med språket `fr-FR-Julie-Apollo` och rösten.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Behållar avbildning `fr-FR` med språket `fr-FR-Paul-Apollo` och rösten.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Behållar avbildning `it-IT` med språket `it-IT-Cosimo-Apollo` och rösten.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Behållar avbildning `it-IT` med språket `it-IT-LuciaRUS` och rösten.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållar avbildning `ja-JP` med språket `ja-JP-Ayumi-Apollo` och rösten.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Behållar avbildning `ja-JP` med språket `ja-JP-HarukaRUS` och rösten.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållar avbildning `ja-JP` med språket `ja-JP-Ichiro-Apollo` och rösten.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Behållar avbildning `ko-KR` med språket `ko-KR-HeamiRUS` och rösten.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Behållar avbildning `pt-BR` med språket `pt-BR-Daniel-Apollo` och rösten.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Behållar avbildning `pt-BR` med språket `pt-BR-HeloisaRUS` och rösten.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning `zh-CN` med språket `zh-CN-HuihuiRUS` och rösten.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning `zh-CN` med språket `zh-CN-Kangkang-Apollo` och rösten. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning `zh-CN` med språket `zh-CN-Yaoyao-Apollo` och rösten.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Behållar avbildning `de-DE` med språket `de-DE-Hedda` och rösten.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Behållar avbildning `de-DE` med språket `de-DE-Hedda` och rösten.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Behållar avbildning `de-DE` med språket `de-DE-HeddaRUS` och rösten.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Behållar avbildning `de-DE` med språket `de-DE-Stefan-Apollo` och rösten.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Behållar avbildning `en-AU` med språket `en-AU-Catherine` och rösten.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Behållar avbildning `en-AU` med språket `en-AU-HayleyRUS` och rösten.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Behållar avbildning `en-GB` med språket `en-GB-George-Apollo` och rösten.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Behållar avbildning `en-GB` med språket `en-GB-HazelRUS` och rösten.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Behållar avbildning `en-GB` med språket `en-GB-Susan-Apollo` och rösten.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Behållar avbildning `en-IN` med språket `en-IN-Heera-Apollo` och rösten.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Behållar avbildning `en-IN` med språket `en-IN-PriyaRUS` och rösten.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Behållar avbildning `en-IN` med språket `en-IN-Ravi-Apollo` och rösten.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning `en-US` med språket `en-US-BenjaminRUS` och rösten.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning `en-US` med språket `en-US-Guy24kRUS` och rösten.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning `en-US` med språket `en-US-Jessa24kRUS` och rösten.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Behållar avbildning `en-US` med språket `en-US-JessaRUS` och rösten.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Behållar avbildning `en-US` med språket `en-US-ZiraRUS` och rösten.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Behållar avbildning `es-ES` med språket `es-ES-HelenaRUS` och rösten.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Behållar avbildning `es-ES` med språket `es-ES-Laura-Apollo` och rösten.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Behållar avbildning `es-ES` med språket `es-ES-Pablo-Apollo` och rösten.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Behållar avbildning `es-MX` med språket `es-MX-HildaRUS` och rösten.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Behållar avbildning `es-MX` med språket `es-MX-Raul-Apollo` och rösten.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Behållar avbildning `fr-CA` med språket `fr-CA-Caroline` och rösten.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Behållar avbildning `fr-CA` med språket `fr-CA-HarmonieRUS` och rösten.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Behållar avbildning `fr-FR` med språket `fr-FR-HortenseRUS` och rösten.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Behållar avbildning `fr-FR` med språket `fr-FR-Julie-Apollo` och rösten.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Behållar avbildning `fr-FR` med språket `fr-FR-Paul-Apollo` och rösten.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Behållar avbildning `it-IT` med språket `it-IT-Cosimo-Apollo` och rösten.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Behållar avbildning `it-IT` med språket `it-IT-LuciaRUS` och rösten.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Behållar avbildning `ja-JP` med språket `ja-JP-Ayumi-Apollo` och rösten.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Behållar avbildning `ja-JP` med språket `ja-JP-HarukaRUS` och rösten.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Behållar avbildning `ja-JP` med språket `ja-JP-Ichiro-Apollo` och rösten.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Behållar avbildning `ko-KR` med språket `ko-KR-HeamiRUS` och rösten.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Behållar avbildning `pt-BR` med språket `pt-BR-Daniel-Apollo` och rösten.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Behållar avbildning `pt-BR` med språket `pt-BR-HeloisaRUS` och rösten.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning `zh-CN` med språket `zh-CN-HuihuiRUS` och rösten.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning `zh-CN` med språket `zh-CN-Kangkang-Apollo` och rösten. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning `zh-CN` med språket `zh-CN-Yaoyao-Apollo` och rösten.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Behållar avbildning `en-US` med språket `en-US-BenjaminRUS` och rösten.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Behållar avbildning `en-US` med språket `en-US-Guy24kRUS` och rösten.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Behållar avbildning `en-US` med språket `en-US-Jessa24kRUS` och rösten.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Behållar avbildning `en-US` med språket `en-US-JessaRUS` och rösten.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Behållar avbildning `en-US` med språket `en-US-ZiraRUS` och rösten.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Behållar avbildning `zh-CN` med språket `zh-CN-HuihuiRUS` och rösten.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Behållar avbildning `zh-CN` med språket `zh-CN-Kangkang-Apollo` och rösten. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Behållar avbildning `zh-CN` med språket `zh-CN-Yaoyao-Apollo` och rösten.   |

## <a name="key-phrase-extraction"></a>Extrahering av nyckelfraser

[Extrahering av diskussionsämne][ta-kp] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `keyphrase`. Det fullständigt kvalificerade namnet på behållar `mcr.microsoft.com/azure-cognitive-services/keyphrase`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Språkidentifiering

[Språkidentifiering][ta-la] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `language`. Det fullständigt kvalificerade namnet på behållar `mcr.microsoft.com/azure-cognitive-services/language`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Attitydanalys

[Attitydanalys][ta-se] behållar avbildningen finns i `mcr.microsoft.com` behållar registrets syndikering. Den finns i `azure-cognitive-services` lagrings platsen och får namnet `sentiment`. Det fullständigt kvalificerade namnet på behållar `mcr.microsoft.com/azure-cognitive-services/sentiment`avbildningen är,.

Den här behållar avbildningen har följande tillgängliga Taggar:

| Bildtaggar                    | Obs! |
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
