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
ms.topic: article
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 9521a9be1d2bdc91836830dbb6471d2ffcb4daaf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Hantera resurser med Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure.

## <a name="understand-scope"></a>Förstå omfång

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

I den här artikeln gäller du alla inställningar till en resursgrupp så du kan enkelt ta bort dessa inställningar när du är klar.

Nu ska vi skapa resursgruppen.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Resursgruppen är för närvarande är tom.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Tilldela en roll

I den här artikeln får distribuera du en virtuell dator och dess relaterade virtuella nätverk. Det finns tre resursspecifika roller som ger vanligtvis behövs åtkomst för att hantera virtuella lösningar:

* [Virtual Machine-deltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Nätverksdeltagare](../role-based-access-control/built-in-roles.md#network-contributor)
* [Storage-konto deltagare](../role-based-access-control/built-in-roles.md#storage-account-contributor)

I stället för att tilldela roller till enskilda användare, är det ofta lättare att [skapa en Azure Active Directory-grupp](../active-directory/active-directory-groups-create-azure-portal.md) för användare som behöver vidta liknande åtgärder. Tilldela sedan den gruppen till rätt roll. För att förenkla den här artikeln kan skapa du en Azure Active Directory-grupp utan medlemmar. Du kan fortfarande tilldela den här gruppen till en roll för ett omfång. 

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

Normalt du upprepa processen för **Network-deltagare** och **Storage-konto deltagare** Kontrollera användare tilldelas Hantera distribuerade resurser. Du kan hoppa över de här stegen i den här artikeln.

## <a name="azure-policies"></a>Principer för Azure

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Tillämpa principer

Din prenumeration redan har flera principdefinitioner av. Tillgängliga principdefinitioner, Använd:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Du kan visa befintliga principdefinitioner. Typ av princip är antingen **BuiltIn** eller **anpassad**. Titta igenom definitionerna för de som beskriver ett villkor som du vill tilldela. I den här artikeln får tilldela du principer som:

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

Du har tilldelat roller och principer, så att du är redo att distribuera lösningar. Standardstorleken är Standard_DS1_v2 som är en av dina tillåtna SKU: er. När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

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

När distributionen är klar kan tillämpa du inställningar för flera till lösningen.

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

Den virtuella datorn kan bara tas bort om du uttryckligen tar bort låset. Steget visas i [Rensa resurser](#clean-up-resources).

## <a name="tag-resources"></a>Taggen resurser

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Taggen resurser

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

Du kan använda de returnerade värdena för hanteringsuppgifter som stoppas alla virtuella datorer med ett taggvärde.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visa kostnader av taggvärden

Du kan visa kostnaderna för resurser med taggarna när taggar till resurser. Det tar ett tag för kostnadsanalys att visa den senaste användningen, så du inte kan visa kostnaderna ännu. När kostnader, kan du visa kostnader för resurser över resursgrupper i din prenumeration. Användarna måste ha [prenumeration åtkomst till faktureringsinformationen](../billing/billing-manage-access.md) att visa kostnaderna.

Om du vill visa kostnaderna efter tagg i portal, väljer din prenumeration och välj **kostnaden Analysis**.

![Kostnadsanalys](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Filtrera efter Taggvärdet sedan och välj **tillämpa**.

![Visa kostnad per tagg](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Du kan också använda den [Azure Billing API: erna](../billing/billing-usage-rate-card-overview.md) programmässigt visa kostnaderna.

## <a name="clean-up-resources"></a>Rensa resurser

Låst nätverkssäkerhetsgruppen kan inte tas bort förrän låset tas bort. Ta bort låset med:

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
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).
