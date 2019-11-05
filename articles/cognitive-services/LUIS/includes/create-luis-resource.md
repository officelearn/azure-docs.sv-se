---
title: Skapa LUIS-resurs
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465923"
---
## <a name="create-a-luis-resource"></a>Skapa en LUIS-resurs

1. Logga in på [Azure Portal](https://portal.azure.com)
1. Klicka på [skapa **language Understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Plats|Välj valfri närliggande och tillgänglig plats|
    |Prisnivå|`F0` – den minimala pris nivån|
    |Resursgrupp|Välj en tillgänglig resurs grupp|

1. Klicka på **skapa** och vänta tills resursen har skapats. När den har skapats går du till resurs sidan.
1. Samla in konfigurerade `endpoint` och en API-nyckel, se [samla in obligatoriska parametrar](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
