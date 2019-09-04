---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274688"
---
## <a name="azure-cognitive-service-resource-types"></a>Resurs typer för Azure kognitiva tjänster

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Du kan komma åt Azure Cognitive Services via två olika resurser: En resurs med flera tjänster eller en enskild tjänst. Med dessa prenumerationer kan du ansluta till antingen en enskild kognitiv tjänst eller flera kognitiva tjänster samtidigt.

### <a name="multi-service-resource"></a>Resurs för flera tjänster

Prenumererar på en Cognitive Services-resurs med flera tjänster:
* Gör att du kan använda en enda Azure-resurs för de flesta Azure-Cognitive Services.
* Du får en enda nyckel som kan användas med flera Azure-Cognitive Services.
* Konsoliderar faktureringen från de tjänster som du använder. Mer information finns i [Cognitive Services prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/) .

>[!WARNING]
> För tillfället stöder dessa tjänster **inte** nycklar med flera tjänster: QnA Maker, tal tjänster, Custom Vision och avvikelse detektor.

### <a name="single-service-resource"></a>Resurs för enskild tjänst

Prenumererar på en Cognitive Services resurs med en tjänst:
* Gör att du kan använda en angiven kognitiv tjänst som du skapar resursen för (till exempel Visuellt innehåll eller tal tjänster).
* Du får en nyckel som är unik för den kognitiva tjänst som du skapar en resurs för.