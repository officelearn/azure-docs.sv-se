---
title: ta med fil
description: ta med fil
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 830deb7569772b610b7e6abde649830b7ad67a92
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047845"
---
## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en befintlig hanterad avbildning av en generaliserad virtuell dator för att utföra exemplet i den här artikeln. Mer information finns i [självstudie: skapa en anpassad avbildning av en Azure-dator med Azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). 

## <a name="preview-register-the-feature"></a>Förhandsversion: Registrera funktionen

Delade Image Galleries är i förhandsversion, men du behöver registrera funktionen innan du kan använda den. Så här registrerar funktionen delad Image Galleries:

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

Det kan ta ett par minuter att registrera funktionen. Du kan kontrollera förloppet med:

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>Skapa ett avbildningsgalleri 

Ett galleri med avbildningar är den primära resursen som används för att aktivera delning av avbildningen. Galleriet namn måste vara unikt inom prenumerationen. Skapa en avbildning galleriet med hjälp av [az sig skapa](/cli/azure/sig#az-sig-create). I följande exempel skapas ett galleri med namnet *myGallery* i *myGalleryRG*.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Skapa en definition för avbildning

Skapa en definition för inledande bild i galleriet med [az sig avbildningsdefinitionen skapa](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>Skapa en Bildversion 
 
Skapa versioner av bilden efter behov med hjälp av [az galleriet Skapa-bild-Avbildningsversion](/cli/azure/sig/image-version#az-sig-image-version-create). Du behöver att skicka in ID för hanterad avbildning som ska användas som utgångspunkt för att skapa versionsnumret för avbildningen. Du kan använda [az bildlista](/cli/azure/image?view#az-image-list) att få information om avbildningar som finns på en resursgrupp. I det här exemplet är versionen av vår avbildning *1.0.0* och vi ska skapa 5 Totalt antal repliker i den *USA, västra centrala*, *södra centrala USA* och östra USA 2 * regioner.

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

