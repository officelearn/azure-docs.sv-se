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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814710"
---
## <a name="before-you-begin"></a>Innan du börjar

Om du vill slutföra exemplet i den här artikeln måste du ha en befintlig hanterad avbildning av en allmän virtuell dator. Mer information finns i [Självstudiekurs: Skapa en anpassad avbildning av en virtuell Azure-dator med Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Om den hanterade avbildningen innehåller en datadisk kan datadiskstorleken inte vara mer än 1 TB.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i [https://shell.azure.com/bash](https://shell.azure.com/bash)en separat webbläsarflik genom att gå till . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda CLI lokalt läser du [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Skapa ett bildgalleri 

Ett bildgalleri är den primära resurs som används för att aktivera bilddelning. Tillåtna tecken för Gallerinamn är versaler eller gemener, siffror, punkter och punkter. Gallerinamnet får inte innehålla streck.   Gallerinamn måste vara unika i din prenumeration. 

Skapa ett bildgalleri med [az sig create](/cli/azure/sig#az-sig-create). I följande exempel skapas ett galleri med namnet *myGallery* i *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Skapa en bilddefinition

Bilddefinitioner skapar en logisk gruppering för bilder. De används för att hantera information om de bildversioner som skapas i dem. Bilddefinitionsnamn kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. Mer information om de värden du kan ange för en bilddefinition finns i [Bilddefinitioner](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Skapa en första bilddefinition i galleriet med [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

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


## <a name="create-an-image-version"></a>Skapa en bildversion 

Skapa versioner av bilden efter behov med hjälp av [az bildgalleri create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Du måste skicka in ID:et för den hanterade avbildningen som ska användas som baslinje för att skapa avbildningsversionen. Du kan använda [az image list](/cli/azure/image?view#az-image-list) för att få information om bilder som finns i en resursgrupp. 

Tillåtna tecken för bildversion är siffror och punkter. Talen måste ligga inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Patch*.

I det här exemplet är versionen av vår avbildning *1.0.0* och vi kommer att skapa 2 repliker i regionen *Västra centrala USA,* 1 replik i regionen *Södra centrala USA* och 1 replik i regionen Östra USA *2* med zonuppsagd lagring.


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
> Du måste vänta tills avbildningsversionen helt har byggts och replikerats innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra alla dina bildversionsrepliker i [Zon redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) genom att lägga till `--storage-account-type standard_zrs` när du skapar avbildningsversionen.
>

## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar med andra användare på gallerinivå. Om du vill hämta objekt-ID:et för ditt galleri använder du [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Använd objekt-ID som ett scope, tillsammans med en e-postadress och [az-rolltilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att ge en användare åtkomst till det delade bildgalleriet.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


