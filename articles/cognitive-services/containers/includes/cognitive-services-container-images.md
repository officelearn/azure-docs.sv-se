---
title: Behållar databaser och avbildningar
services: cognitive-services
author: aahill
manager: nitinme
description: Två tabeller som representerar behållar register, databaser och avbildnings namn för alla kognitiva tjänst erbjudanden.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 6d47829d32ed0b7ded545c73748cda1019d94bde
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721441"
---
### <a name="container-repositories-and-images"></a>Behållar databaser och avbildningar

Tabellerna nedan innehåller en lista över tillgängliga behållar avbildningar som erbjuds av Azure Cognitive Services. En fullständig lista över alla tillgängliga behållar avbildnings namn och deras tillgängliga taggar finns i [Cognitive Services behållar avbildnings Taggar](../container-image-tags.md). 

#### <a name="generally-available"></a>Allmänt tillgänglig 

Microsoft Container Registry (MCR) syndikerar alla allmänt tillgängliga behållare för Cognitive Services. Behållarna är också tillgängliga direkt från [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| LUIS-behållare | Container Registry/namn på lagrings plats/avbildning |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Mer information finns i [så här kör och installerar du Luis-behållare](../../LUIS/luis-container-howto.md) .

#### <a name="text-analytics"></a>[Textanalys](#tab/text-analytics)

| Textanalys behållare | Container Registry/namn på lagrings plats/avbildning |
|--|--|
| Attitydanalys v3 (engelska) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Attitydanalys v3 (spanska) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Attitydanalys v3 (franska) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Attitydanalys v3 (italiensk) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Attitydanalys v3 (tyska) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Attitydanalys v3 (kinesiskt-förenklat) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Attitydanalys v3 (kinesiska – traditionell) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Attitydanalys v3 (japanska) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Attitydanalys v3 (portugisiska) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Attitydanalys v3 (nederländska) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Mer information finns i [köra och installera textanalys behållare](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Offentlig "icke-grind"-förhands granskning (container Registry: `mcr.microsoft.com` )

Följande för hands behållare är tillgängliga offentligt. Microsoft Container Registry (MCR) syndikerar alla allmänt tillgängliga icke-hälsocontainrar för Cognitive Services. Behållarna är också tillgängliga direkt från [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Avvikelse detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Avvikelseidentifiering | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Offentlig "gated"-förhands granskning (container Registry: `containerpreview.azurecr.io` )

Följande gated Preview-behållare finns i förhands gransknings registret för behållare och kräver åtkomst till ett program. Se följande behållar artiklar för mer information.

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Läsa | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Ansikte](../../face/face-how-to-install-containers.md) | Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulär igenkänning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=stt) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech till text | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=tts) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Anpassad text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
