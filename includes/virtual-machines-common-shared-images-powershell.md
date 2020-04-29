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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "66241217"
---
## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att [https://shell.azure.com/powershell](https://shell.azure.com/powershell)gå till. Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="get-the-managed-image"></a>Hämta den hanterade avbildningen

Du kan se en lista över avbildningar som är tillgängliga i en resurs grupp med hjälp av [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). När du känner till avbildningens namn och vilken resurs grupp det finns i kan du använda `Get-AzImage` igen för att hämta objektet och lagra det i en variabel som ska användas senare. Det här exemplet hämtar en bild med namnet *image* från resurs gruppen "myResourceGroup" och tilldelar den variabeln *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck. Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). I följande exempel skapas ett galleri med namnet min *Galleri* i resurs gruppen *myGalleryRG* .

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
   
## <a name="create-an-image-definition"></a>Skapa en avbildnings definition 

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om de avbildnings versioner som skapas i dem. Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Skapa avbildnings definitionen med [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). I det här exemplet heter Galleri bilden *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Skapa en avbildnings version

Skapa en avbildnings version från en hanterad avbildning med [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är avbildnings versionen *1.0.0* och replikeras till både *västra centrala* USA och *södra centrala* Data Center. Kom ihåg att du även måste inkludera *käll* regionen som mål för replikering när du väljer mål regioner för replikering.


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

Det kan ta en stund att replikera avbildningen till alla mål regioner, så vi har skapat ett jobb så att vi kan spåra förloppet. Om du vill se förloppet för jobbet skriver `$job.State`du.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version. 
>
> Du kan också lagra avbildnings versionen i [zon redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) genom att `-StorageAccountType Standard_ZRS` lägga till den när du skapar avbildnings versionen.
>


## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar åtkomst på avbildnings Galleri nivån. Använd en e-postadress och cmdleten [Get-AzADUser](/powershell/module/az.resources/get-azaduser) för att hämta objekt-ID: t för användaren och Använd sedan [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att ge dem åtkomst till galleriet. Ersätt e-postmeddelandet alinne_montes@contoso.com i det här exemplet med din egen information.

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