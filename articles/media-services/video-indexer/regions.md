---
title: Regioner där Video Indexer - Azure
titlesuffix: Azure Media Services
description: Den här artikeln handlar om Azure-regioner som Video Indexer är tillgängligt.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 8c1d51c2bb6062f1360c831549e523a5821ff14b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532178"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-regioner där Video Indexer finns

Video Indexer API: er innehåller en **plats** parameter som du bör ange till Azure-region som anropet ska dirigeras. Det här måste vara en [Azure-region som Video Indexer är tillgängligt](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Platser

Den **plats** parametern måste anges kodnamnet Azure-region som sitt värde. Om du använder Video Indexer i förhandsgranskningsläge, bör du placera *”utvärderingsversion”* som värde. I annat fall för att få kodnamnet för Azure-region som ditt konto tillhör och att ditt samtal ska vidarebefordras till, du kan köra följande rad i [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

När du kör raden ovan kan du hämta en lista över alla Azure-regioner. Gå till Azure-region som har den *displayName* du letar efter och använda dess *namn* värde för den **plats** parametern.

Till exempel för Azure-regionen USA, västra 2 (visas nedan), använder du ”västra USA 2” för den **plats** parametern.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Nästa steg

- [Anpassa språkmodellen med API: er](customize-language-model-with-api.md)
- [Anpassa varumärken modell med API: er](customize-brands-model-with-api.md)
- [Anpassa Person modell med API: er](customize-person-model-with-api.md)
