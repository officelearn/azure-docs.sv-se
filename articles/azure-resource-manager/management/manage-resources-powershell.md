---
title: Hantera resurser - Azure PowerShell
description: Använd Azure PowerShell och Azure Resource Manager för att hantera dina resurser. Visar hur du distribuerar och tar bort resurser.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: de6f24ea66ef41b5ee4bfdda5948de9639f10a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485408"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Hantera Azure-resurser med hjälp av Azure PowerShell

Lär dig hur du använder Azure PowerShell med [Azure Resource Manager](overview.md) för att hantera dina Azure-resurser. Information om hur du hanterar resursgrupper finns i [Hantera Azure-resursgrupper med hjälp av Azure PowerShell](manage-resource-groups-powershell.md).

Andra artiklar om hantering av resurser:

- [Hantera Azure-resurser med hjälp av Azure-portalen](manage-resources-portal.md)
- [Hantera Azure-resurser med hjälp av Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Du kan distribuera Azure-resurser direkt med hjälp av Azure PowerShell eller distribuera en Resource Manager-mall för att skapa Azure-resurser.

### <a name="deploy-a-resource"></a>Distribuera en resurs

Följande skript skapar ett lagringskonto.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Distribuera en mall

Följande skript skapar distribuera en Snabbstartsmall för att skapa ett lagringskonto. Mer information finns i [Snabbstart: Skapa Azure Resource Manager-mallar med hjälp av Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser till gruppen. Mer information finns i [Skapa resursgrupp och distribuera resurser](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuera resurser till flera prenumerationer eller resursgrupper

Vanligtvis distribuerar du alla resurser i mallen till en enda resursgrupp. Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper eller prenumerationer. Mer information finns i [Distribuera Azure-resurser till flera prenumerationer eller resursgrupper](../templates/cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Ta bort resurser

Följande skript visar hur du tar bort ett lagringskonto.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Mer information om hur Azure Resource Manager beställer borttagning av resurser finns i [Azure Resource Manager-resursgruppborttagning](delete-resource-group.md).

## <a name="move-resources"></a>Flytta resurser

Följande skript visar hur du tar bort ett lagringskonto från en resursgrupp till en annan resursgrupp.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Lås resurser

Låsning hindrar andra användare i organisationen från att oavsiktligt ta bort eller ändra kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser ett lagringskonto så att kontot inte kan tas bort.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Följande skript hämtar alla lås för ett lagringskonto:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Följande skript tar bort ett lås för ett lagringskonto:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Mer information finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggning hjälper till att organisera resursgruppen och resurserna logiskt. Information finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md#powershell).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) är det sätt som du hanterar åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](overview.md).
- Information om hur du lär dig mallsyntaxen för Resurshanteraren finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../templates/template-syntax.md).
- Mer information om hur du utvecklar mallar finns i [steg-för-steg-självstudier](/azure/azure-resource-manager/).
- Information om hur du visar mallscheman för Azure Resource Manager finns i [mallreferens](/azure/templates/).
