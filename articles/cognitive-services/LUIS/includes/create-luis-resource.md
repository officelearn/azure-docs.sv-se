---
title: Stöd för containrar
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 52d8e1355558b197b193a50c7cde571799541268
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711839"
---
## <a name="create-a-luis-resource"></a>Skapa en LUIS-resurs

1. Logga in på den [Azure-portalen](https://portal.azure.com)
1. Klicka på [skapa **Språkförståelse**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS)
1. Ange alla nödvändiga inställningar:

    |Inställning|Värde|
    |--|--|
    |Namn|Önskat namn (2-64 tecken)|
    |Subscription|Välj lämplig prenumeration|
    |Location|Välj valfri plats i närheten och tillgänglig|
    |Prisnivå|`F0` -minimal prisnivån|
    |Resursgrupp|Välj en tillgänglig resursgrupp|

1. Klicka på **skapa** och vänta tills resursen som ska skapas. När den har skapats, gå till resurssidan
1. Samla in konfigurerats `endpoint` och en API-nyckel:

    |Resurs-fliken i portalen|Inställning|Värde|
    |--|--|--|
    |**Översikt**|Slutpunkt|Kopiera slutpunkten. Den liknar `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**nycklar**|API-nyckel|Kopiera 1 av två nycklar. Det är en 32 alfanumeriska tecken lång sträng utan mellanslag eller tankstreck, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
