---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 213fec9e7d9da56d34f79fee7e677b0e6bbd7a63
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303960"
---
## <a name="update-resources"></a>Uppdatera resurser

Det finns vissa begränsningar för vad som kan uppdateras. Följande objekt kan uppdateras: 

Galleri för delad avbildning:
- Beskrivning

Bild definition:
- Rekommenderad virtuella processorer
- Rekommenderat minne
- Beskrivning
- Datum för slut på livs längd

Avbildnings version:
- Antal regionala repliker
- Mål regioner
- Undantag från senaste
- Datum för slut på livs längd

Om du planerar att lägga till replik regioner ska du inte ta bort den hanterade käll avbildningen. Den käll hanterade avbildningen krävs för att replikera avbildnings versionen till ytterligare regioner. 

Uppdatera beskrivningen av ett galleri med hjälp av ([AZ sig-uppdatera](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Uppdatera beskrivningen av en avbildnings definition med hjälp av [AZ sig-avbildnings definitions uppdatering](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Uppdatera en avbildnings version för att lägga till en region att replikera till med hjälp av [AZ sig-avbildning-version Update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Den här ändringen tar en stund medan avbildningen replikeras till den nya regionen.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Det här exemplet illustrerar hur du använder [AZ sig-avbildning – versions uppdatering](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) för att utesluta den här avbildnings versionen från att användas som den *senaste* avbildningen.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

I det här exemplet visas hur du använder [AZ sig-avbildning – versions uppdatering](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update) för att inkludera den här avbildnings versionen i den *senaste* avbildningen.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Ta bort resurser

Du måste ta bort resurser i omvänd ordning genom att först ta bort avbildnings versionen. När du har tagit bort alla avbildnings versioner kan du ta bort avbildnings definitionen. När du har tagit bort alla avbildnings definitioner kan du ta bort galleriet. 

Ta bort en avbildnings version med hjälp av [AZ sig sig-version Delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Ta bort en avbildnings definition med hjälp av [AZ sig sig](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Ta bort ett bild galleri med [AZ sig-Delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
