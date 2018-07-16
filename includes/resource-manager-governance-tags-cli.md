---
title: ta med fil
description: ta med fil
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b9484336add0719749e9f0af56bdd70fa3906ef5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38752667"
---
Om du vill lägga till två taggar till en resursgrupp använder du kommandot [az-gruppuppdatering](/cli/azure/group#az_group_update):

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Vi utgår från att du vill lägga till en tredje tagg. Kör kommandot igen med den nya taggen. Den läggs till de befintliga taggarna.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Resurser ärver inte taggar från resursgruppen. För närvarande har resursgruppen tre taggar, men resurserna har inte några taggar. Om du vill tillämpa alla taggar från en resursgrupp på dess resurser, och behålla någon av de befintliga taggarna för resurserna, kan du använda följande skript:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Du kan även tillämpa taggar från resursgruppen till resurser utan att behålla befintliga taggar:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Om du vill kombinera flera värden i en enda tagg använder du en JSON-sträng.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Om du vill ta bort alla taggar från en resursgrupp använder du:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
