---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241217"
---
## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/powershell](https://shell.azure.com/powershell)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="get-the-managed-image"></a>Hämta den hanterade bilden

Du kan se en lista över bilder som är tillgängliga i en resursgrupp med [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). När du väl känner till bildnamnet och vilken `Get-AzImage` resursgrupp det finns i kan du använda igen för att hämta bildobjektet och lagra det i en variabel som ska användas senare. Det här exemplet hämtar en bild med namnet *myImage* från resursgruppen "myResourceGroup" och tilldelar den till variabeln *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Skapa ett bildgalleri 

Ett bildgalleri är den primära resurs som används för att aktivera bilddelning. Tillåtna tecken för Gallerinamn är versaler eller gemener, siffror, punkter och punkter. Gallerinamnet får inte innehålla streck. Gallerinamn måste vara unika i din prenumeration. 

Skapa ett bildgalleri med [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). I följande exempel skapas ett galleri med namnet *myGallery* i resursgruppen *myGalleryRG.*

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
   
## <a name="create-an-image-definition"></a>Skapa en bilddefinition 

Bilddefinitioner skapar en logisk gruppering för bilder. De används för att hantera information om de bildversioner som skapas i dem. Bilddefinitionsnamn kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. Mer information om de värden du kan ange för en bilddefinition finns i [Bilddefinitioner](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Skapa bilddefinitionen med [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). I det här exemplet heter galleribilden *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Skapa en bildversion

Skapa en bildversion från en hanterad avbildning med [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Tillåtna tecken för bildversion är siffror och punkter. Talen måste ligga inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Patch*.

I det här exemplet är avbildningsversionen *1.0.0* och den replikeras till både datacenter i *Västra centrala USA* och södra centrala *USA.* När du väljer målområden för replikering bör du komma ihåg att du också måste inkludera *källregionen* som ett mål för replikering.


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

Det kan ta ett tag att replikera avbildningen till alla målregioner, så vi har skapat ett jobb så att vi kan spåra förloppet. Om du vill se förloppet för jobbet skriver du `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Du måste vänta tills avbildningsversionen helt har byggts och replikerats innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion. 
>
> Du kan också lagra avbildningsversionen `-StorageAccountType Standard_ZRS` i Zon redundant [lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) genom att lägga till när du skapar avbildningsversionen.
>


## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar åtkomst på bildgallerinivå. Använd en [e-postadress och cmdlet Get-AzADUser](/powershell/module/az.resources/get-azaduser) för att hämta objekt-ID:n för användaren och använd sedan [Ny-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att ge dem åtkomst till galleriet. Ersätt exempelmeddelandet alinne_montes@contoso.com i det här exemplet med din egen information.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```