---
title: Skapa en princip för att identifiera icke-kompatibla resurser med Azure PowerShell
description: Använda Azure PowerShell för att skapa en Azure Policy-tilldelning som identifierar icke-kompatibla resurser.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 32fe811c80fd34b4ea3390a3f46a1d36aba7534e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310716"
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Skapa en policytilldelning som identifierar icke-kompatibla resurser med hjälp av Azure PowerShell

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar identifierar du virtuella datorer som är *icke-kompatibla* med principtilldelningen.

AzureRM PowerShell-modulen används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här guiden förklaras hur du använder AzureRM för att skapa en principtilldelning. Principen identifierar icke-kompatibla resurser i Azure-miljön.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Installera [ARMClient](https://github.com/projectkudu/ARMClient), om du inte redan gjort det. Det är ett verktyg som skickar HTTP-begäranden till Azure Resource Manager-baserade API:er.
- Innan du börjar bör du kontrollera att den senaste versionen av PowerShell har installerats. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Uppdatera din AzureRM PowerShell-modul till den senaste versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).
- Registrera resursprovidern Policy Insights med hjälp av Azure PowerShell. När du registrerar resursprovidern säkerställer du att din prenumeration fungerar med den. Om du vill registrera en resursprovider måste du ha behörighet att utföra åtgärden att registrera resursprovider. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Läs mer om att registrera och visa resursprovidrar i [(Resursprovidrar och typer)](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar definitionen *Granska virtuella datorer utan hanterade diskar*. Den här principdefinitionen ska identifiera resurser som inte uppfyller villkoren i principdefinitionen.

Skapa en ny principtilldelning genom att köra följande kommandon:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Föregående kommandon använder följande information:

- **Namn** – det faktiska namnet på tilldelningen.  I det här exemplet användes *audit-vm-manageddisks*.
- **Visningsnamn** – Visningsnamn för principtilldelningen. I det här fallet använder du *tilldelningen Granska virtuella datorer utan hanterade diskar*.
- **Definition** – Principdefinitionen som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionens ID *Granska virtuella datorer som inte använder hanterade diskar*.
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp.

Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetsstatus.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Använd följande information för att identifiera resurser som inte är kompatibla med principtilldelningen som du skapade. Kör följande kommandon:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Mer information om principtilldelnings-ID:n finns i [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Kör sedan följande kommando för att hämta resurs-ID:n för de icke-kompatibla resurserna som matas ut till en JSON-fil:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Resultatet är jämförbart med det du vanligtvis skulle se under **Icke-kompatibla resurser** i Azure-portalens vy.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den skapade tilldelningen använder du följande kommando:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudiekursen för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)