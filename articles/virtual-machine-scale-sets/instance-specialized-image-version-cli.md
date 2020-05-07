---
title: Skapa en skalnings uppsättning från en specialiserad avbildnings version med hjälp av Azure CLI
description: Skapa en skalnings uppsättning med hjälp av en specialiserad avbildnings version i ett galleri för delade avbildningar med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 5de9fe7c81059c56c99a55ca066e186cbf83c50f
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796985"
---
# <a name="create-a-scale-set-using-a-specialized-image-version-with-the-azure-cli"></a>Skapa en skalnings uppsättning med hjälp av en specialiserad avbildnings version med Azure CLI

Skapa en skalnings uppsättning från en [specialiserad avbildnings version](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#generalized-and-specialized-images) som lagras i ett delat avbildnings Galleri. Om du vill skapa en skalnings uppsättning med en generaliserad avbildnings version, se [skapa en virtuell dator från en generaliserad avbildnings version](instance-generalized-image-version-cli.md).

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.4.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

Ersätt resurs namn efter behov i det här exemplet. 

Visa en lista över bild definitionerna i ett galleri med hjälp av [AZ sig-bild definitions listan](/cli/azure/sig/image-definition#az-sig-image-definition-list) för att se namn och ID för definitionerna.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Skapa en skalnings uppsättning [`az vmss create`](/cli/azure/vmss#az-vmss-create) med hjälp `--specialized` av parametern för att ange att avbildningen är en specialiserad bild.

Använd bild Definitions-ID `--image` : t för för att skapa skalnings uppsättnings instanserna från den senaste versionen av avbildningen som är tillgänglig. Du kan också skapa skalnings uppsättnings instanser från en speciell version genom att ange avbildningens versions `--image`-ID för. Tänk på att om du använder en angiven avbildnings version kan Automation inte köras om den angivna avbildnings versionen inte är tillgänglig eftersom den har tagits bort eller tagits bort från regionen. Vi rekommenderar att du använder bild Definitions-ID: t för att skapa din nya virtuella dator, om inte en speciell avbildnings version krävs.

I det här exemplet skapar vi instanser från den senaste versionen av *myImageDefinition* -avbildningen.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --Specialized
```


## <a name="next-steps"></a>Nästa steg
[Azure Image Builder (för hands version)](../virtual-machines/linux/image-builder-overview.md) kan hjälpa dig att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildnings definition i ett galleri för delade avbildningar](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildnings version i ett galleri för delad avbildning](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)



