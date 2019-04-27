---
title: Hantera Azure Resource Manager-grupper med hjälp av Azure PowerShell | Microsoft Docs
description: Använda Azure PowerShell för att hantera dina Azure Resource Manager-grupper.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 8ae86d8bc7914a7a9c41eee93bb16b2f774993b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550503"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Hantera resursgrupper i Azure Resource Manager med hjälp av Azure PowerShell

Lär dig hur du använder Azure PowerShell med [Azure Resource Manager](resource-group-overview.md) att hantera dina Azure-resursgrupper. Hantera Azure-resurser finns i [hantera Azure-resurser med hjälp av Azure PowerShell](./manage-resources-powershell.md).

Andra artiklar om hur du hanterar resursgrupper:

- [Hantera Azure-resursgrupper med hjälp av Azure-portalen](./manage-resources-portal.md)
- [Hantera Azure-resursgrupper med hjälp av Azure CLI](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Vad är ett resursgrupp

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. I allmänhet ska du lägga till resurser som delar samma livscykel i samma resursgrupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du var metadatan ska lagras.

## <a name="create-resource-groups"></a>Skapa resursgrupper

Följande PowerShell-skript skapar en resursgrupp och sedan visar resursgruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Lista resursgrupper

Följande PowerShell-skript visar resursgrupper i din prenumeration.

```azurepowershell-interactive
Get-AzResourceGroup
```

Hämta en resursgrupp:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

Följande PowerShell-skript tar bort en resursgrupp:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Mer information om hur Azure Resource Manager beställningar borttagningen av resurser finns i [borttagning av Azure Resource Manager resource](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Se [distribuera resurser till en befintlig resursgrupp](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

För att verifiera en distribution av resursgrupper kan se [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser i gruppen med hjälp av Resource Manager-mall. Mer information finns i [skapa resursgrupp och distribuera resurser](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen misslyckas

Den här funktionen kallas även *återställning vid fel*. Mer information finns i [omdistribuera när distributionen misslyckas](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resursgrupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resursgrupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](./resource-group-move-resources.md#move-resources).

## <a name="lock-resource-groups"></a>Lås resursgrupper

Låsning förhindrar andra användare i din organisation av misstag tar bort eller ändrar kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser en resursgrupp så resursgruppen inte kan tas bort.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Följande skript hämtar alla Lås för en resursgrupp:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Resursgrupper för taggen

Du kan lägga till taggar till resursgrupper och resurser och organisera dem logiskt dina tillgångar. Mer information finns i [med taggar för att organisera Azure-resurser](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportera resursgrupper till mallar

När du har skapat en resursgrupp kan du visa Resource Manager-mallen för resursgruppen. Exportera mallen erbjuder två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller alla hela infrastrukturen.
- Lär dig mer om mallsyntaxen genom att titta på den JavaScript Object Notation (JSON) som representerar din lösning.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Mer information finns i [Export resursgrupp](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resursgrupper

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [hantera åtkomst med RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Läs Azure Resource Manager i [översikt över Azure Resource Manager](./resource-group-overview.md).
- Läs Resource Manager-mallens syntax i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](./resource-group-authoring-templates.md).
- Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).
- Mallscheman för Azure Resource Manager-finns [mallreferensen](/azure/templates/).