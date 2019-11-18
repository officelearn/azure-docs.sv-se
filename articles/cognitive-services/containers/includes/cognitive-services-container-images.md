---
title: Behållar databaser och avbildningar
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Två tabeller som representerar behållar register, databaser och avbildnings namn för alla kognitiva tjänst erbjudanden.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/15/2019
ms.author: dapine
ms.openlocfilehash: 2058dd6e52ddb417e24368b27384df9a222c378e
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74142221"
---
### <a name="container-repositories-and-images"></a>Behållar databaser och avbildningar

Tabellerna nedan innehåller en lista över tillgängliga behållar avbildningar som erbjuds av Azure Cognitive Services. En fullständig lista över alla tillgängliga behållar avbildnings namn och deras tillgängliga taggar finns i [Cognitive Services behållar avbildnings Taggar](../container-image-tags.md).

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Offentlig "icke-grind" (container Registry: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) syndikerar alla allmänt tillgängliga "ej" "ej" "icke-" icke-Cognitive Services "icke-" icke-"icke- De är också tillgängliga direkt från [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

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
| [Ansikte](../../face/face-how-to-install-containers.md) | Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulär igenkänning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech till text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
