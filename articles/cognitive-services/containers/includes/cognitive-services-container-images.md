---
title: Behållar databaser och avbildningar
services: cognitive-services
author: aahill
manager: nitinme
description: Två tabeller som representerar behållar register, databaser och avbildnings namn för alla kognitiva tjänst erbjudanden.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 2c2ebe9b419100163ae55c1be85dd1464904e841
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94978503"
---
### <a name="container-repositories-and-images"></a>Behållar databaser och avbildningar

Tabellerna nedan innehåller en lista över tillgängliga behållar avbildningar som erbjuds av Azure Cognitive Services. En fullständig lista över alla tillgängliga behållar avbildnings namn och deras tillgängliga taggar finns i [Cognitive Services behållar avbildnings Taggar](../container-image-tags.md). 

#### <a name="generally-available"></a>Allmänt tillgänglig 

Microsoft Container Registry (MCR) syndikerar alla allmänt tillgängliga behållare för Cognitive Services. Behållarna är också tillgängliga direkt från [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/language/luis` |

Mer information finns i [så här kör och installerar du Luis-behållare](../../LUIS/luis-container-howto.md) .

**Textanalys**

| Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|
| Attitydanalys v3 (engelska) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en` |
| Attitydanalys v3 (spanska) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-es` |
| Attitydanalys v3 (franska) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-fr` |
| Attitydanalys v3 (italiensk) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-it` |
| Attitydanalys v3 (tyska) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-de` |
| Attitydanalys v3 (kinesiskt-förenklat) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zh` |
| Attitydanalys v3 (kinesiska – traditionell) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-zht` |
| Attitydanalys v3 (japanska) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-ja` |
| Attitydanalys v3 (portugisiska) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-pt` |
| Attitydanalys v3 (nederländska) | `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-nl` |

Mer information finns i [köra och installera textanalys behållare](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) .

**Avvikelseidentifiering** 

| Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|
| Avvikelse detektor | `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` |

Mer information finns i [köra och installera avvikelse detektor behållare](../../anomaly-detector/anomaly-detector-container-howto.md) .

**Tal tjänst**

> [!NOTE]
> Om du vill använda tal behållare måste du fylla i ett [formulär för online-begäran](https://aka.ms/csgate).

| Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|
| [Tal till text](../../speech-service/speech-container-howto.md?tab=stt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` |
| [Custom Speech till text](../../speech-service/speech-container-howto.md?tab=cstt) | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` |
| [Text till tal](../../speech-service/speech-container-howto.md?tab=tts) | `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` |

#### <a name="ungated-preview"></a>"Icke-grind"-förhands granskning 

Följande för hands behållare är tillgängliga offentligt. Microsoft Container Registry (MCR) syndikerar alla allmänt tillgängliga icke-hälsocontainrar för Cognitive Services. Behållarna är också tillgängliga direkt från [Docker-hubben](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/textanalytics/language` |


#### <a name="gated-preview"></a>"Gated"-för hands version

Tidigare var gated Preview-behållare värdbaserade på `containerpreview.azurecr.io` lagrings platsen. Från och med september 22 2020 finns de här behållarna (utom Textanalys för hälsa) som finns på Microsoft Container Registry (MCR) och nedladdning av dem kräver inte kommandot Docker login. Om du vill använda behållaren måste du:

1. Slutför ett [formulär för begäran](https://aka.ms/csgate) med ditt Azure-prenumerations-ID och användar scenario. 
2. Hämta behållaren från MCR vid godkännande. 
3. Använd nyckeln och slut punkten från en lämplig Azure-resurs för att autentisera behållaren vid körning. 

| Tjänst | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Läsa v 2.0 | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Läsa v 3.1 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview` |
| [Visuellt innehåll](../../computer-vision/spatial-analysis-container.md) | Rumslig analys | `mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Anpassad text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=lid) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ntts) | Neurala text till tal | `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` |
| [Textanalys för hälsa](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Textanalys för hälsa | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |