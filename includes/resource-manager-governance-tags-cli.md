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
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
ms.locfileid: "29532347"
---
Om du vill lägga till två taggar i en resursgrupp, Använd den [az grupp uppdatering](/cli/azure/group#az_group_update) kommando:

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Anta att du vill lägga till en tredje tagg. Kör kommandot igen med den nya taggen. Den läggs till i befintliga taggar.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Resurser som ärver inte taggar från resursgruppen. För närvarande resursgruppen har tre taggar, men resurserna som inte har några taggar. Om du vill lägga till alla taggar från en resursgrupp för dess resurser och behålla befintliga taggar på resurser, använder du följande skript:

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

Du kan också använda taggar från resursgruppen i resurser utan att behålla befintliga taggar:

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

Om du vill kombinera flera värden i en enskild tagg, använder du en JSON-sträng.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Ta bort alla taggar på en resursgrupp med:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
