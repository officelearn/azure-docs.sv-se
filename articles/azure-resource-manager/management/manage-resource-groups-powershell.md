---
title: Hantera resursgrupper – Azure PowerShell
description: Använd Azure PowerShell för att hantera dina resursgrupper via Azure Resource Manager. Visar hur du skapar, listar och tar bort resursgrupper.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248364"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Hantera Azure Resource Manager-resursgrupper med hjälp av Azure PowerShell

Lär dig hur du använder Azure PowerShell med [Azure Resource Manager](overview.md) för att hantera dina Azure-resursgrupper. Information om hantering av Azure-resurser finns i [Hantera Azure-resurser med hjälp av Azure PowerShell](manage-resources-powershell.md).

Andra artiklar om hantering av resursgrupper:

- [Hantera Azure-resursgrupper med hjälp av Azure-portalen](manage-resources-portal.md)
- [Hantera Azure-resursgrupper med hjälp av Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Vad är en resursgrupp

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. Lägg i allmänhet till resurser som delar samma livscykel i samma resursgrupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du var metadata lagras.

## <a name="create-resource-groups"></a>Skapa resursgrupper

Följande PowerShell-skript skapar en resursgrupp och visar sedan resursgruppen.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Lista resursgrupper

I följande PowerShell-skript visas resursgrupperna under din prenumeration.

```azurepowershell-interactive
Get-AzResourceGroup
```

Så här hämtar du en resursgrupp:

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

Mer information om hur Azure Resource Manager beställer borttagning av resurser finns i [Azure Resource Manager-resursgruppborttagning](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Se [Distribuera resurser till en befintlig resursgrupp](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Information om hur du validerar en resursgruppsdistribution finns i [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser till gruppen med hjälp av en Resource Manager-mall. Mer information finns i [Skapa resursgrupp och distribuera resurser](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen misslyckas

Den här funktionen kallas även *Återställning vid fel*. Mer information finns i [Omfördela när distributionen misslyckas](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resursgrupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resursgrupp. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Låsa resursgrupper

Låsning hindrar andra användare i organisationen från att oavsiktligt ta bort eller ändra kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser en resursgrupp så att resursgruppen inte kan tas bort.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Följande skript hämtar alla lås för en resursgrupp:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Mer information finns i [Låsa resurser med Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Tagga resursgrupper

Du kan använda taggar på resursgrupper och resurser för att logiskt ordna dina resurser. Information finns i [Använda taggar för att ordna dina Azure-resurser](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportera resursgrupper till mallar

När du har konfigurerat resursgruppen kan du visa en Resource Manager-mall för resursgruppen. Att exportera mallen ger två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller den fullständiga infrastrukturen.
- Lär dig mallsyntaxen genom att titta på Den JavaScript-objekt notation (JSON) som representerar din lösning.

Om du vill exportera alla resurser i en resursgrupp använder du cmdleten [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) och anger resursgruppsnamnet.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Mallen sparas som en lokal fil.

I stället för att exportera alla resurser i resursgruppen kan du välja vilka resurser som ska exporteras.

Om du vill exportera en resurs skickar du det resurs-ID:et.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Om du vill exportera mer än en resurs skickar du resurs-ID:erna i en matris.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

När du exporterar mallen kan du ange om parametrar ska användas i mallen. Som standard inkluderas parametrar för resursnamn, men de har inget standardvärde. Du måste skicka parametervärdet under distributionen.

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

Om du `-IncludeParameterDefaultValue` använder parametern när du exporterar mallen innehåller mallparametern ett standardvärde som är inställt på det aktuella värdet. Du kan antingen använda standardvärdet eller skriva över standardvärdet genom att skicka in ett annat värde.

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

Om du `-SkipResourceNameParameterization` använder parametern när du exporterar mallen inkluderas inte parametrar för resursnamn i mallen. I stället anges resursnamnet direkt på resursen till dess aktuella värde. Du kan inte anpassa namnet under distributionen.

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

Funktionen för exportmall stöder inte export av Azure Data Factory-resurser. Mer information om hur du kan exportera datafabriksresurser finns i [Kopiera eller klona en datafabrik i Azure Data Factory](https://aka.ms/exportTemplateViaAdf).

Om du vill exportera resurser som skapats via den klassiska distributionsmodellen måste du [migrera dem till resurshanterarens distributionsmodell](https://aka.ms/migrateclassicresourcetoarm).

Mer information finns i [Enkel export och export med flera resurser till mallen i Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resursgrupper

[Rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/overview.md) är det sätt som du hanterar åtkomst till resurser i Azure. Mer information finns i [Hantera åtkomst med RBAC och Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Information om Azure Resource Manager finns i [Översikt över Azure Resource Manager](overview.md).
- Information om hur du lär dig mallsyntaxen för Resurshanteraren finns [i Förstå strukturen och syntaxen för Azure Resource Manager-mallar](../templates/template-syntax.md).
- Mer information om hur du utvecklar mallar finns i [steg-för-steg-självstudier](/azure/azure-resource-manager/).
- Information om hur du visar mallscheman för Azure Resource Manager finns i [mallreferens](/azure/templates/).