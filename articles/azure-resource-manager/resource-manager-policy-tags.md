---
title: "Principer för Azure-resurs för taggar | Microsoft Docs"
description: "Ger exempel på resursprinciper för hantering av taggar för resurser"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: tomfitz
ms.openlocfilehash: 469bd8d637337e5900ea84c6bfaf88064695fb7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="apply-resource-policies-for-tags"></a>Tillämpa principer för företagsresurser för taggar

Det här avsnittet innehåller gemensamma regler som du kan använda för att säkerställa konsekvent användning av taggar för resurser.

Tillämpa en princip för taggen på en resursgrupp eller prenumeration med befintliga resurser inte applicera principen till dessa resurser. Om du vill tillämpa principer på dessa resurser, Utlös en uppdatering med befintliga resurser. Den här artikeln innehåller ett PowerShell-exempel för att utlösa en uppdatering.

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>Kontrollera att alla resurser i en resursgrupp har ett Taggvärde

Ett vanligt krav är att alla resurser i en resursgrupp har en viss tagg och värde. Det här kravet behövs ofta för att spåra kostnader per avdelning. Följande villkor uppfyllas:

* Det obligatoriska taggen och värdet läggs till nya och uppdaterade resurser som inte har taggen.
* Den obligatoriska taggen och värde kan inte tas bort från alla befintliga resurser.

Du kan göra det här kravet genom att använda två inbyggda principer till en resursgrupp.

| ID | Beskrivning |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | Gäller obligatoriska taggen med sitt standardvärde när det inte anges av användaren. |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | Tillämpar en nödvändig tagg och dess värde. |

### <a name="powershell"></a>PowerShell

Följande PowerShell-skript tilldelar två inbyggda principdefinitioner till en resursgrupp. Tilldela alla taggar som krävs innan du kör skriptet till resursgruppen. Varje tagg på resursgruppens namn måste anges för resurser i gruppen. Om du vill tilldela alla resursgrupper i prenumerationen inte ger den `-Name` parameter när du hämtar resursgrupper.

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

Du kan utlösa en uppdatering till alla befintliga resurser för att genomdriva tagg-principer som du har lagt till efter att principerna. Följande skript behåller andra taggar som fanns på resurser:

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="require-tags-for-a-resource-type"></a>Kräv taggar för en resurstyp
I följande exempel visar hur du kapsla logiska operatorer för att kräva en program-tagg för endast en viss resurstyp (i det här fallet, storage-konton).

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>Kräv tagg
Följande princip nekar förfrågningar som inte har en tagg som innehåller ”costCenter” nyckel (valfritt värde kan tillämpas):

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="next-steps"></a>Nästa steg
* När du definierar en regel (som visas i föregående exempel) behöver du skapar principdefinitionen och kopplar den till ett omfång. Omfattningen kan vara en prenumeration, resursgrupp eller resurs. Om du vill tilldela principer via portalen finns [Använd Azure-portalen för att tilldela och hantera resursprinciper](resource-manager-policy-portal.md). Om du vill tilldela principer via REST-API, PowerShell eller Azure CLI, se [tilldela och hantera principer via skript](resource-manager-policy-create-assign.md).
* En introduktion till resursprinciper finns [resurs uppgifter](resource-manager-policy.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).

