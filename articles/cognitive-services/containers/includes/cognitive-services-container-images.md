---
title: Behållar databaser och avbildningar
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Två tabeller som representerar behållar register, databaser och avbildnings namn för alla kognitiva tjänst erbjudanden.
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2019
ms.author: dapine
ms.openlocfilehash: c1593cb3dad7ee1370a66747fa3fe47e93c19957
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499169"
---
### <a name="container-repositories-and-images"></a>Behållar databaser och avbildningar

Tabellerna nedan innehåller en omfattande lista över tillgängliga behållar avbildningar som erbjuds av Azure Cognitive Services.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Offentlig "icke-grind" (container Registry: `mcr.microsoft.com`)

Microsoft Container Registry är värd för alla allmänt tillgängliga "ej grindade" behållare för Cognitive Services.

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Offentlig "gated"-förhands granskning (container Registry: `containerpreview.azurecr.io`)

I förhands gransknings registret är värd för alla offentligt tillgängliga "gated"-behållare för Cognitive Services. Dessa behållare kräver en formell begäran om åtkomst för att kunna använda dem.

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Avvikelseidentifiering](../../anomaly-detector/anomaly-detector-container-howto.md) | Avvikelseidentifiering | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Läsa | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Vändning](../../face/face-how-to-install-containers.md) | Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulär igenkänning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
< < < < < < < HEAD | [API för tal tjänst](../../speech-service/speech-container-howto.md?tab=stt) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API för tal tjänst](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech-till-text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` | | [API för tal tjänst](../../speech-service/speech-container-howto.md?tab=tts) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [API för tal tjänst](../../speech-service/speech-container-howto.md?tab=ctts) | Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` | ======= | [API för tal tjänst](../../speech-service/speech-container-howto.md) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` | | [API för tal tjänst](../../speech-service/speech-container-howto.md) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` | | [Translator text](../../translator/how-to-install-containers.md) | Translator Text | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |
>>>>>>> ReFS/fjärst/Microsoft docs/Master
