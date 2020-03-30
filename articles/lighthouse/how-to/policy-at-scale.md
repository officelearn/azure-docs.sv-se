---
title: Distribuera Azure-princip till delegerade prenumerationer i stor skala
description: Lär dig hur Azure-delegerad resurshantering gör att du kan distribuera en principdefinition och principtilldelning över flera klienter.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 9e061995b728e2864d1bd33a32d530634ab794d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456854"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Distribuera Azure-princip till delegerade prenumerationer i stor skala

Som tjänsteleverantör kan du ha inbyggda flera kundklienter för Azure-delegerad resurshantering. [Azure Lighthouse](../overview.md) gör det möjligt för tjänsteleverantörer att utföra åtgärder i stor skala över flera klienter samtidigt, vilket gör hanteringsuppgifter mer effektiva.

Det här avsnittet visar hur du använder [Azure Policy](../../governance/policy/index.yml) för att distribuera en principdefinition och principtilldelning över flera klienter med PowerShell-kommandon. I det här exemplet säkerställer principdefinitionen att lagringskonton skyddas genom att endast tillåta HTTPS-trafik.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Använda Azure Resource Graph för att fråga över kundklienter

Du kan använda [Azure Resource Graph](../../governance/resource-graph/index.yml) för att fråga över alla prenumerationer i kundklienter som du hanterar. I det här exemplet identifierar vi alla lagringskonton i dessa prenumerationer som för närvarande inte kräver HTTPS-trafik.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Distribuera en princip för flera kundklienter

Exemplet nedan visar hur du använder en [Azure Resource Manager-mall](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json) för att distribuera en principdefinition och principtilldelning över delegerade prenumerationer i flera kundklienter. Den här principdefinitionen kräver att alla lagringskonton använder HTTPS-trafik, vilket förhindrar att nya lagringskonton skapas som inte följer och markerar befintliga lagringskonton utan inställningen som icke-kompatibel.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Verifiera principdistributionen

När du har distribuerat Azure Resource Manager-mallen kan du bekräfta att principdefinitionen har tillämpats genom att försöka skapa ett **lagringskonto med EnableHttpsTrafficOnly** inställt på **false** i en av dina delegerade prenumerationer. På grund av principtilldelningen bör du inte kunna skapa det här lagringskontot.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort principdefinitionen och tilldelningen som skapats av distributionen.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Policy](../../governance/policy/index.yml).
- Lär dig mer om [hanteringsupplevelser mellan klienter.](../concepts/cross-tenant-management-experience.md)
