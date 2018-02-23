---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 87b9bd74fc59c86bf177e45c18bfc4e104d9c996
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
Om du vill lägga till två taggar i en resursgrupp, använder du:

```powershell
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

Anta att du vill lägga till en tredje tagg. Varje gång du tillämpar taggar på en resurs eller resursgrupp skriver du över resursens eller resursgruppens befintliga taggar. Om du vill lägga till en ny etikett utan att förlora befintliga taggar måste du hämta befintliga taggar, lägga till en ny tagg och tillämpa samling taggar:

```powershell
$tags = (Get-AzureRmResourceGroup -Name myResourceGroup).Tags
$tags += @{Project="Documentation"}
Set-AzureRmResourceGroup -Tag $tags -Name myResourceGroup
```

Resurser som ärver inte taggar från resursgruppen. För närvarande resursgruppen har tre taggar, men resurserna som inte har några taggar. Om du vill lägga till alla taggar från en resursgrupp för dess resurser och behålla befintliga taggar för resurser som inte är dubbletter, använder du följande skript:

```powershell
$group = Get-AzureRmResourceGroup myResourceGroup
if ($group.Tags -ne $null) {
    $resources = $group | Find-AzureRmResource
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        foreach ($key in $group.Tags.Keys)
        {
            if (($resourcetags) -AND ($resourcetags.ContainsKey($key))) { $resourcetags.Remove($key) }
        }
        $resourcetags += $group.Tags
        Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
    }
}
```

Du kan också använda taggar från resursgruppen i resurser utan att behålla befintliga taggar:

```powershell
$g = Get-AzureRmResourceGroup -Name myResourceGroup
Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

Om du vill kombinera flera värden i en enskild tagg, använder du en JSON-sträng.

```powershell
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

Om du vill ta bort alla taggar, kan du skicka en tom hash-tabell.

```powershell
Set-AzureRmResourceGroup -Name myResourceGroup -Tag @{ }
```
