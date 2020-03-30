---
title: Moln och regioner där Azure Media Services v3 är tillgängligt
description: I den här artikeln beskrivs Azure-moln och regioner där Azure Media Services v3 är tillgängligt.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382978"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Moln och regioner där Azure Media Services v3 finns

Azure Media Services v3 är tillgängligt via Azure Resource Manager-manifestet i globala Azure, Azure Government, Azure Germany, Azure China 21Vianet. Alla Media Services-funktioner är dock inte tillgängliga i alla Azure-moln. I det här dokumentet beskrivs tillgängligheten för de viktigaste mediatjänsterna v3-komponenter.

## <a name="feature-availability-in-azure-clouds"></a>Funktionstillgänglighet i Azure-moln

| Funktion|Globala Azure-regioner | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| [AudioAnalyzerFörbeka](analyzing-video-audio-files-concept.md) |  Tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| [StandardEncoderPreset](encoding-concept.md) | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| [LiveEvents](live-streaming-overview.md) | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |

## <a name="regionsgeographieslocations"></a>Regioner/geografiska områden/platser

[Regioner där Azure Media Services-tjänsten distribueras](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Namn på regionkod

När du behöver **location** ange platsparametern måste du ange regionkodnamnet som **platsvärde.** Om du vill hämta kodnamnet för den region som ditt konto finns i och som samtalet ska dirigeras till kan du köra följande rad i [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

När du har kört raden som visas ovan får du en lista över alla Azure-regioner. Navigera till Azure-regionen som har det *displayName* du letar efter och använd *dess namnvärde* för platsparametern. **location**

Till exempel för Azure-regionen Västra US 2 (visas nedan) använder du **location** "westus2" för platsparametern.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Slutpunkter  

Följande slutpunkter är viktiga att känna till när du ansluter till Media Services-konton från olika nationella Azure-moln.

### <a name="global-azure"></a>Global Azure

|Slutpunkter||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Autentisering | `https://login.microsoftonline.com/` |
| Token-målgrupp | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Slutpunkter||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Autentisering | `https://login.microsoftonline.us/` |
| Token-målgrupp | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Tyskland

| Slutpunkter ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Autentisering | `https://login.microsoftonline.de/` |
| Token-målgrupp | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure Kina 21Vianet

|Slutpunkter||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Autentisering | `https://login.chinacloudapi.cn/` |
| Token-målgrupp |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Se även

* [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografiska områden i Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
