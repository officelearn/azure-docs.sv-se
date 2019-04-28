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
ms.openlocfilehash: 7fd5b2051f81a5dc34270a608c1518e8a11678b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542477"
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
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Lista över delade bild-versioner i ett galleri med [az sig versionsnumret för avbildningen lista](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

Hämta ID för en bild version med hjälp av [az sig versionsnumret för avbildningen show](/cli/azure/sig/image-version#az-sig-image-version-show).

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```