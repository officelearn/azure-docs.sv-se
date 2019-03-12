---
title: Moln och regioner där Azure Media Services v3 är tillgängligt | Microsoft Docs
description: Den här artikeln handlar om Azure-moln och regioner där Azure Media Services v3 är tillgängligt.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: 4f8851248c395a1f03c46490c8eb5e71221dd133
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549909"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Moln och regioner där Azure Media Services v3 finns

Azure Media Services v3 är tillgängligt via Azure Resource Manager-manifestet i globala Azure, Azure Government, Azure Tyskland, Azure Kina 21Vianet. Men finns inte alla Media Services-funktioner i alla moln på Azure. Det här dokumentet beskrivs tillgången på Media Services v3 huvudkomponenter.

## <a name="feature-availability-in-azure-clouds"></a>Tillgängliga funktioner i Azure-moln

| Funktion|Global Azure Regions | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| [StandardEncoderPreset](encoding-concept.md) | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| [LiveEvents](live-streaming-overview.md) | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |
| [Strömningsslutpunkter](streaming-endpoint-concept.md) | Tillgängligt | Tillgängligt | Tillgängligt | Tillgängligt |

## <a name="regionsgeographieslocations"></a>Regioner-geografiska områden-platser

* [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/)
* [Produkten efter region](https://azure.microsoft.com/global-infrastructure/services/)
* [Geografiska Azure-områden](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/)

### <a name="region-code-name"></a>Kod Regionsnamn 

När du behöver ange den **plats** parameter, måste du ange kodnamnet region som den **plats** värde. Hämtar namnet på koden för den region som ditt konto tillhör och att ditt samtal ska vidarebefordras till, kan du köra följande rad i [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

När du kör raden ovan kan du hämta en lista över alla Azure-regioner. Gå till Azure-region som har den *displayName* du letar efter och använda dess *namn* värde för den **plats** parametern.

Till exempel för Azure-regionen USA, västra 2 (visas nedan), använder du ”västra USA 2” för den **plats** parametern.

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

Följande slutpunkter är viktigt att veta när du ansluter till Media Services-konton från olika nationella Azure-moln.

### <a name="global-azure"></a>Globalt Azure

|Slutpunkter ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Authentication | `https://login.microsoftonline.com/` | 
| Tokenmålgruppen | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Slutpunkter||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Authentication | `https://login.microsoftonline.us/` | 
| Tokenmålgruppen | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Tyskland

| Slutpunkter ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Authentication | `https://login.microsoftonline.de/` |
| Tokenmålgruppen | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure Kina 21Vianet

|Slutpunkter||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Authentication | `https://login.chinacloudapi.cn/` |
| Tokenmålgruppen |  `https://management.core.chinacloudapi.cn/` |

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
