---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334250"
---
## <a name="azure-cognitive-service-resource-types"></a>Resurs typer för Azure kognitiva tjänster

> [!NOTE]
> Prenumerations ägare kan inaktivera skapande av Cognitive Services resurser för resurs grupper och prenumerationer genom att använda [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), tilldela en princip definition som inte är tillåtna resurs typer och ange  **Microsoft. CognitiveServices/Accounts** som mål resurs typ.

Du kan komma åt Azure Cognitive Services via två olika resurser: En resurs med flera tjänster eller en enskild tjänst. Med dessa prenumerationer kan du ansluta till antingen en enskild tjänst eller flera tjänster samtidigt.

### <a name="multi-service-resource"></a>Resurs för flera tjänster

>[!WARNING]
> För tillfället stöder dessa tjänster **inte** nycklar med flera tjänster: QnA Maker, tal tjänster, Custom Vision och avvikelse detektor.

Prenumererar på en Cognitive Services-resurs med flera tjänster:
* Gör att du kan använda en enda Azure-resurs för de flesta Azure-Cognitive Services.
* Konsoliderar faktureringen från de tjänster som du använder. Mer information finns i [Cognitive Services prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/) .

### <a name="single-service-resource"></a>Resurs för enskild tjänst

Resurser med en tjänst (till exempel Visuellt innehåll eller tal tjänster) är begränsade till den angivna tjänsten.