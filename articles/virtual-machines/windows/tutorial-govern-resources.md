---
title: Självstudier – Hantera virtuella datorer i Azure med Azure PowerShell | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du använder Azure PowerShell för att hantera virtuella Azure-datorer med hjälp av RBAC, principer, lås och taggar
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 42f7d767162f2f403b2cf921e31a38b711a3c773
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38477702"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>Självstudier: Lär dig hur du hanterar virtuella Windows-datorer med Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Exemplen i den här artikeln kräver Azure PowerShell 6.0 eller senare. Om du kör PowerShell lokalt och du inte har version 6.0 eller senare måste du [uppdatera din version](/powershell/azure/install-azurerm-ps). Du måste också köra `Connect-AzureRmAccount` för att upprätta en anslutning till Azure. För lokala installationer måste du även [ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/) för att skapa en ny Azure Active Directory-grupp.

## <a name="understand-scope"></a>Förstå omfång

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

I den här självstudiekursen tillämpar du alla hanteringsinställningar på en resursgrupp, så att du enkelt kan ta bort inställningarna när du är klar.

Nu ska vi skapa resursgruppen.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Resursgruppen är tom för närvarande.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Du vill kontrollera att användarna i din organisation har rätt åtkomstnivå till dessa resurser. Du vill inte bevilja obegränsad åtkomst till användare, men du måste också försäkra dig om att de kan utföra sitt arbete. Med [rollbaserad åtkomstkontroll](../../role-based-access-control/overview.md) kan du hantera vilka användare som har behörighet att utföra specifika åtgärder i ett omfång.

För att kunna skapa och ta bort rolltilldelningar måste användare ha `Microsoft.Authorization/roleAssignments/*`-åtkomst. Den här åtkomsten beviljas via rollerna Ägare eller Administratör för användaråtkomst.

För hanteringen av VM-lösningar finns det tre resursspecifika roller som beviljar åtkomst på lämplig nivå:

* [Virtuell datordeltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Nätverksdeltagare](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Lagringskontodeltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

I stället för att tilldela roller till enskilda användare är det ofta lättare att [skapa en Azure Active Directory-grupp](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) för användare som behöver utföra liknande åtgärder. Därefter tilldelar du gruppen lämplig roll. För att förenkla informationen i den här artikeln skapar vi en Azure Active Directory-grupp utan medlemmar. Du kan fortfarande tilldela den här gruppen en roll för ett omfång. 

I följande exempel skapas en Azure Active Directory-grupp med namnet *VMDemoContributors* med smeknamnet *vmDemoGroup* för e-post. Smeknamnet för e-post fungerar som ett alias för gruppen.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

När du har kört kommandot tar det en stund för gruppen att distribueras i Azure Active Directory. När du har väntat 20–30 sekunder kör du kommandot [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) för att tilldela den nya Azure Active Directory-gruppen till rollen Virtuell datordeltagare för resursgruppen.  Om du kör följande kommando innan gruppen har distribuerats får du ett felmeddelande som anger att **huvudkontot<guid> inte finns i katalogen**. Prova att köra kommandot igen.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Normalt upprepar du processen för *Nätverksdeltagare* och *Lagringskontodeltagare* för att se till att hanteringen av alla distribuerade resurser tilldelas till användare. Du kan hoppa över dessa steg i den här artikeln.

## <a name="azure-policies"></a>Azure-principer

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Tillämpa principer

Din prenumeration har redan flera principdefinitioner. Du kan visa de tillgängliga principdefinitionerna genom att köra kommandot [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition):

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

De befintliga principdefinitionerna visas. Principtypen är antingen **BuiltIn** eller **Custom**. Titta igenom definitionerna och leta efter sådana som beskriver ett tillstånd som du vill tilldela. I den här artikeln tilldelar du principer som:

* Begränsar platserna för alla resurser.
* Begränsa SKU:erna för virtuella datorer.
* Granskar virtuella datorer som inte använder hanterade diskar.

I exemplet nedan hämtar du tre principdefinitioner baserat på visningsnamn. Du använder kommandot [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) för att tilldela dessa definitioner till resursgruppen. För vissa principer anger du parametervärden för att ange de tillåtna värdena.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
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

[Resurslås](../../azure-resource-manager/resource-group-lock-resources.md) förhindrar att användare i din organisation tar bort eller ändrar viktiga resurser av misstag. Till skillnad från rollbaserad åtkomstkontroll tillämpar resurslås en begränsning för alla användare och roller. Du kan ange låssnivån till *CanNotDelete* eller *ReadOnly*.

Om du vill låsa den virtuella datorn och nätverkssäkerhetsgruppen använder du kommandot [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Om du vill testa låsen provar du att köra följande kommando:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Ett felmeddelande visas som anger att borttagningsåtgärden inte kan utföras på grund av ett lås. Resursgruppen kan bara tas bort om du tar bort låsen. Det steget beskrivs i [Rensa resurser](#clean-up-resources).

## <a name="tag-resources"></a>Tagga resurser

Du kan ordna Azure-resurser i kategorier genom att lägga till [taggar](../../azure-resource-manager/resource-group-using-tags.md). Varje tagg består av ett namn och ett värde. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Om du vill lägga till taggar för en virtuell dator använder du kommandot [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Hitta resurser efter tagg

Om du vill söka efter resurser med ett taggnamn och taggvärde använder du kommandot [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource):

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

Du kan använda de returnerade värdena för olika hanteringsuppgifter, t.ex. för att stoppa alla virtuella datorer med ett visst taggvärde.

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visa kostnader efter taggvärden

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Den låsta nätverkssäkerhetsgruppen kan inte tas bort förrän låset tagits bort. Om du vill ta bort låset använder du kommandot [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock):

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

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Tilldela användare en roll
> * Tillämpa principer som verkställer standarder
> * Skydda viktiga resurser med lås
> * Tagga resurser för fakturering och hantering

Gå vidare till nästa självstudie om du vill lära dig mer om virtuella datorer med hög tillgänglighet.

> [!div class="nextstepaction"]
> [Övervaka virtuella datorer](tutorial-monitoring.md)

