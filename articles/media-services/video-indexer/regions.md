---
title: Regioner där Video Indexer är tillgängligt - Azure
titleSuffix: Azure Media Services
description: I den här artikeln beskrivs Azure-regioner där Azure Media Services Video Indexer är tillgängligt.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382757"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-regioner där Video Indexer finns

Video Indexer API:er innehåller en **platsparameter** som du bör ange till azure-regionen som samtalet ska dirigeras till. Detta måste vara en [Azure-region där Video Indexer är tillgänglig](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Platser

Platsparametern måste anges azure-regionkodnamnet som värde. **location** Om du använder Video Indexer i förhandsgranskningsläge bör du sätta *"testversion"* som värde. Annars, för att få kodnamnet för Azure-regionen som ditt konto finns i och att ditt samtal ska dirigeras till, kan du köra följande rad i [Azure CLI:](/cli/azure)

```azurecli-interactive
az account list-locations
```

När du har kört raden som visas ovan får du en lista över alla Azure-regioner. Navigera till Azure-regionen som har det *displayName* du letar efter och använd *dess namnvärde* för platsparametern. **location**

Till exempel för Azure-regionen Västra US 2 (visas nedan) använder du **location** "westus2" för platsparametern.

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

- [Anpassa språkmodell med API:er](customize-language-model-with-api.md)
- [Anpassa brands-modellen med API:er](customize-brands-model-with-api.md)
- [Anpassa personmodell med API:er](customize-person-model-with-api.md)
