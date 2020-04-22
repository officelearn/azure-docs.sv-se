---
title: Skapa delade avbildningsgallerier med Azure CLI
description: I den här artikeln får du lära dig hur du använder Azure CLI för att skapa en delad avbildning av en virtuell dator i Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: article
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c75d33f8310cfd143bf201cdac861954d07baf0b
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758416"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Skapa ett delat avbildningsgalleri med Azure CLI

Ett [delat bildgalleri](shared-image-galleries.md) förenklar anpassad bilddelning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med det delade bildgalleriet kan du dela dina anpassade VM-avbildningar med andra i organisationen, inom eller mellan regioner, inom en AAD-klientorganisation. Välj vilka bilder du vill dela, vilka regioner du vill göra dem tillgängliga i och vilka du vill dela dem med. Du kan skapa flera gallerier så att du logiskt kan gruppera delade bilder. 

Galleriet är en resurs på den högsta nivån som ger fullständig rollbaserad åtkomstkontroll (RBAC). Avbildningar kan versionsas och du kan välja att replikera varje avbildningsversion till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade bilder.

Funktionen Delat bildgalleri har flera resurstyper. Vi kommer att använda eller bygga dessa i den här artikeln:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad bild** | Det här är en grundläggande bild som kan användas ensamt eller användas för att skapa en **bildversion** i ett bildgalleri. Hanterade avbildningar skapas från generaliserade virtuella datorer. En hanterad avbildning är en speciell typ av virtuell hårddisk som kan användas för att skapa flera virtuella datorer och som nu kan användas för att skapa delade avbildningsversioner. |
| **Bildgalleri** | Precis som Azure Marketplace är ett **avbildningsgalleri** en databas för hantering och delning av avbildningar, men du styr vem som har åtkomst. |
| **Bilddefinition** | Bilder definieras i ett galleri och innehåller information om bilden och krav för att använda den internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och lägsta och högsta minneskrav. Det är en definition av en typ av bild. |
| **Bildversion** | En **bildversion** är vad du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. Precis som en hanterad avbildning används avbildningsversionen när du använder en **avbildningsversion** för att skapa en virtuell dator för att skapa nya diskar för den virtuella datorn. Bildversioner kan användas flera gånger. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator från den senaste avbildningsversionen med [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

Du kan också använda en viss version med `--image` hjälp av bildversions-ID för parametern. Om du till exempel vill använda bildversion *1.0.0* skriver du: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Nästa steg
[Azure Image Builder (förhandsgranskning)](image-builder-overview.md) kan hjälpa till att automatisera skapandet av avbildningsversion, du kan till och med använda den för att uppdatera och [skapa en ny avbildningsversion från en befintlig avbildningsversion](image-builder-gallery-update-image-version.md). 

Du kan också skapa resurser för delat bildgalleri med hjälp av mallar. Det finns flera Azure Quickstart-mallar tillgängliga: 

- [Skapa ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en bilddefinition i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en bildversion i ett delat bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade bildgallerier finns i [översikten](shared-image-galleries.md). Om du stöter på problem läser du [Felsöka delade bildgallerier](troubleshooting-shared-images.md).
