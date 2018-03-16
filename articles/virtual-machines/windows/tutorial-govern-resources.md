---
title: Styr virtuella Azure-datorer med Azure PowerShell | Microsoft Docs
description: "Självstudiekurs – hantera virtuella Azure-datorer genom att använda RBAC, principer, lås och taggar med Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 9fbe9318e52f8299c3ef46f73c3be177de6d4a0c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="virtual-machine-governance-with-azure-powershell"></a>Virtual machine-styrning med Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Om du väljer att installera och använda PowerShell lokalt, se [installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Om du kör PowerShell lokalt måste du också köra `Login-AzureRmAccount` för att skapa en anslutning till Azure. Om lokala installationer måste du också [ladda ned Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureAD/) att skapa en ny Azure Active Directory-grupp.

## <a name="understand-scope"></a>Förstå omfång

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

I den här kursen gäller du alla inställningar till en resursgrupp så du kan enkelt ta bort dessa inställningar när du är klar.

Nu ska vi skapa resursgruppen.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Resursgruppen är för närvarande är tom.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Du vill kontrollera att användare i din organisation har rätt nivå av åtkomst till dessa resurser. Du vill inte bevilja obegränsad åtkomst till användare, men du måste också kontrollera att de kan utföra sitt arbete. [Rollbaserad åtkomstkontroll](../../active-directory/role-based-access-control-what-is.md) kan du hantera vilka användare som har behörighet att utföra specifika åtgärder på ett scope.

Om du vill skapa och ta bort rolltilldelningar, måste användarna ha `Microsoft.Authorization/roleAssignments/*` åtkomst. Den här komma åt via rollerna ägare eller administratör för användaråtkomst.

Det finns tre resursspecifika roller som ger vanligtvis behövs åtkomst för att hantera virtuella lösningar:

* [Virtual Machine-deltagare](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Nätverksdeltagare](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Storage-konto deltagare](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

I stället för att tilldela roller till enskilda användare, är det ofta lättare att [skapa en Azure Active Directory-grupp](../../active-directory/active-directory-groups-create-azure-portal.md) för användare som behöver vidta liknande åtgärder. Tilldela sedan den gruppen till rätt roll. För att förenkla den här artikeln kan skapa du en Azure Active Directory-grupp utan medlemmar. Du kan fortfarande tilldela den här gruppen till en roll för ett omfång. 

I följande exempel skapas en Azure Active Directory-grupp med namnet *VMDemoContributors* med ett smeknamn för e-post för *vmDemoGroup*. Smeknamn för e-post fungerar som ett alias för gruppen.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

Det tar en stund när Kommandotolken returnerar för gruppen att spridas i Azure Active Directory. Vänta 20 eller 30 sekunder, Använd den [ny AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) kommando för att tilldela den nya Azure Active Directory-gruppen till den virtuella datorn deltagarrollen för resursgruppen.  Om du kör följande kommando innan den har spridits, du får ett felmeddelande om **huvudsakliga <guid> finns inte i katalogen**. Försök köra kommandot igen.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Normalt du upprepa processen för *Network-deltagare* och *Storage-konto deltagare* Kontrollera användare tilldelas Hantera distribuerade resurser. Du kan hoppa över de här stegen i den här artikeln.

## <a name="azure-policies"></a>Principer för Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Tillämpa principer

Din prenumeration redan har flera principdefinitioner av. Tillgängliga principdefinitioner, Använd den [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) kommando:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Du kan visa befintliga principdefinitioner. Typ av princip är antingen **BuiltIn** eller **anpassad**. Titta igenom definitionerna för de som beskriver ett villkor som du vill tilldela. I den här artikeln får tilldela du principer som:

* Begränsa platserna för alla resurser.
* Begränsa SKU: er för virtuella datorer.
* Granska virtuella datorer som inte använder hanterade diskar.

I exemplet nedan kan du hämta tre principdefinitioner baserat på namn. Du använder den [ny AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) kommando för att tilldela dessa definitioner till resursgruppen. För vissa principer kan ange du parametervärden för att ange de tillåtna värdena.

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

[Resurslås](../../azure-resource-manager/resource-group-lock-resources.md) förhindra användare i din organisation av misstag tas bort eller ändra viktiga resurser. Till skillnad från rollbaserad åtkomstkontroll gäller resurslås en begränsning för alla användare och roller. Du kan ange låset för *CanNotDelete* eller *ReadOnly*.

Om du vill låsa den virtuella datorn och en säkerhetsgrupp för nätverk använder den [ny AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock) kommando:

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

Om du vill testa Lås försök att köra följande kommando:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Du ser ett felmeddelande om att ta borttagningsåtgärden inte kan utföras på grund av ett lås. Resursgruppen kan bara tas bort om du uttryckligen ta bort lås. Steget visas i [Rensa resurser](#clean-up-resources).

## <a name="tag-resources"></a>Taggen resurser

Du använder [taggar](../../azure-resource-manager/resource-group-using-tags.md) till Azure-resurser och organisera dem logiskt efter kategorier. Varje tagg består av ett namn och ett värde. Du kan till exempel använda namnet ”Miljö” och värdet ”Produktion” för alla resurser i produktionsmiljön.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Om du vill lägga till taggar för en virtuell dator, Använd den [Set AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) kommando:

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Hitta resurser efter tagg

Hitta resurser med taggnamn och värde i [hitta AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) kommando:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Du kan använda de returnerade värdena för hanteringsuppgifter som stoppas alla virtuella datorer med ett taggvärde.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Visa kostnader av taggvärden

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Låst nätverkssäkerhetsgruppen kan inte tas bort förrän låset tas bort. Ta bort låset med den [ta bort AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock) kommando:

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
> * Tilldela användare till en roll
> * Tillämpa principer som verkställer standarder
> * Skydda viktiga resurser med lås
> * Tagga resurser för fakturering och hantering

Gå vidare till nästa kurs att lära dig hur högtillgängliga virtuella datorer.

> [!div class="nextstepaction"]
> [Övervaka virtuella datorer](tutorial-monitoring.md)

