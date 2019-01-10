---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3ec5b9c6357f0d075ddd9b0fd5c8a88ee2846209
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192314"
---
## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="preview-register-the-feature"></a>Förhandsversion: Registrera funktionen

Delade Image Galleries är i förhandsversion, men du behöver registrera funktionen innan du kan använda den. Så här registrerar funktionen delad Image Galleries:

```azurepowershell-interactive
Register-AzureRmProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>Hämta hanterad avbildning

Du kan se en lista med avbildningar som är tillgängliga i en resurs med [Get-AzureRmImage](/powershell/module/AzureRM.Compute/get-azurermimage). När du vet avbildningens namn och vilken resursgrupp det är i, du kan använda `Get-AzureRmImage` igen för att hämta objektet för avbildning och lagra den i en variabel för senare användning. Det här exemplet hämtar en avbildning med namnet *myImage* från resursgruppen ”myResourceGroup” och tilldelar den till variabeln *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzureRmImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Skapa ett avbildningsgalleri 

Ett galleri med avbildningar är den primära resursen som används för att aktivera delning av avbildningen. Galleriet namn måste vara unikt inom prenumerationen. Skapa en avbildning galleriet med hjälp av [New AzureRmGallery](/powershell/module/AzureRM.Compute/new-azurermgallery). I följande exempel skapas ett galleri med namnet *myGallery* i den *myGalleryRG* resursgrupp.

```azurepowershell-interactive
$resourceGroup = New-AzureRMResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzureRmGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Skapa en definition för avbildning 

Skapar galleriet bilden definition med [New AzureRmGalleryImageDefinition](/powershell/module/azurerm.compute/new-azurermgalleryimageversion). I det här exemplet heter galleriet bilden *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzureRmGalleryImageDefinition `
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

I en kommande version kommer du att kunna använda ditt personligt definierade **-utgivare**, **-erbjuder** och **- Sku** värden för att hitta och ange en avbildningsdefinitionen sedan skapa en virtuell dator med senaste versionsnumret för avbildningen från den matchande avbildningsdefinitionen. Här är till exempel tre definitioner som avbildning och deras värden:

|Bilddefinition|Utgivare|Erbjudande|Sku|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Testning|standardOffer|testSku|

Alla tre av dessa ha unika värden. I en kommande version kommer du att kunna kombinera dessa värden för att begära den senaste versionen av en viss avbildning. 

```powershell
# The following should set the source image as myImage1 from the table above
$vmConfig = Set-AzureRmVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Detta liknar hur du för närvarande kan ange dessa för [Azure Marketplace-avbildningar](../articles/virtual-machines/windows/cli-ps-findimage.md) skapa en virtuell dator. Med detta i åtanke måste varje avbildningsdefinitionen ha en unik uppsättning dessa värden. Du kan ha bild-versioner som delar en eller två, men inte alla tre värden. 

##<a name="create-an-image-version"></a>Skapa en Bildversion

Skapa en Avbildningsversion från en hanterad avbildning med hjälp av [New AzureRmGalleryImageVersion](/powershell/module/AzureRM.Compute/new-azurermgalleryimageversion) . I det här exemplet är versionsnumret för avbildningen *1.0.0* och den replikeras till både *USA, västra centrala* och *södra centrala USA* datacenter.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzureRmGalleryImageVersion `
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

