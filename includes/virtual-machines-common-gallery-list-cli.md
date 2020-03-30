---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ec3ecdafb8e475f5f13372789528612ccd7b8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66226031"
---
## <a name="using-rbac-to-share-images"></a>Använda RBAC för att dela bilder

Du kan dela avbildningar mellan prenumerationer med rollbaserad åtkomstkontroll (RBAC). Alla användare som har läsbehörighet till en avbildningsversion, även mellan prenumerationer, kan distribuera en virtuell dator med avbildningsversionen.

Mer information om hur du delar resurser med RBAC finns i [Hantera åtkomst med RBAC och Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Lista information

Hämta plats, status och annan information om tillgängliga bildgallerier med hjälp av [az sig-listan](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Lista bilddefinitionerna i ett galleri, inklusive information om OS-typ och status, med hjälp av [az sig-bilddefinitionslista](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Lista de delade bildversionerna i ett galleri med hjälp av [az sig bildversionslista](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Hämta ID för en bildversion med [az sig bildversion show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```