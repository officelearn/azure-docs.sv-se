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
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187306"
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

Om du vill uppdatera beskrivningen av ett galleri använder du [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Det här exemplet visar hur du använder [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) för att uppdatera uttjänta datum för vår bilddefinition.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

I det här exemplet visas hur du använder [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) för att utesluta den här avbildningsversionen från att användas som den *senaste* avbildningen.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Rensa resurser

När du tar bort resurser måste du börja med det sista objektet i de kapslade resurserna - avbildningsversionen. När versioner har tagits bort kan du ta bort bilddefinitionen. Du kan inte ta bort galleriet förrän alla resurser under det har tagits bort.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

