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
ms.openlocfilehash: 91889971e1ab8a9ea8341f6bc57735d973ea0e89
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125096"
---
## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="preview-register-the-feature"></a>Förhandsversion: Registrera funktionen

Delade Image Galleries är i förhandsversion, men du behöver registrera funktionen innan du kan använda den. Så här registrerar funktionen delad Image Galleries:

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>Hämta hanterad avbildning

Du kan se en lista med avbildningar som är tillgängliga i en resurs med [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). När du vet avbildningens namn och vilken resursgrupp det är i, du kan använda `Get-AzImage` igen för att hämta objektet för avbildning och lagra den i en variabel för senare användning. Det här exemplet hämtar en avbildning med namnet *myImage* från resursgruppen ”myResourceGroup” och tilldelar den till variabeln *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Skapa ett avbildningsgalleri 

Ett galleri med avbildningar är den primära resursen som används för att aktivera delning av avbildningen. Galleriet namn måste vara unikt inom prenumerationen. Skapa en avbildning galleriet med hjälp av [New AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). I följande exempel skapas ett galleri med namnet *myGallery* i den *myGalleryRG* resursgrupp.

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

Skapar galleriet bilden definition med [New AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). I det här exemplet heter galleriet bilden *myGalleryImage*.

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
### <a name="using-publisher-offer-and-sku"></a>Med hjälp av utgivare, erbjudande och SKU 
För kunder som planerar att implementera delade avbildningar **i en kommande version**, du kommer att kunna använda ditt personligt definierade **-utgivare**, **-erbjuder** och **- Sku** värden för att hitta och ange en definition av bild och sedan skapa en virtuell dator med senaste versionsnumret för avbildningen från den matchande bild definition. Här är till exempel tre definitioner som avbildning och deras värden:

|Bilddefinition|Utgivare|Erbjudande|Sku|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Testning|standardOffer|testSku|

Alla tre av dessa ha unika värden. Du kan ha bild-versioner som delar en eller två, men inte alla tre värden. **I en kommande version**, du kommer att kunna kombinera dessa värden för att begära den senaste versionen av en viss avbildning. **Detta fungerar inte i den aktuella versionen**, men kommer att vara tillgängliga i framtiden. När den släpps, med följande syntax ska användas för att ange Källavbildningen som *myImage1* från tabellen ovan.

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Detta liknar hur du kan för närvarande ange Använd utgivare, erbjudande och SKU för [Azure Marketplace-avbildningar](../articles/virtual-machines/windows/cli-ps-findimage.md) att hämta den senaste versionen av en Marketplace-avbildning. Med detta i åtanke måste varje avbildningsdefinitionen ha en unik uppsättning dessa värden.  

## <a name="create-an-image-version"></a>Skapa en Bildversion

Skapa en Avbildningsversion från en hanterad avbildning med hjälp av [New AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) . I det här exemplet är versionsnumret för avbildningen *1.0.0* och den replikeras till både *USA, västra centrala* och *södra centrala USA* datacenter.


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

