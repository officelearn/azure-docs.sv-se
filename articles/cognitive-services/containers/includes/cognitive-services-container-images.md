---
title: Behållar databaser och avbildningar
services: cognitive-services
author: aahill
manager: nitinme
description: Två tabeller som representerar behållar register, databaser och avbildnings namn för alla kognitiva tjänst erbjudanden.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876850"
---
### <a name="container-repositories-and-images"></a>Behållar databaser och avbildningar

Tabellerna nedan innehåller en lista över tillgängliga behållar avbildningar som erbjuds av Azure Cognitive Services. En fullständig lista över alla tillgängliga behållar avbildnings namn och deras tillgängliga taggar finns i [Cognitive Services behållar avbildnings Taggar](../container-image-tags.md). För närvarande finns det inga Cognitive Services behållare som är allmänt tillgängliga (GA). Under tiden, tills ytterligare meddelanden görs – behållare är tillgängliga som antingen offentlig eller för *hands version av offentlig* *port* .

 - *Offentlig utan grind*: behållare är tillgängliga offentligt utan en hantera mekanism.
 - För *hands version av offentlig gated*: behållare är tillgängliga offentligt, men kräver först formell begäran om att få åtkomst till behållar registret.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Offentlig "icke-grind" (container Registry: `mcr.microsoft.com`)

Microsoft Container Registry (MCR) syndikerar alla allmänt tillgängliga "ej" "ej" "icke-" icke-Cognitive Services "icke-" icke-"icke- Behållarna är också tillgängliga direkt från [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Offentlig "gated"-förhands granskning `containerpreview.azurecr.io`(container Registry:)

I förhands gransknings registret är värd för alla offentligt tillgängliga "gated"-behållare för Cognitive Services. Dessa behållare kräver en formell begäran om åtkomst till dem via behållar registret.

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Avvikelse detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Avvikelseidentifiering | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Läsa | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Ansikte](../../face/face-how-to-install-containers.md) | Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulär igenkänning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech till text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
