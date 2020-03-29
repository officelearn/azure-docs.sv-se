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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73465923"
---
## <a name="create-a-luis-resource"></a>Skapa en LUIS-resurs

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Klicka på [Skapa **språk understanding** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Prenumeration|Välj lämplig prenumeration|
    |Location|Välj en plats i närheten och tillgänglig|
    |Prisnivå|`F0`- den minimala prisnivån|
    |Resursgrupp|Välj en tillgänglig resursgrupp|

1. Klicka på **Skapa** och vänta tills resursen har skapats. När den har skapats navigerar du till resurssidan.
1. Samla in `endpoint` konfigurerad och en API-nyckel, se [samla in nödvändiga parametrar](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
