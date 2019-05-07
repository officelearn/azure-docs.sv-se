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
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191739"
---
## <a name="update-resources"></a>Uppdatera resurser

Det finns vissa begränsningar vad kan uppdateras. Följande objekt kan uppdateras: 

Delade bildgalleriet:
- Beskrivning

definition av avbildningen:
- Rekommenderade virtuella processorer
- Rekommenderat minne
- Beskrivning
- Slutet av liv datum

Avbildningsversion:
- Regionala replikantal
- Målregioner
- Undantag från den senaste
- Slutet av liv datum

Om du planerar att lägga till repliken regioner, ta inte bort hanterade Källavbildningen. Hanterade Källavbildningen krävs för att replikera versionsnumret för avbildningen till ytterligare regioner. 

Uppdatera beskrivningen av ett galleri med [uppdatering AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

Det här exemplet visar hur du använder [uppdatering AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) att uppdatera datum slutet på sin livscykel för vår avbildningsdefinitionen.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

Det här exemplet visar hur du använder [uppdatering AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) att undanta den här versionen av avbildningen från att användas som den *senaste* bild.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```


## <a name="clean-up-resources"></a>Rensa resurser

När du tar bort resurser, måste du starta med sista objektet i kapslade resurser - versionsnumret för avbildningen. När versioner har tagits bort, kan du ta bort avbildningsdefinitionen. Du kan inte ta bort galleriet förrän alla resurser under den har tagits bort.

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

