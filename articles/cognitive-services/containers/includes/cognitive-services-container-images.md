---
ms.openlocfilehash: fe9879f9574fe1496ebdf20ac76fe5b759d4ea6a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051235"
---
### <a name="container-repositories-and-images"></a>Behållar databaser och avbildningar

Tabellerna nedan innehåller en omfattande lista över tillgängliga behållar avbildningar som erbjuds av Azure Cognitive Services.

#### <a name="public-container-registry-mcrmicrosoftcom"></a>Offentlig (container Registry: `mcr.microsoft.com`)

Microsoft Container Registry är värd för alla allmänna tillgänglighets behållare (GA) för Cognitive Services.

| Tjänsten | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Textanalys](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-preview-container-registry-containerpreviewazurecrio"></a>Offentlig för hands version (container `containerpreview.azurecr.io`Registry:)

I förhands gransknings registret är värd för alla behållarna "offentlig för hands version" för Cognitive Services som ännu inte har förfallit till allmän tillgänglighet (GA). Dessa behållare kräver en formell begäran om åtkomst för att kunna använda dem.

| Tjänsten | Container | Container Registry/namn på lagrings plats/avbildning |
|--|--|--|
| [Avvikelse detektor](../../anomaly-detector/anomaly-detector-container-howto.md) | Avvikelseidentifiering | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Visuellt innehåll](../../Computer-vision/computer-vision-how-to-install-containers.md) | Identifiera text | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Ansikte](../../face/face-how-to-install-containers.md) | Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulär igenkänning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Formigenkänning | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Tal till text | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service API](../../speech-service/speech-container-howto.md) | Text till tal | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
