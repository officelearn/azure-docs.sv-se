---
title: Använda delade VM-avbildningar för att skapa en skalningsuppsättning i Azure
description: Lär dig hur du använder Azure CLI för att skapa delade VM-avbildningar som ska användas för distribution av skalningsuppsättningar för virtuella datorer i Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276273"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Skapa och använda delade avbildningar för skalningsuppsättningar för virtuella datorer med Azure CLI 2.0

När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. [Delade bildgallerier](shared-image-galleries.md) förenklar i hög grad anpassad bilddelning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. Med det delade bildgalleriet kan du dela dina anpassade VM-avbildningar med andra i organisationen, inom eller mellan regioner, inom en AAD-klientorganisation. Välj vilka bilder du vill dela, vilka regioner du vill göra dem tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du logiskt kan gruppera delade bilder. Galleriet är en resurs på den högsta nivån som ger fullständig rollbaserad åtkomstkontroll (RBAC). Avbildningar kan versionsas och du kan välja att replikera varje avbildningsversion till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder. 

>[!NOTE]
> Den här artikeln går igenom processen att använda en generaliserad hanterad avbildning. Det går inte att skapa en skalningsuppsättning från en särskild VM-avbildning.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Skapa en skalningsuppsättning från den anpassad virtuella datoravbildningen
Skapa en skalningsuppsättning med [`az vmss create`](/cli/azure/vmss#az-vmss-create). Istället för en plattformsavbildning som *UbuntuLTS* eller *CentOS*, anger du namnet på din anpassade virtuella datoravbildning. Följande exempel skapar en skalningsuppsättning med namnet *myScaleSet* som använder den anpassade avbildningen med namnet *myImage* från föregående steg:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort skalningsuppsättningen och ytterligare resurser tar du bort resursgruppen och alla dess resurser med [az-gruppborttagning](/cli/azure/group). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg

Du kan också skapa resurs för delat bildgalleri med hjälp av mallar. Det finns flera Azure Quickstart-mallar tillgängliga: 

- [Skapa ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en bilddefinition i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en bildversion i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Om du stöter på några problem kan du [felsöka delade bildgallerier](troubleshooting-shared-images.md).
