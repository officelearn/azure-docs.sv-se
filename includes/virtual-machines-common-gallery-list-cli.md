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
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226031"
---
## <a name="using-rbac-to-share-images"></a>Använder RBAC för att dela bilder

Du kan dela bilder över prenumerationer med hjälp av rollbaserad åtkomstkontroll (RBAC). Alla användare som har läsbehörighet till en Bildversion, även över prenumerationer, kommer att kunna distribuera en virtuell dator med versionsnumret för avbildningen.

Läs mer om hur du delar resurser med hjälp av RBAC [hantera åtkomst med RBAC och Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Lista information

Hämta platsen, status och annan information om tillgängliga bild gallerierna med [az sig lista](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Lista bild definitioner i ett galleri, inklusive information om OS-typ och status, med hjälp av [az sig bild-definitionslista](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Lista över delade bild-versioner i ett galleri med [az sig versionsnumret för avbildningen lista](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Hämta ID för en bild version med hjälp av [az sig versionsnumret för avbildningen show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```