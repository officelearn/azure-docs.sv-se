---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: ad1527a5e7f1cb2ff1beb9ddace5460f41bb8a87
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461522"
---
## <a name="azure-cognitive-service-subscription-types"></a>Typer av Azure Cognitive Service-prenumerationer

> [!NOTE]
> Prenumerationsägare kan inaktivera skapandet av Cognitive Services-konton för resursgrupper och prenumerationer genom att använda [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), att tilldela en principdefinition för ”tillåts inte resurstyper” och ange **Microsoft.CognitiveServices/accounts** som resource måltypen.

Du kan komma åt Azure Cognitive Services via två olika prenumerationer: En prenumeration som flera tjänster eller en enskild tjänst en. Dessa prenumerationer kan du ansluta till en enskild tjänst eller flera tjänster på samma gång.

### <a name="multi-service-subscription"></a>Flera tjänster prenumeration

>[!WARNING]
> De här tjänsterna för närvarande **inte** stöd för flera tjänster nycklar: QnA Maker Speech Services, Custom Vision och Avvikelseidentifiering detektor.

En prenumeration som har flera tjänster för Azure Cognitive Services kan du använda en enda prenumeration och Azure-resurs för de flesta av Azure Cognitive Services och konsoliderar fakturering från de tjänster du använder. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) för ytterligare information.

### <a name="single-service-subscription"></a>En tjänst-prenumeration

En prenumeration på en enda tjänst, till exempel visuellt innehåll eller Speech Services. En prenumeration för en tjänst är begränsad till den här resursen. 
