---
title: Distribuera Azure Policy till delegerade prenumerationer i stor skala
description: Lär dig hur Azure delegerad resurs hantering gör det möjligt att distribuera en princip definition och princip tilldelning över flera klienter.
ms.date: 11/8/2019
ms.topic: overview
ms.openlocfilehash: fd335e77feb26241d573db48c2e96c725f70d031
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131284"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Distribuera Azure Policy till delegerade prenumerationer i stor skala

Som tjänst leverantör kan du ha registrerat flera kund innehavare för Azure-delegerad resurs hantering. Med [Azure Lighthouse](../overview.md) kan tjänst leverantörer utföra åtgärder i skala över flera klienter samtidigt, vilket gör hanterings uppgifter mer effektiva.

Det här avsnittet visar hur du använder [Azure policy](https://docs.microsoft.com/azure/governance/policy/) för att distribuera en princip definition och princip tilldelning över flera klienter med PowerShell-kommandon. I det här exemplet säkerställer princip definitionen att lagrings konton skyddas genom att endast tillåta HTTPS-trafik.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Använd Azure Resource Graph för att fråga mellan kund klienter

Du kan använda [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/) för att fråga över alla prenumerationer i de kund innehavare som du hanterar. I det här exemplet kommer vi att identifiera eventuella lagrings konton i dessa prenumerationer som inte kräver HTTPS-trafik.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Distribuera en princip över flera kund klienter

Exemplet nedan visar hur du använder en [Azure Resource Manager-mall](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-https-storage/enforceHttpsStorage.json) för att distribuera en princip definition och princip tilldelning mellan delegerade prenumerationer i flera kund klienter. Den här princip definitionen kräver att alla lagrings konton använder HTTPS-trafik, vilket förhindrar att nya lagrings konton skapas som inte uppfyller och markerar befintliga lagrings konton utan inställningen som icke-kompatibel.

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

## <a name="validate-the-policy-deployment"></a>Verifiera princip distributionen

När du har distribuerat Azure Resource Manager-mallen kan du bekräfta att princip definitionen har tillämpats genom att försöka skapa ett lagrings konto med **EnableHttpsTrafficOnly** inställt på **false** i en av dina delegerade prenumerationer. På grund av princip tilldelningen bör du inte skapa det här lagrings kontot.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort princip definitionen och tilldelningen som skapats av distributionen.

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

- Läs mer om [Azure policy](https://docs.microsoft.com/azure/governance/policy/).
- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).
