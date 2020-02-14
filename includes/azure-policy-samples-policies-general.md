---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 9054340799ac6f95a9a3064351193f0680a000c9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193095"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Tillåtna platser](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Med den här principen kan du begränsa vilka platser som organisationen kan ange när den distribuerar resurser. Den används för att genomdriva kraven på geo-efterlevnad. Resursgrupper, Microsoft.AzureActiveDirectory/b2cDirectories och resurser som använder regionen ”global” undantas. |autentiseringsregel |1.0.0 |
|[Tillåtna platser för resurs grupper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Med den här principen kan du begränsa vilka platser som organisationen kan skapa resurs grupper i. Den används för att genomdriva kraven på geo-efterlevnad. |autentiseringsregel |1.0.0 |
|[Tillåtna resurstyper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Med den här principen kan du ange vilka resurs typer som din organisation kan distribuera. Endast resurs typer som stöder ' taggar ' och ' Location ' kommer att påverkas av den här principen. Om du vill begränsa alla resurser duplicerar du principen och ändrar "mode" till "alla". |autentiseringsregel |1.0.0 |
|[Granska resurs plats matchar resurs gruppens plats](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Granska att resurs platsen matchar resurs gruppens plats |händelse |1.0.0 |
|[Granska användningen av anpassade RBAC-regler](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Granska inbyggda roller som "ägare, Contribute", läsare "i stället för anpassade RBAC-roller som är fel känsliga. Användning av anpassade roller behandlas som ett undantag och kräver en rigorös gransknings-och hot modellering |Granskning, inaktive rad |1.0.0 |
|[Roller för anpassade prenumerationer får inte finnas](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Den här principen säkerställer att det inte finns några anpassade prenumerations ägare roller. |Granskning, inaktive rad |1.0.0 |
|[Otillåtna resurstyper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Med den här principen kan du ange de resurs typer som din organisation inte kan distribuera. |Neka |1.0.0 |
