---
title: Snabbstart – Använda PowerShell för att skapa en principtilldelning som identifierar icke-kompatibla resurser i Azure-miljön | Microsoft Docs
description: I den här snabbstarten använder du PowerShell för att skapa en Azure Policy-tilldelning som identifierar icke-kompatibla resurser.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 3/14/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 9f7d32d3d1208b6fe6075f7dacdd6d350aee03e2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Snabbstart: Skapa en principtilldelning som identifierar icke-kompatibla resurser med i Azure RM PowerShell-modulen

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status. I den här snabbstarten skapar du en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar. När du är klar identifierar du virtuella datorer som är *icke-kompatibla* med principtilldelningen.

AzureRM PowerShell-modulen används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här guiden förklaras hur du använder AzureRM för att skapa en principtilldelning. Principen identifierar icke-kompatibla resurser i Azure-miljön.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Innan du börjar bör du kontrollera att den senaste versionen av PowerShell har installerats. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Uppdatera din AzureRM PowerShell-modul till den senaste versionen. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

I den här snabbstarten skapar du en principtilldelning och tilldelar definitionen *Granska virtuella datorer utan Managed Disks*. Den här principdefinitionen ska identifiera resurser som inte uppfyller villkoren i principdefinitionen.

Skapa en ny principtilldelning genom att köra följande kommandon:

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"

$definition = Get-AzureRmPolicyDefinition -Name "Audit Virtual Machines without Managed Disks"

New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}

```

Föregående kommandon använder följande information:

- **Namn** – Visningsnamn för principtilldelningen. I det här fallet använder du *tilldelningen Granska virtuella datorer utan Managed Disks*.
- **Definition** – Principdefinitionen som du använder som bas för att skapa tilldelningen. I det här fallet är principdefinitionen *Granska virtuella datorer utan Managed Disks*.
- **Omfång** – Ett omfång avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper. Kom ihåg att ersätta &lt;omfång&gt; med namnet på din resursgrupp.
- **Sku** – Det här kommandot skapar en principtilldelning med standardnivån. Med standardnivån kan du få skalenlig hantering, utvärdering av efterlevnad och reparation. Standardnivån är kostnadsfri för närvarande. I framtiden kommer standardnivån att vara avgiftsbelagd. När prissättningsändringen sker meddelas det, och mer information finns i [ priserna för Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).


Du är nu redo att identifiera icke-kompatibla resurser för att förstå miljöns kompatibilitetsstatus.

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Använd följande information för att identifiera resurser som inte är kompatibla med principtilldelningen som du skapade. Kör följande kommandon:

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

Mer information om principtilldelnings-ID finns i [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Kör sedan följande kommando för att hämta resurs-ID:n för de icke-kompatibla resurserna som matas ut till en JSON-fil:

```powershell
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```
Ditt resultat liknar följande exempel:


```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
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

Efterföljande guider i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta arbeta med andra självstudier ska du inte rensa resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta tar du bort tilldelningen som du skapade genom att köra det här kommandot:

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du tilldelat en principdefinition för att identifiera icke-kompatibla resurser i Azure-miljön.

Om du vill lära dig mer om att tilldela principer och se till att **framtida** resurser som skapas är kompatibla kan du gå vidare till självstudien för:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./create-manage-policy.md)
