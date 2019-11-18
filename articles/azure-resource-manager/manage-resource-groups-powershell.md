---
title: Hantera resurs grupper – Azure PowerShell
description: Använd Azure PowerShell för att hantera dina resurs grupper via Azure Resource Manager. Visar hur du skapar, listar och tar bort resurs grupper.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5d0d7394de2763c785640b0981de9017cf0a7ae7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149788"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Hantera Azure Resource Manager resurs grupper med hjälp av Azure PowerShell

Lär dig hur du använder Azure PowerShell med [Azure Resource Manager](resource-group-overview.md) för att hantera Azures resurs grupper. Information om hur du hanterar Azure-resurser finns i [Hantera Azure-resurser med hjälp av Azure PowerShell](./manage-resources-powershell.md).

Andra artiklar om att hantera resurs grupper:

- [Hantera Azure-resurs grupper med hjälp av Azure Portal](./manage-resources-portal.md)
- [Hantera Azure-resurs grupper med hjälp av Azure CLI](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Vad är en resurs grupp?

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Lägg vanligt vis till resurser som delar samma livs cykel i samma resurs grupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resurs gruppen anger du var metadata lagras.

## <a name="create-resource-groups"></a>Skapa resurs grupper

Följande PowerShell-skript skapar en resurs grupp och visar sedan resurs gruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Lista resurs grupper

Följande PowerShell-skript visar resurs grupperna under din prenumeration.

```azurepowershell-interactive
Get-AzResourceGroup
```

Så här hämtar du en resurs grupp:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Ta bort resurs grupper

Följande PowerShell-skript tar bort en resurs grupp:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Mer information om hur Azure Resource Manager beställer borttagningen av resurser finns i [Azure Resource Manager ta bort resurs grupp](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resurs grupp

Se [distribuera resurser till en befintlig resurs grupp](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Information om hur du validerar en resurs grupps distribution finns i [test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resurs grupp och resurser

Du kan skapa en resurs grupp och distribuera resurser till gruppen med hjälp av en Resource Manager-mall. Mer information finns i [skapa resurs grupp och distribuera resurser](./deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen Miss lyckas

Den här funktionen kallas även *återgång vid fel*. Mer information finns i [omdistribuera när distributionen Miss lyckas](./rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resurs grupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resurs grupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](./resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Lås resurs grupper

Låsning förhindrar att andra användare i organisationen oavsiktligt tar bort eller ändrar kritiska resurser, t. ex. Azure-prenumeration, resurs grupp eller resurs. 

Följande skript låser en resurs grupp så att det inte går att ta bort resurs gruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Följande skript hämtar alla Lås för en resurs grupp:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Tagga resurs grupper

Du kan använda taggar för resurs grupper och resurser för att logiskt organisera dina till gångar. Mer information finns i [använda taggar för att ordna dina Azure-resurser](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportera resurs grupper till mallar

När du har konfigurerat resurs gruppen kan du Visa en Resource Manager-mall för resurs gruppen. Att exportera mallen erbjuder två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller den fullständiga infrastrukturen.
- Lär dig mer om mallens syntax genom att titta på den JavaScript Object Notation (JSON) som representerar din lösning.

Om du vill exportera alla resurser i en resurs grupp använder du cmdleten [export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) och anger resurs gruppens namn.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Mallen sparas som en lokal fil.

I stället för att exportera alla resurser i resurs gruppen kan du välja vilka resurser som ska exporteras.

Överför resurs-ID för att exportera en resurs.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Om du vill exportera fler än en resurs skickar du resurs-ID: n i en matris.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

När du exporterar mallen kan du ange om parametrar ska användas i mallen. Som standard ingår parametrar för resurs namn, men de har inget standardvärde. Du måste skicka parameter värde under distributionen.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

I resursen används parametern för namnet.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Om du använder parametern `-IncludeParameterDefaultValue` när du exporterar mallen, innehåller parametern Template ett standardvärde som är inställt på det aktuella värdet. Du kan antingen använda standardvärdet eller skriva över standardvärdet genom att skicka ett annat värde.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Om du använder parametern `-SkipResourceNameParameterization` när du exporterar mallen ingår inte parametrar för resurs namn i mallen. I stället anges resurs namnet direkt på resursen till det aktuella värdet. Du kan inte anpassa namnet under distributionen.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Mer information finns i avsnittet [Exportera en och flera resurser till mallen i Azure Portal](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resurs grupper

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Mer information Azure Resource Manager finns [Azure Resource Manager översikt](./resource-group-overview.md).
- Information om syntaxen för Resource Manager-mallar finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](./resource-group-authoring-templates.md).
- Information om hur du utvecklar mallar finns i de [stegvisa självstudierna](/azure/azure-resource-manager/).
- Om du vill visa scheman för Azure Resource Manager mallar, se [referens för mallar](/azure/templates/).