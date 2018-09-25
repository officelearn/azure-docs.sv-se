---
title: Skapa delade VM-avbildningar med Azure CLI | Microsoft Docs
description: I den här artikeln får du lära dig hur du använder Azure CLI för att skapa en delad avbildning av en virtuell dator i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 2f8ddae05b97b3fa6f1d3f65681defdd4e5a38b7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47048164"
---
# <a name="preview-create-a-shared-image-gallery-with-the-azure-cli"></a>Förhandstitt: Skapa en delad bildgalleriet med Azure CLI

Delad bildgalleriet förenklar anpassad avbildning delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. Delad galleriet kan du dela din anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner inom en AAD-klient. Välj vilka bilder som du vill dela, vilka regioner som du vill göra dem tillgängliga i och som du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar. Galleriet är en resurs på toppnivå som ger fullständig rollbaserad åtkomstkontroll (RBAC). Bilder kan versionshanteras och du kan välja att replikera varje versionsnumret för avbildningen till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade avbildningar.

I den här artikeln får skapa du dina egna galleriet och anpassade avbildningar av virtuella Azure-datorer. Lär dig att:

> [!div class="checklist"]
> * Skapa en delad bildgalleri
> * Skapa en delad avbildningsdefinitionen
> * Skapa en delad Avbildningsversion
> * Skapa en virtuell dator från en delad avbildning
> * Ta bort en resurser


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här artikeln kräver att du kör Azure CLI version 2.0.46 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator från avbildningen version med [az vm skapa](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   -g myGalleryRG \
   -n myVM \
   --image "/subscriptions/<subscription-ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
   --generate-ssh-keys
```

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]



## <a name="next-steps"></a>Nästa steg

Mer information om delade Image Galleries finns i den [översikt](shared-image-galleries.md).
