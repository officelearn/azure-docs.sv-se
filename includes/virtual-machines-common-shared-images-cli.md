---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "66814710"
---
## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en befintlig hanterad avbildning av en generaliserad virtuell dator för att kunna slutföra exemplet i den här artikeln. Mer information finns i [Självstudier: skapa en anpassad avbildning av en virtuell Azure-dator med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Om den hanterade avbildningen innehåller en datadisk får data disk storleken inte vara större än 1 TB.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att [https://shell.azure.com/bash](https://shell.azure.com/bash)gå till. Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck.   Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [AZ sig-Create](/cli/azure/sig#az-sig-create). I följande exempel skapas ett galleri med namnet *Galleri* i *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Skapa en avbildnings definition

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om de avbildnings versioner som skapas i dem. Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Skapa en första avbildnings definition i galleriet med hjälp av [AZ sig-bild-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Skapa en avbildnings version 

Skapa versioner av avbildningen efter behov med hjälp av [AZ Image Gallery Create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Du måste skicka in ID: t för den hanterade avbildningen som ska användas som bas linje för att skapa avbildnings versionen. Du kan använda [AZ avbildnings lista](/cli/azure/image?view#az-image-list) för att hämta information om avbildningar som finns i en resurs grupp. 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är versionen av vår avbildning *1.0.0* och vi kommer att skapa 2 repliker i regionen *västra centrala USA* , 1 replik i regionen *södra centrala USA* och 1 replik i regionen *USA, östra 2* med zon-redundant lagring.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra alla dina avbildnings versions repliker i [zon redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) genom `--storage-account-type standard_zrs` att lägga till den när du skapar avbildnings versionen.
>

## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar med andra användare på Galleri nivån. Om du vill hämta objekt-ID: t för galleriet använder du [AZ sig Visa](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Använd objekt-ID: t som ett omfång, tillsammans med en e-postadress och [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att ge en användare åtkomst till det delade avbildnings galleriet.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


