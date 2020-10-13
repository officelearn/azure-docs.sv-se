---
title: Moln och regioner där Azure Media Services v3 är tillgängligt
description: Den här artikeln pratar om Azure-moln och regioner där Azure Media Services v3 är tillgängligt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 9c54482dc188141c4fc697bd43628b81e0ddc29f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597129"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Moln och regioner där Azure Media Services v3 finns

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3 är tillgängligt via Azure Resource Manager manifest i Global Azure, Azure Government, Azure Germany, Azure Kina 21Vianet. Alla Media Services funktioner är dock inte tillgängliga i alla Azure-moln. Det här dokumentet beskriver tillgänglighet av Main Media Services v3-komponenter.

## <a name="feature-availability-in-azure-clouds"></a>Funktions tillgänglighet i Azure-moln

| Funktion|Globala Azure-regioner | Azure Government|Azure Tyskland|Azure Kina 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| [StandardEncoderPreset](encoding-concept.md) | Tillgänglig | Tillgänglig | Tillgänglig | Tillgänglig |
| [LiveEvents](live-streaming-overview.md) | Tillgänglig | Tillgänglig | Tillgänglig | Tillgänglig |
| [Strömnings slut punkter](streaming-endpoint-concept.md) | Tillgänglig | Tillgänglig | Tillgänglig | Tillgänglig |

## <a name="feature-availability-in-preview"></a>Funktions tillgänglighet för för hands version

[LiveTranscription](live-transcription.md) är tillgängligt i följande regioner:

- Sydostasien
- Europa, västra
- Norra Europa
- East US
- Central US
- USA, södra centrala
- USA, västra 2
- Brasilien, södra

## <a name="regionsgeographieslocations"></a>Regioner/geografiska områden/platser

[Regioner där Azure Media Servicess tjänsten har distribuerats](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Region kod namn

När du behöver ange **plats** parametern måste du ange regions kod namnet som **plats** värde. Om du vill hämta kod namnet för den region där ditt konto finns och att ditt anrop ska dirigeras till kan du köra följande rad i [Azure CLI](/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

När du har kört raden som visas ovan får du en lista över alla Azure-regioner. Navigera till den Azure-region som har det *visnings* namn du söker och använd dess *Name* -värde för parametern **location** .

För Azure-regionen Västra USA 2 (visas nedan) använder du till exempel "westus2" för **plats** parametern.

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

Följande slut punkter är viktiga att känna till när du ansluter till Media Services konton från olika nationella Azure-moln.

### <a name="global-azure"></a>Global Azure

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Autentisering | `https://login.microsoftonline.com/` |
| Token-Audience | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Autentisering | `https://login.microsoftonline.us/` |
| Token-Audience | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Tyskland

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Autentisering | `https://login.microsoftonline.de/` |
| Token-Audience | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure Kina 21Vianet

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Autentisering | `https://login.chinacloudapi.cn/` |
| Token-Audience |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Se även

* [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/)
* [Geografiska områden i Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
