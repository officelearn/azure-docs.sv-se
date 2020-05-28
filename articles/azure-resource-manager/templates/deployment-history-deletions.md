---
title: Borttagning av distributions historik
description: Beskriver hur Azure Resource Manager automatiskt tar bort distributioner från distributions historiken. Distributioner tas bort när historiken är nära att överskrida gränsen på 800.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: 3e48b2da00986da00f7597cf887aa74f84587710
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122453"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatisk borttagning från distributions historik

Varje gång du distribuerar en mall skrivs information om distributionen till distributions historiken. Varje resurs grupp är begränsad till 800 distributioner i distributions historiken.

Från och med juni 2020 tar Azure Resource Manager automatiskt bort distributioner från historiken när du närmar dig gränsen. Automatisk borttagning är en förändring från tidigare beteende. Tidigare var du tvungen att manuellt ta bort distributioner från distributions historiken för att undvika ett fel.

> [!NOTE]
> Att ta bort en distribution från historiken påverkar inte några av de distribuerade resurserna.

## <a name="when-deployments-are-deleted"></a>När distributioner tas bort

Distributioner tas bara bort från distributions historiken när du närmar dig gränsen på 800. Azure Resource Manager tar bort en liten uppsättning av de äldsta distributionerna för att rensa utrymme för framtida distributioner. Merparten av historiken ändras inte. De äldsta distributionerna tas alltid bort först.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Borttagningar från distributions historik":::

Förutom distributioner utlöser du även borttagningar när du kör [åtgärden vad händer om](template-deploy-what-if.md) eller verifierar en distribution.

När du ger en distribution samma namn som en i historiken återställer du dess plats i historiken. Distributionen flyttas till den senaste platsen i historiken. Du kan också återställa en distributions plats när du återställer [till distributionen](rollback-on-error.md) efter ett fel.

## <a name="opt-out-of-automatic-deletions"></a>Inaktivera automatiska borttagningar

Du kan välja bort automatiska borttagningar från historiken. **Använd bara det här alternativet när du vill hantera distributions historiken själv.** Gränsen på 800-distributioner i historiken tillämpas fortfarande. Om du överskrider 800-distributioner får du ett fel meddelande och distributionen kommer att Miss förfaller.

Registrera funktions flaggan om du vill inaktivera automatiska borttagningar `Microsoft.Resources/DisableDeploymentGrooming` . När du registrerar funktions flaggan kan du välja bort automatiska borttagningar för hela Azure-prenumerationen. Du kan inte välja bara för en viss resurs grupp.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För PowerShell använder du [register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Om du vill se aktuell status för din prenumeration använder du:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [AZ-funktions register](/cli/azure/feature#az-feature-register)för Azure CLI.

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Om du vill se aktuell status för din prenumeration använder du:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Använd [funktioner – registrera](/rest/api/resources/features/register)för REST API.

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Om du vill se aktuell status för din prenumeration använder du:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Nästa steg

* Information om hur du visar distributions historiken finns i [Visa distributions historik med Azure Resource Manager](deployment-history.md).
