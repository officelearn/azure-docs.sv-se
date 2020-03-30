---
title: Efterlevnad med Azure-principen
description: Tilldela inbyggda principer i Azure Policy för granskning av efterlevnad av dina Azure-behållarregister
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330744"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Granska efterlevnad av Azure-behållarregister med Azure Policy

[Azure Policy](../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal.

I den här artikeln introduceras inbyggda principer (förhandsversion) för Azure Container Registry. Använd dessa principer för att granska nya och befintliga register för efterlevnad.

Det finns inga avgifter för att använda Azure Policy.

> [!IMPORTANT]
> Den här funktionen är för närvarande en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="built-in-policy-definitions"></a>Inbyggda principdefinitioner

Följande inbyggda principdefinitioner är specifika för Azure Container Registry:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Se även den inbyggda [nätverksprincipdefinitionen: [Preview] Container Registry bör använda en slutpunkt](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)för virtuella nätverkstjänster .

## <a name="assign-policies"></a>Tilldela principer

* Tilldela principer med [Azure-portalen,](../governance/policy/assign-policy-portal.md) [Azure CLI](../governance/policy/assign-policy-azurecli.md), en [Resource Manager-mall](../governance/policy/assign-policy-template.md)eller Azure Policy SDK:er.
* Begränsa en principtilldelning till en resursgrupp, en prenumeration eller en [Azure-hanteringsgrupp](../governance/management-groups/overview.md). Behållarregisterprinciptilldelningar gäller för befintliga och nya behållarregister i omfånget.
* Aktivera eller inaktivera [principkontroll](../governance/policy/concepts/assignment-structure.md#enforcement-mode) när som helst.

> [!NOTE]
> När du har tilldelat eller uppdaterat en princip tar det lite tid innan tilldelningen tillämpas på resurser i det definierade scopet. Se information om [utlösare av principutvärdering](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Granska efterlevnad av policyer

Få tillgång till efterlevnadsinformation som genereras av dina principtilldelningar med hjälp av Azure-portalen, Azure-kommandoradsverktygen eller Azure Policy-SDK:erna. Mer information finns i [Hämta efterlevnadsdata för Azure-resurser](../governance/policy/how-to/get-compliance-data.md).

När en resurs inte är kompatibel finns det många möjliga orsaker. Information om orsaken eller hur du hittar den ansvariga ändringen finns i [Fastställa bristande efterlevnad](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Regelefterlevnad i portalen:

1. Välj **Alla tjänster**och sök efter **Princip**.
1. Välj **Efterlevnad**.
1. Använd filtren för att begränsa efterlevnadstillstånd eller för att söka efter principer ![Principefterlevnad i portalen](./media/container-registry-azure-policy/azure-policy-compliance.png).
1. Välj en princip för att granska samlad efterlevnadsinformation och händelser. Om du vill väljer du ett specifikt register för resursefterlevnad.

### <a name="policy-compliance-in-the-azure-cli"></a>Principefterlevnad i Azure CLI

Du kan också använda Azure CLI för att hämta efterlevnadsdata. Använd till exempel kommandot [az-principtilldelningslista](/cli/azure/policy/assignment#az-policy-assignment-list) i CLI för att hämta princip-ID:n för azure container-registerprinciper som tillämpas:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Exempel på utdata:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Kör sedan [az-principtillståndslistan](/cli/azure/policy/state#az-policy-state-list) för att returnera det JSON-formaterade efterlevnadstillståndet för alla resurser under ett visst princip-ID:

```azurecli
az policy state list \
  --resource <policyID>
```

Eller kör [az principtillståndslista](/cli/azure/policy/state#az-policy-state-list) för att returnera JSON-formaterade efterlevnadstillståndet för en viss registerresurs, till exempel *mitt register:*

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [definitioner](../governance/policy/concepts/definition-structure.md) och effekter för [Azure-princip](../governance/policy/concepts/effects.md)

* Skapa en [anpassad principdefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

* Läs mer om [styrningsfunktioner](../governance/index.yml) i Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/