---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 8a40f5f98117290a0c5afa53e965ba5e31628055
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624542"
---
|Namn |Beskrivning |Effekt(er) |Version |GitHub |
|---|---|---|---|---|
|[Tillåtna platser](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe56962a6-4747-49cd-b67b-bf8b01975c4c) |Med den här principen kan du begränsa vilka platser som organisationen kan ange när den distribuerar resurser. Den används för att genomdriva kraven på geo-efterlevnad. Resursgrupper, Microsoft.AzureActiveDirectory/b2cDirectories och resurser som använder regionen ”global” undantas. |Förneka |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedLocations_Deny.json)
|[Tillåtna platser för resursgrupper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe765b5de-1225-4ba3-bd56-1ac6695af988) |Med den här principen kan du begränsa de platser som din organisation kan skapa resursgrupper i. Den används för att genomdriva kraven på geo-efterlevnad. |Förneka |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json)
|[Tillåtna resurstyper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa08ec900-254a-4555-9bf5-e42af04b5c5c) |Med den här principen kan du ange vilka resurstyper som din organisation kan distribuera. Endast resurstyper som stöder "taggar" och "plats" påverkas av den här principen. Om du vill begränsa alla resurser duplicerar du den här principen och ändrar "läge" till "Alla". |Förneka |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json)
|[Granskningsresursplats matchar resursgruppsplatsen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a914e76-4921-4c19-b460-a2d36003525a) |Granska att resursplatsen matchar dess resursgruppsplats |Revision |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json)
|[Granskningsanvändning av anpassade RBAC-regler](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |Granska inbyggda roller som Ägare, Contributer, Reader i stället för anpassade RBAC-roller, som är felbenägna. Att använda anpassade roller behandlas som ett undantag och kräver en rigorös granskning och hotmodellering |Granskning, inaktiverad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json)
|[Anpassade prenumerationsägare roller bör inte finnas](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Den här principen säkerställer att det inte finns några anpassade prenumerationsägareroller. |Granskning, inaktiverad |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json)
|[Ej tillåtna resurstyper](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6c112d4e-5bc7-47ae-a041-ea2d9dccd749) |Med den här principen kan du ange vilka resurstyper som organisationen inte kan distribuera. |Neka |1.0.0 |[Länk](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json)
