---
title: Efterlevnad med Azure Policy
description: Tilldela inbyggda principer i Azure Policy för att granska kompatibiliteten för dina Azure Container register
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: a2bfdc18f4bbf16fe8fa6bcbcba7bab18aedabf1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145009"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Granska kompatibilitet för Azure Container register med hjälp av Azure Policy

[Azure policy](../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal.

I den här artikeln beskrivs inbyggda principer (för hands version) för Azure Container Registry. Använd dessa principer för att granska nya och befintliga register för efterlevnad.

Det kostar inget att använda Azure Policy.

> [!IMPORTANT]
> Den här funktionen finns för närvarande som en förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="built-in-policy-definitions"></a>Inbyggda princip definitioner

Följande inbyggda princip definitioner är bara för Azure Container Registry:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/policy/samples/bycat/policies-container-registry.md)]

Se även den inbyggda nätverks princip definitionen: [för [hands version] container Registry ska använda en tjänst slut punkt för virtuellt nätverk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Tilldela principer

* Tilldela principer med hjälp av [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), en [Resource Manager-mall](../governance/policy/assign-policy-template.md)eller Azure policy SDK: er.
* Begränsa en princip tilldelning till en resurs grupp, en prenumeration eller en [Azure-hanteringsserver](../governance/management-groups/overview.md). Princip tilldelningar för container registret gäller befintliga och nya behållar register i omfånget.
* Aktivera eller inaktivera [princip tillämpning](../governance/policy/concepts/assignment-structure.md#enforcement-mode) när som helst.

> [!NOTE]
> När du har tilldelat eller uppdaterat en princip tar det lite tid för tilldelningen att tillämpas på resurser i det definierade omfånget. Se information om [princip utvärderings utlösare](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Granska efterlevnad av policy

Åtkomst till kompatibilitetsinformation som genereras av dina princip tilldelningar med hjälp av Azure Portal, Azures kommando rads verktyg eller Azure Policy SDK: er. Mer information finns i [Hämta kompatibilitetstillstånd för Azure-resurser](../governance/policy/how-to/get-compliance-data.md).

När en resurs är icke-kompatibel finns det många möjliga orsaker. Information om hur du avgör orsaken eller hur du hittar den ansvarige ändrings funktionen finns i [bestämma inkompatibilitet](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Efterlevnadsprincip i portalen:

1. Välj **alla tjänster**och Sök efter **princip**.
1. Välj **efterlevnad**.
1. Använd filtren för att begränsa kompatibilitetstillstånd eller för att söka efter policy ![efterlevnad för principer i](./media/container-registry-azure-policy/azure-policy-compliance.png)portalen.
1. Välj en princip för att granska sammanställd information om efterlevnad och händelser. Om du vill kan du välja ett visst register för resursens efterlevnad.

### <a name="policy-compliance-in-the-azure-cli"></a>Efterlevnad av principer i Azure CLI

Du kan också använda Azure CLI för att hämta efterlevnads data. Använd exempelvis kommandot [AZ policy Assignment List](/cli/azure/policy/assignment#az-policy-assignment-list) i CLI för att hämta princip-ID: n för de Azure Container Registry principer som tillämpas:

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

Kör sedan [AZ policy State List](/cli/azure/policy/state#az-policy-state-list) för att returnera det JSON-formaterade kompatibilitetstillstånd för alla resurser under ett angivet princip-ID:

```azurecli
az policy state list \
  --resource <policyID>
```

Eller kör [AZ policy State List](/cli/azure/policy/state#az-policy-state-list) för att returnera det JSON-formaterade kompatibilitetstillstånd för en speciell register resurs, till exempel *registret*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att Azure Policy [definitioner](../governance/policy/concepts/definition-structure.md) och [effekter](../governance/policy/concepts/effects.md)

* Skapa en [anpassad princip definition](../governance/policy/tutorials/create-custom-policy-definition.md)

* Lär dig mer om [styrnings funktioner](../governance/index.yml) i Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/