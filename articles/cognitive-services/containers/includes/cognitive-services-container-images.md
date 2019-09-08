---
title: Behållar databaser och avbildningar
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Två tabeller som representerar behållar register, databaser och avbildnings namn för alla kognitiva tjänst erbjudanden.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 600c01fc2a273141b06996369231c2389c5d6d57
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775901"
---
### <a name="container-repositories-and-images"></a>Behållar databaser och avbildningar

Tabellerna nedan innehåller en omfattande lista över tillgängliga behållar avbildningar som erbjuds av Azure Cognitive Services.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Offentlig "icke-grind" (container Registry: `mcr.microsoft.com`)

Microsoft Container Registry är värd för alla allmänt tillgängliga "ej grindade" behållare för Cognitive Services.

| Tjänsten | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Offentlig "gated"-förhands granskning `containerpreview.azurecr.io`(container Registry:)

I förhands gransknings registret är värd för alla offentligt tillgängliga "gated"-behållare för Cognitive Services. Dessa behållare kräver en formell begäran om åtkomst för att kunna använda dem.

| Tjänsten | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Avvikelse detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Avvikelseidentifiering | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Identifiera text | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Ansikte](../../face/face-how-to-install-containers.md) | Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulär igenkänning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
