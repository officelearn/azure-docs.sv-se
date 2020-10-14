---
title: Skapa en hanterad disk från en avbildnings version
description: Skapa en hanterad disk från en avbildnings version i ett delat avbildnings Galleri.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: bf4a1feb91a1ac4b0bca0d6afdbac41a8be3aa4f
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049880"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>Skapa en hanterad disk från en avbildnings version

Om du behöver kan du göra en hanterad disk från en avbildnings version som lagras i ett delat avbildnings Galleri.


## <a name="cli"></a>CLI

Ange `source` variabeln till ID för avbildnings versionen och Använd sedan [AZ disk Create](/cli/azure/disk.md#az_disk_create) för att skapa den hanterade disken. 


Du kan se en lista över avbildnings versioner med hjälp av [AZ sig-avbildnings versions listan](/cli/azure/sig/image-version.md#az_sig_image_version_list). I det här exemplet letar vi efter alla avbildnings versioner som ingår i *myImageDefinition* -bilddefinitionen i galleriet för *gallerier* .

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


I det här exemplet skapar vi en hanterad disk med namnet *myManagedDisk*i regionen *östra* i en resurs grupp med namnet *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

Om disken är en data disk lägger du till `--gallery-image-reference-lun` för att ange LUN.

## <a name="powershell"></a>PowerShell

Du kan visa en lista över alla avbildnings versioner med [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



När du har all information du behöver kan du använda [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) för att hämta den käll avbildnings version som du vill använda och tilldela den till en variabel. I det här exemplet hämtar vi `1.0.0` avbildnings versionen av `myImageDefinition` definitionen i `myGallery` käll galleriet i `myResourceGroup` resurs gruppen.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Konfigurera vissa variabler för disk informationen.

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

Skapa en disk konfiguration och sedan disken med käll avbildningens versions-ID. För `-GalleryImageReference` krävs det bara LUN om källan är en datadisk.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

Skapa disken.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa en avbildnings version från en hanterad disk med hjälp av [Azure CLI](image-version-managed-image-cli.md) eller [PowerShell](image-version-managed-image-powershell.md).


