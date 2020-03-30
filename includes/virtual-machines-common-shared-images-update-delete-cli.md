---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187355"
---
## <a name="update-resources"></a>Uppdatera resurser

Det finns vissa begränsningar för vad som kan uppdateras. Följande objekt kan uppdateras: 

Delat bildgalleri:
- Beskrivning

Bilddefinition:
- Rekommenderade vCPUs
- Rekommenderat minne
- Beskrivning
- Datum för uttjänt

Bildversion:
- Antal regionala repliker
- Målregioner
- Uteslutning från senaste
- Datum för uttjänt

Om du planerar att lägga till replikområden ska du inte ta bort den källhanterade avbildningen. Källhanterad avbildning behövs för att replikera avbildningsversionen till ytterligare regioner. 

Uppdatera beskrivningen av ett galleri med ([az sig update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Uppdatera beskrivningen av en bilddefinition med [az sig image-definition update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Uppdatera en bildversion för att lägga till en region som ska replikeras till med az [sig image-version update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Den här ändringen tar ett tag när avbildningen replikeras till den nya regionen.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Ta bort resurser

Du måste ta bort resurser i omvänd ordning genom att ta bort bildversionen först. När du har tagit bort alla bildversioner kan du ta bort bilddefinitionen. När du har tagit bort alla bilddefinitioner kan du ta bort galleriet. 

Ta bort en bildversion med [az sig bildversion ta bort](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Ta bort en bilddefinition med [az sig image-definition delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Ta bort ett bildgalleri med [az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
