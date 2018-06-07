---
title: Hantera Azure lösningar med PowerShell | Microsoft Docs
description: Använda Azure PowerShell och Resource Manager för att hantera dina resurser.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 02616ef566dd576c3f406d4b9f3059dab27bf3e0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603421"
---
# <a name="manage-resources-with-azure-powershell"></a>Hantera resurser med Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt läser du [Installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Om du kör PowerShell lokalt måste du också köra `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="understand-scope"></a>Förstå omfång

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

I den här artikeln gäller du alla inställningar till en resursgrupp så du kan enkelt ta bort dessa inställningar när du är klar.

Nu ska vi skapa resursgruppen.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Resursgruppen är tom för närvarande.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Tilldela en roll

I den här artikeln får distribuera du en virtuell dator och dess relaterade virtuella nätverk. För hanteringen av VM-lösningar finns det tre resursspecifika roller som beviljar åtkomst på lämplig nivå:

* [Virtuell datordeltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor)
* [Lagringskontodeltagare](../role-based-access-control/built-in-roles.md#storage-account-contributor)

I stället för att tilldela roller till enskilda användare är det ofta lättare att [skapa en Azure Active Directory-grupp](../active-directory/active-directory-groups-create-azure-portal.md) för användare som behöver utföra liknande åtgärder. Därefter tilldelar du gruppen lämplig roll. För att förenkla informationen i den här artikeln skapar vi en Azure Active Directory-grupp utan medlemmar. Du kan fortfarande tilldela den här gruppen en roll för ett omfång. 

I följande exempel skapar en grupp och tilldelar deltagarrollen för virtuell dator för resursgruppen. Att köra den `New-AzureAdGroup` kommandot, måste du antingen använda de [Azure Cloud Shell](/azure/cloud-shell/overview) eller [ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Normalt upprepar du processen för **Nätverksdeltagare** och **Lagringskontodeltagare** för att se till att hanteringen av alla distribuerade resurser tilldelas till användare. Du kan hoppa över dessa steg i den här artikeln.

## <a name="azure-policies"></a>Azure-principer

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Tillämpa principer

Din prenumeration har redan flera principdefinitioner. Tillgängliga principdefinitioner, Använd:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

De befintliga principdefinitionerna visas. Principtypen är antingen **BuiltIn** eller **Custom**. Titta igenom definitionerna och leta efter sådana som beskriver ett tillstånd som du vill tilldela. I den här artikeln tilldelar du principer som:

* begränsa platserna för alla resurser
* begränsa SKU: er för virtuella datorer
* gransknings-och virtuella datorer som inte använder hanterade diskar

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Distribuera den virtuella datorn

Nu när du har tilldelat roller och principer är det dags att distribuera lösningen. Standardstorleken är Standard_DS1_v2, som är en av dina tillåtna SKU:er. När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

När distributionen är klar kan du lägga till fler hanteringsinställningar för lösningen.

## <a name="lock-resources"></a>Lås resurser

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Låsa en resurs

Om du vill låsa den virtuella datorn och nätverkssäkerhetsgruppen, använder du:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Den virtuella datorn kan bara tas bort om du uttryckligen tar bort låset. Det steget beskrivs i [Rensa resurser](#clean-up-resources).

## <a name="tag-resources"></a>Tagga resurser

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Tagga resurser

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Om du vill lägga till taggar för en virtuell dator, använder du:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Hitta resurser efter tagg

Om du vill söka efter resurser med taggnamn och värde använder du:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Du kan använda de returnerade värdena för olika hanteringsuppgifter, t.ex. för att stoppa alla virtuella datorer med ett visst taggvärde.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visa kostnader efter taggvärden

Du kan visa kostnaderna för resurser med taggarna när taggar till resurser. Det tar ett tag för kostnadsanalys att visa den senaste användningen, så du inte kan visa kostnaderna ännu. När kostnader, kan du visa kostnader för resurser över resursgrupper i din prenumeration. Användarna måste ha [prenumeration åtkomst till faktureringsinformationen](../billing/billing-manage-access.md) att visa kostnaderna.

Om du vill visa kostnaderna efter tagg i portal, väljer din prenumeration och välj **kostnaden Analysis**.

![Kostnadsanalys](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Filtrera efter Taggvärdet sedan och välj **tillämpa**.

![Visa kostnad per tagg](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Du kan också använda den [Azure Billing API: erna](../billing/billing-usage-rate-card-overview.md) programmässigt visa kostnaderna.

## <a name="clean-up-resources"></a>Rensa resurser

Den låsta nätverkssäkerhetsgruppen kan inte tas bort förrän låset tagits bort. Ta bort låset med:

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

När den inte längre behövs du använda kommandot [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg
* Läs om hur du övervakar dina virtuella datorer i [övervaka och uppdatera en virtuell dator i Windows med Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Mer information om hur du använder Azure Security Center för att implementera rekommenderad säkerhetspraxis [övervaka virtuella säkerhet med hjälp av Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Du kan flytta befintliga resurser till en ny resursgrupp. Exempel finns i [flytta resurser till ny resursgrupp eller prenumeration](resource-group-move-resources.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).
