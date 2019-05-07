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
ms.openlocfilehash: 5d4be0bf52fd925e22e40e98258082304a25a111
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148757"
---
## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="get-the-managed-image"></a>Hämta hanterad avbildning

Du kan se en lista med avbildningar som är tillgängliga i en resurs med [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). När du vet avbildningens namn och vilken resursgrupp det är i, du kan använda `Get-AzImage` igen för att hämta objektet för avbildning och lagra den i en variabel för senare användning. Det här exemplet hämtar en avbildning med namnet *myImage* från resursgruppen ”myResourceGroup” och tilldelar den till variabeln *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Skapa ett avbildningsgalleri 

Ett galleri med avbildningar är den primära resursen som används för att aktivera delning av avbildningen. Tillåtna tecken för namn på galleriet är versaler, gemener, siffror, punkter och punkter. Namn på galleriet får inte innehålla bindestreck. Galleriet namn måste vara unikt inom prenumerationen. 

Skapa en avbildning galleriet med hjälp av [New AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). I följande exempel skapas ett galleri med namnet *myGallery* i den *myGalleryRG* resursgrupp.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Skapa en definition för avbildning 

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om vilka avbildningsversioner som skapas i dem. Namn på definition bilder kan bestå av versaler, gemener, siffror, punkter, bindestreck och punkter. Läs mer om de värden som du kan ange för en avbildningsdefinitionen [bild definitioner](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Skapar avbildning definition med [New AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). I det här exemplet heter galleriet bilden *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Skapa en Bildversion

Skapa en Avbildningsversion från en hanterad avbildning med hjälp av [New AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Tillåtna tecken för Avbildningsversion är siffror och punkter. Måste vara ett nummer i intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *LägreVersion*. *Patch*.

I det här exemplet är versionsnumret för avbildningen *1.0.0* och den replikeras till både *USA, västra centrala* och *södra centrala USA* datacenter. När du väljer målregioner för replikering, komma ihåg att du även inkludera den *källa* region som mål för replikering.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Det kan ta en stund att replikera avbildningen till alla målregioner, så vi har skapat ett jobb så att vi kan spåra förloppet. Om du vill se förloppet för jobbet, Skriv `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Du måste vänta tills Avbildningsversion helt Slutför som bygger och replikeras innan du kan använda samma avbildning som hanterad för att skapa en annan Avbildningsversion.