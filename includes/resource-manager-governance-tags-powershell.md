---
title: inkludera fil
description: inkludera fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 069baf627c0230b6a4727c375494352ab3e6a803
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187277"
---
Om du vill lägga till två Taggar i en resurs grupp använder du kommandot [set-AzResourceGroup](/powershell/module/az.resources/set-azresourcegroup) :

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

Vi utgår från att du vill lägga till en tredje tagg. Varje gång du tillämpar taggar på en resurs eller resursgrupp skriver du över resursens eller resursgruppens befintliga taggar. Om du vill lägga till en ny tagg utan att förlora befintliga taggar måste du hämta de befintliga taggarna, lägga till en ny tagg och sedan tillämpa samlingen av taggar igen:

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzResourceGroup -Name myResourceGroup).Tags
$tags.Add("Project", "Documentation")

# Reapply the updated set of tags 
Set-AzResourceGroup -Tag $tags -Name myResourceGroup
```

Resurser ärver inte taggar från resursgruppen. För närvarande har resursgruppen tre taggar, men resurserna har inte några taggar. Om du vill tillämpa alla taggar från en resursgrupp på dess resurser och behålla de befintliga taggarna på resurser som inte är dubbletter så kan du använda följande skript:

```azurepowershell-interactive
# Get the resource group
$group = Get-AzResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        
        # If the resource has existing tags, add new ones
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }

            # Reapply the updated tags to the resource 
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Du kan även tillämpa taggar från resursgruppen till resurser utan att behålla befintliga taggar:

```azurepowershell-interactive
# Get the resource group
$g = Get-AzResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

Om du vill kombinera flera värden i en enda tagg använder du en JSON-sträng.

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

Om du vill lägga till en ny tagg med flera värden utan att förlora de befintliga taggarna måste du hämta de befintliga taggarna, använda en JSON-sträng för den nya taggen och applicera om samlingen med Taggar:

```azurepowershell-interactive
# Get existing tags and add a new tag
$ResourceGroup = Get-AzResourceGroup -Name myResourceGroup
$Tags = $ResourceGroup.Tags
$Tags.Add("CostCenter", "{`"Dept`":`"IT`",`"Environment`":`"Test`"}")

# Reapply the updated set of tags
$ResourceGroup | Set-AzResourceGroup -Tag $Tags
```

Om du vill ta bort alla taggar skickar du en tom hash-tabell.

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ }
```
