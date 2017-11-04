---
title: Skapa anpassade VM-avbildningar med Azure CLI | Microsoft Docs
description: "Självstudiekurs – skapa en anpassad VM-avbildning med hjälp av Azure CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/21/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 23212bc947dc523561acdf9d1ca784d1bab2c84c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI

Anpassade avbildningar liknar marketplace-bilder, men du skapa dem själv. Anpassade avbildningar kan användas för bootstrap konfigurationer, till exempel förinstalleras program, Programinställningar och andra OS-konfigurationer. I den här självstudiekursen skapar du en egen anpassad avbildning av en virtuell Azure-dator. Lär dig att:

> [!div class="checklist"]
> * Ta bort etableringen och generalisera virtuella datorer
> * Skapa en egen avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Alla avbildningar i din prenumeration
> * Ta bort en bild


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan innehåller information om hur du tar en befintlig virtuell dator och omvandla det till en återanvändbara anpassad avbildning som du kan använda för att skapa nya VM-instanser.

Du måste ha en befintlig virtuell dator för att slutföra exemplet i den här självstudiekursen. Om det behövs, detta [skriptexempel](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) kan skapa en åt dig. När gå igenom kursen, ersätter namn resursgrupp och VM där det behövs.

## <a name="create-a-custom-image"></a>Skapa en egen avbildning

Om du vill skapa en avbildning av en virtuell dator måste du förbereda den virtuella datorn genom att avetablering det frigjorts och markera den Virtuella som generaliserad källdatorn. När den virtuella datorn har förberetts, kan du skapa en avbildning.

### <a name="deprovision-the-vm"></a>Ta bort etableringen av den virtuella datorn 

Avetablering generalisering den virtuella datorn genom att ta bort information om datorn. Den här generalisering gör det möjligt att distribuera flera virtuella datorer från en enda avbildning. Under avetablering, värdnamnet återställs till *localhost.localdomain*. SSH-värdnycklar, nameserver konfigurationer, rotlösenordet och cachelagrade DHCP-lån tas också bort.

Om du vill ta bort etableringen av den virtuella datorn, kan du använda Virtuella Azure-agenten (waagent). Virtuella Azure-agenten är installerad på den virtuella datorn och hanterar etablering och interagerar med Azure-Infrastrukturkontrollanten. Mer information finns i [Azure Linux-agenten användarhandboken](agent-user-guide.md).

Ansluta till den virtuella datorn med SSH och kör kommandot för att ta bort etableringen av den virtuella datorn. Med den `+user` argumentet, det senaste kontot för etablerad användare och alla associerade data tas också bort. Ersätt den IP-adressen med den offentliga IP-adressen på den virtuella datorn.

SSH till den virtuella datorn.
```bash
ssh azureuser@52.174.34.95
```
Ta bort etableringen av den virtuella datorn.

```bash
sudo waagent -deprovision+user -force
```
Stäng SSH-session.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Frigöra och markera den virtuella datorn som generaliserad

Om du vill skapa en avbildning måste den virtuella datorn att frigöras. Frigör den virtuella datorn med hjälp av [az vm frigöra](/cli//azure/vm#deallocate). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Slutligen att ställa in tillståndet för den virtuella datorn eftersom generaliserad med [az vm generalisera](/cli//azure/vm#generalize) så att Azure-plattformen vet att den virtuella datorn har generaliserats. Du kan bara skapa en avbildning från en generaliserad virtuell dator.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Skapa avbildningen

Nu kan du skapa en avbildning av den virtuella datorn med hjälp av [az bild skapa](/cli//azure/image#create). I följande exempel skapas en bild med namnet *myImage* från en virtuell dator med namnet *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Skapa virtuella datorer från avbildningen

Nu när du har skapat en avbildning kan du skapa en eller flera nya virtuella datorer från en avbildning med hjälp av [az vm skapa](/cli/azure/vm#create). I följande exempel skapas en virtuell dator med namnet *myVMfromImage* från avbildningen med namnet *myImage*.

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

Visa alla avbildningar av namnet i tabellformat.

```azurecli-interactive 
az image list \
  --resource-group myResourceGroup
```

Ta bort en bild. Det här exemplet tar bort det bild med namnet *myOldImage* från den *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I kursen får skapat du en anpassad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Ta bort etableringen och generalisera virtuella datorer
> * Skapa en egen avbildning
> * Skapa en virtuell dator från en anpassad avbildning
> * Alla avbildningar i din prenumeration
> * Ta bort en bild

Gå vidare till nästa kurs vill veta mer om virtuella datorer med hög tillgänglighet.

> [!div class="nextstepaction"]
> [Skapa högtillgängliga virtuella datorer](tutorial-availability-sets.md).

