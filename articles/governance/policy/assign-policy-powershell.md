---
title: 'Snabbstart: Nytt principtilldelning med PowerShell'
description: I den här snabbstarten använder du Azure PowerShell för att skapa en Azure Policy-tilldelning för att identifiera icke-kompatibla resurser.
ms.date: 11/25/2019
ms.topic: quickstart
ms.openlocfilehash: 3fd6060d1f38c523ccf22e80807f6220bfdf3cbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75978296"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Snabbstart: Skapa en principtilldelning för att identifiera icke-kompatibla resurser med Azure PowerShell

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar identifierar du virtuella datorer som inte är _kompatibla_.

Azure PowerShell-modulen används för att hantera Azure-resurser från kommandoraden eller i skript.
Den här guiden förklarar hur du använder Az-modulen för att skapa en principtilldelning.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Innan du börjar bör du kontrollera att den senaste versionen av Azure PowerShell har installerats. Detaljerad information finns i [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

- Registrera azure policy insights-resursleverantören med Azure PowerShell. När du registrerar resursprovidern säkerställer du att din prenumeration fungerar med den. Om du vill registrera en resursprovider måste du ha behörighet att utföra åtgärden att registrera resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Mer information om hur du registrerar och visar resursleverantörer finns i [Resursleverantörer och typer](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning för _virtuella gransknings-datorer utan definition av hanterade diskar._ Den här principdefinitionen identifierar virtuella datorer som inte använder hanterade diskar.

Skapa en ny principtilldelning genom att köra följande kommandon:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Föregående kommandon använder följande information:

- **Namn** – det faktiska namnet på tilldelningen. I det här exemplet användes _audit-vm-manageddisks_.
- **Visningsnamn** – Visningsnamn för principtilldelningen. I det här fallet använder du _tilldelningen Granska virtuella datorer utan Managed Disks_.
- **Definition** – Principdefinitionen som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionens ID _Granska virtuella datorer som inte använder hanterade diskar_.
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå efterlevnadstillståndet för din miljö.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Använd följande information för att identifiera resurser som inte är kompatibla med principtilldelningen som du skapade. Kör följande kommandon:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Mer information om hur du hämtar principtillstånd finns i [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Ditt resultat liknar följande exempel:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Resultaten matchar vad du ser på fliken **Resursefterlevnad** för en principtilldelning i Azure-portalvyn.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den skapade tilldelningen använder du följande kommando:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)