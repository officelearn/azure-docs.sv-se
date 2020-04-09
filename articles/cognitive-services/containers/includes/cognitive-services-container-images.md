---
title: Behållardatabaser och avbildningar
services: cognitive-services
author: aahill
manager: nitinme
description: Två tabeller som representerar behållarregister, databaser och bildnamn för alla Cognitive Service-erbjudanden.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876850"
---
### <a name="container-repositories-and-images"></a>Behållardatabaser och avbildningar

Tabellerna nedan är en lista över tillgängliga behållaravbildningar som erbjuds av Azure Cognitive Services. En fullständig lista över alla tillgängliga behållaravbildningsnamn och tillgängliga taggar finns i [Cognitive Services-behållaravbildningstaggar](../container-image-tags.md). För närvarande finns det inga Cognitive Services-behållare som är allmänt tillgängliga (GA). Tills vidare, tills ytterligare meddelanden görs - behållare finns tillgängliga som antingen *Public Ungated* eller *Public Gated Preview*.

 - *Offentliga Ungated*: behållare är tillgängliga publicly utan en gating mekanism.
 - *Offentlig gated preview*: behållare är tillgängliga offentligt, men kräver först formell begäran för att komma åt behållarregistret.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Offentliga "Ungated" (containerregister: `mcr.microsoft.com`)

McR-syndikaten (Microsoft Container Registry) syndikerar alla offentligt tillgängliga "hoverade" behållare för Cognitive Services. Behållarna är också tillgängliga direkt från [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Tjänst | Container | Behållarens register / Databas / Bildnamn |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Offentlig "Gated" Preview (containerregister: `containerpreview.azurecr.io`)

Registret Förhandsgranskning av behållare är värd för alla offentligt tillgängliga "gated"-behållare för Cognitive Services. Dessa behållare kräver en formell begäran om åtkomst till dem via deras behållarregister.

| Tjänst | Container | Behållarens register / Databas / Bildnamn |
|--|--|--|
| [Avvikelsedetektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Avvikelseidentifiering | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Läsa | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Ansikte](../../face/face-how-to-install-containers.md) | Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulärigenkänning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Anpassad tal-till-text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
