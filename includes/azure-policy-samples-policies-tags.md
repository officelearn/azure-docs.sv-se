---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 05f9b653910dcbe39b937c711d0045fd3ee33d4e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192647"
---
|Namn |Beskrivning |Påverkan (ar) |Version |
|---|---|---|---|
|[Lägg till en tagg till resurs grupper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |Lägger till den angivna taggen och värdet när en resurs grupp saknar den här taggen skapas eller uppdateras. Befintliga resurs grupper kan åtgärdas genom att utlösa en reparations uppgift. Om taggen finns med ett annat värde kommer den inte att ändras. |Gör |1.0.0 |
|[Lägg till en tagg till resurser](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |Lägger till den angivna taggen och värdet när en resurs som saknar den här taggen har skapats eller uppdaterats. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd. Om taggen finns med ett annat värde kommer den inte att ändras. Taggar för resursgrupper ändras inte. |Gör |1.0.0 |
|[Lägga till eller ersätta en tagg i resurs grupper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |Lägger till eller ersätter den angivna taggen och värdet när en resurs grupp skapas eller uppdateras. Befintliga resurs grupper kan åtgärdas genom att utlösa en reparations uppgift. |Gör |1.0.0 |
|[Lägga till eller ersätta en tagg på resurser](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |Lägger till eller ersätter den angivna taggen och värdet när en resurs skapas eller uppdateras. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd. Taggar för resursgrupper ändras inte. |Gör |1.0.0 |
|[Lägg till tagg och standardvärdet](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |Lägger till den angivna taggen och värdet när en resurs som saknar taggen skapas eller uppdateras. Ändrar inte de resurs etiketter som skapats innan den här principen tillämpades förrän resurserna har ändrats. Gäller inte för resurs grupper. Nya effekter för att ändra-effekter är tillgängliga som stöder reparation av taggar på befintliga resurser (se https://aka.ms/modifydoc). |slå |1.0.0 |
|[Lägg till tagg och dess standardvärde till resurs grupper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |Lägger till den angivna taggen och värdet när en resurs grupp som saknar taggen skapas eller uppdateras. Ändrar inte taggar för resurs grupper som skapats innan den här principen tillämpades förrän dessa resurs grupper har ändrats. Nya effekter för att ändra-effekter är tillgängliga som stöder reparation av taggar på befintliga resurser (se https://aka.ms/modifydoc). |slå |1.0.0 |
|[Lägg till tagg och dess värde från resurs gruppen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |Lägger till den angivna taggen med värdet från resurs gruppen när en resurs som saknar taggen skapas eller uppdateras. Ändrar inte de resurs etiketter som skapats innan den här principen tillämpades förrän resurserna har ändrats. Nya effekter för att ändra-effekter är tillgängliga som stöder reparation av taggar på befintliga resurser (se https://aka.ms/modifydoc). |slå |1.0.0 |
|[Ärv en tagg från resurs gruppen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |Lägger till eller ersätter den angivna taggen och värdet från den överordnade resurs gruppen när en resurs skapas eller uppdateras. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd. |Gör |1.0.0 |
|[Ärv en tagg från resurs gruppen om den saknas](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |Lägger till den angivna taggen med värdet från den överordnade resurs gruppen när en resurs som saknar den här taggen har skapats eller uppdaterats. Befintliga resurser kan åtgärdas genom att utlösa en reparationsåtgärd. Om taggen finns med ett annat värde kommer den inte att ändras. |Gör |1.0.0 |
|[Kräv angiven tagg](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |Framtvingar förekomst av en tagg. Gäller inte för resurs grupper. |autentiseringsregel |1.0.0 |
|[Kräv den angivna taggen för resurs grupper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |Framtvingar förekomst av en tagg i resurs grupper. |autentiseringsregel |1.0.0 |
|[Kräv tagg och dess värde](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |Tvingar fram en obligatorisk tagg och dess värde. Gäller inte för resurs grupper. |autentiseringsregel |1.0.0 |
|[Kräv tagg och dess värde i resurs grupper](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |Tvingar fram en obligatorisk tagg och dess värde i resurs grupper. |autentiseringsregel |1.0.0 |
