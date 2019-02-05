---
title: Självstudie, skapa anpassade virtuella datoravbildningar med Azure CLI | Microsoft Docs
description: I den här självstudien får du lära dig hur du använder Azure CLI för att skapa en anpassad virtuell datoravbildning i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9c6729abefc62aeae232fff5ea48619377630635
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197627"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Självstudier: Skapa en anpassad avbildning av en virtuell Azure-dator med Azure CLI

Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. I den här självstudien skapar du en egen anpassad avbildning av en virtuell Azure-dator. Lär dig att:

> [!div class="checklist"]
> * Avetablera och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan visar hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan skapa nya VM-instanser med.

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i självstudien. Om det behövs kan du skapa en med detta [skriptexempel](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md). När du använder självstudien ersätter du namn på resursgrupp och VM där det behövs.

## <a name="create-a-custom-image"></a>Skapa en anpassad avbildning

Om du vill skapa en avbildning av en virtuell dator måste du förbereda den virtuella datorn genom att avetablera, frigöra och markera den virtuella källdatorn som generaliserad. Du kan skapa en avbildning när den virtuella datorn har förberetts.

### <a name="deprovision-the-vm"></a>Avetablera den virtuella datorn 

Avetableringen generaliserar den virtuella datorn genom att ta bort datorspecifik information. Tack vare generaliseringen kan man distribuera flera virtuella datorer från en enda avbildning. Under avetableringen återställs värdnamnet till *localhost.localdomain*. SSH-värdnycklar, namnserverkonfigurationer, rotlösenord och cachelagrade DHCP-lån tas också bort.

Om du vill avetablera den virtuella datorn kan du använda Azure VM-agenten (waagent). Azure VM-agenten är installerad på den virtuella datorn och hanterar etablering och interaktion med Azures infrastrukturkontrollant. Mer information finns i [Användarguide för Azure Linux Agent](../extensions/agent-linux.md).

Anslut till den virtuella datorn med SSH och kör kommandot för att avetablera den virtuella datorn. Med argumentet `+user` tas det senast etablerade användarkontot och alla associerade data också bort. Ersätt IP-exempeladressen med den offentliga IP-adressen för din virtuella dator.

SSH till den virtuella datorn.
```bash
ssh azureuser@52.174.34.95
```
Avetablera den virtuella datorn.

```bash
sudo waagent -deprovision+user -force
```
Stäng SSH-sessionen.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Frigöra och markera den virtuella datorn som generaliserad

Om du vill skapa en avbildning måste den virtuella datorn frigöras. Frigör den virtuella datorn med hjälp av [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Slutligen anger du tillståndet för den virtuella datorn som generaliserad med [az vm generalize](/cli//azure/vm#generalize), så att Azure-plattformen vet att den virtuella datorn har generaliserats. Du kan bara skapa en avbildning från en generaliserad virtuell dator.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Skapa avbildningen

Nu kan du skapa en avbildning av den virtuella datorn med hjälp av [az image create](/cli//azure/image#create). I följande exempel skapas en avbildning med namnet *myImage* från en virtuell dator med namnet *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Skapa virtuella datorer från avbildningen

Nu när du har en avbildning kan du skapa en eller flera nya virtuella datorer från den med hjälp av [az vm create](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMfromImage* från en avbildning med namnet *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>Avbildningshantering 

Här följer några exempel på vanliga hanteringsuppgifter för avbildning och hur du utför dem med hjälp av Azure CLI.

Lista alla avbildningar efter namn i ett tabellformat.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Ta bort en avbildning. I det här exemplet tar vi bort avbildningen med namnet *myOldImage* från *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I självstudien skapade du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Avetablera och generalisera virtuella datorer
> * Skapa en anpassad avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Göra en lista med alla avbildningar i din prenumeration
> * Ta bort en avbildning

Gå vidare till nästa självstudie om du vill veta mer om virtuella datorer med hög tillgänglighet.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md).

