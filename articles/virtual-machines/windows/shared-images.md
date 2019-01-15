---
title: Skapa delade VM-avbildningar med Azure PowerShell | Microsoft Docs
description: Lär dig hur du använder Azure PowerShell för att skapa en delad virtuell datoravbildning i Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/11/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: ecac7216582fa07e9c25492ddeb25e9f155da563
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54305185"
---
# <a name="preview-create-a-shared-image-gallery-with-azure-powershell"></a>Förhandsversion: Skapa en delad bildgalleriet med Azure PowerShell 

En [delad bildgalleriet](shared-image-galleries.md) förenklar anpassad avbildning delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för att kunna starta distributionsåtgärder som förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Delad galleriet kan du dela din anpassade VM-avbildningar med andra i din organisation, inom eller mellan regioner inom en AAD-klient. Välj vilka bilder som du vill dela, vilka regioner som du vill göra dem tillgängliga i och som du vill dela dem med. Du kan skapa flera gallerier så att du kan gruppera delade avbildningar. 

Galleriet är en resurs på toppnivå som ger fullständig rollbaserad åtkomstkontroll (RBAC). Bilder kan versionshanteras och du kan välja att replikera varje versionsnumret för avbildningen till en annan uppsättning Azure-regioner. Galleriet fungerar bara med hanterade avbildningar.

Delad bildgalleriet-funktionen har flera resurstyper. Vi ska använda eller att skapa dessa i den här artikeln:

| Resurs | Beskrivning|
|----------|------------|
| **Hanterad avbildning** | Det här är en grundläggande avbildning som kan användas fristående eller används för att skapa en **Avbildningsversion** i ett galleri med avbildningar. Hanterade avbildningarna skapas från generaliserad virtuella datorer. En hanterad avbildning är en särskild typ av virtuell Hårddisk som kan användas för att göra flera virtuella datorer och kan nu användas för att skapa delade bild versioner. |
| **Bildgalleri** | Som Azure Marketplace, en **bildgalleriet** är en lagringsplats för att hantera och dela bilder, men du bestämmer vem som har åtkomst. |
| **Avbildningsdefinitionen** | Bilder har definierats i ett galleri och utföra information om avbildningen och krav för att använda det internt. Detta inkluderar om avbildningen är Windows eller Linux, viktig information och lägsta och högsta minneskrav. Det är en definition av en typ av bild. |
| **Avbildningsversion** | En **Avbildningsversion** är det du använder för att skapa en virtuell dator när du använder ett galleri. Du kan ha flera versioner av en avbildning som behövs för din miljö. Som en hanterad avbildning när du använder en **Avbildningsversion** för att skapa en virtuell dator, versionsnumret för avbildningen som används för att skapa nya diskar för den virtuella datorn. Bild-versioner kan användas flera gånger. |


## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en befintlig hanterad avbildning för att utföra exemplet i den här artikeln. Du kan följa [självstudien: Skapa en anpassad avbildning av en Azure-dator med Azure PowerShell](tutorial-custom-images.md) att skapa en om det behövs. När du börjar med den här artikeln, Ersätt namnges resursgrupp och virtuell dator där det behövs.

[!INCLUDE [virtual-machines-common-shared-images-ps](../../../includes/virtual-machines-common-shared-images-powershell.md)]
 
## <a name="create-vms-from-an-image"></a>Skapa virtuella datorer från en avbildning

När versionsnumret för avbildningen är klar kan skapa du en eller flera nya virtuella datorer. Med hjälp av parametern förenklad ange för den [New-AzureRMVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm) cmdlet, behöver du bara ange avbildnings-ID för versionsnumret för avbildningen. 

Det här exemplet skapar en virtuell dator med namnet *myVMfromImage*i den *myResourceGroup* i den *USA, östra* datacenter.

```azurepowershell-interactive
New-AzureRmVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $imageVersion.Id `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs använder du cmdleten [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myGalleryRG
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa delade bildgalleriet resursen med hjälp av mallar. Det finns flera Azure-Snabbstartsmallar: 

- [Skapa en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en Definition för bilden i en delad Avbildningsgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en Bildversion i en delad bildgalleri](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från Avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Mer information om delade Image Galleries finns i den [översikt](shared-image-galleries.md). Om du stöter på problem, se [felsökning delade bildgallerier](troubleshooting-shared-images.md).

