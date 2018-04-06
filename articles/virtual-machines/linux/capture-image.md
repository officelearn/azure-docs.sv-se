---
title: Hämta en avbildning av en Linux VM i Azure med hjälp av CLI 2.0 | Microsoft Docs
description: Hämta en avbildning av en Azure VM ska användas för masslagring distributioner som använder Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: 9fa87f8dc0c94ec93e049e35be727c031b4b06ea
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Så här skapar du en avbildning av en virtuell dator eller virtuell Hårddisk

<!-- generalize, image - extended version of the tutorial-->

Om du vill skapa flera kopior av en virtuell dator (VM) ska användas i Azure, en avbildning av den virtuella datorn eller OS-VHD. Om du vill skapa en avbildning måste du ta bort personlig information som gör det säkrare att distribuera flera gånger. I följande steg du ta bort etableringen av en befintlig virtuell dator frigöra och skapa en avbildning. Du kan använda den här avbildningen för att skapa virtuella datorer på en resursgrupp i din prenumeration.

Om du vill skapa en kopia av din befintliga Linux-VM för säkerhetskopiering eller felsökning, eller överför en särskild Linux VHD från en lokal virtuell dator, se [överför och skapa en Linux VM från anpassade diskavbildning](upload-vhd.md).  

Du kan också använda **förpackaren** att skapa en anpassad konfiguration. Mer information om hur du använder förpackaren finns [använda förpackaren för att skapa avbildningar av Linux virtuella datorer i Azure](build-image-with-packer.md).


## <a name="before-you-begin"></a>Innan du börjar
Se till att du uppfyller följande krav:

* Du behöver en Azure VM som skapats i Resource Manager-distributionsmodellen med hjälp av hanterade diskar. Om du inte har skapat en Linux VM, kan du använda den [portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md), eller [Resource Manager-mallar](create-ssh-secured-vm-from-template.md). Konfigurera den virtuella datorn. Till exempel [lägga till datadiskar](add-disk.md), tillämpa uppdateringar och installera program. 

* Du måste också har senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och logga in till en Azure-konto med hjälp av [az inloggningen](/cli/azure/reference-index#az_login).

## <a name="quick-commands"></a>Snabbkommandon

En förenklad version av det här avsnittet för att testa, utvärderar eller Lär dig mer om virtuella datorer i Azure finns [skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Steg 1: Ta bort etableringen av den virtuella datorn
Du ta bort etableringen av den virtuella datorn med Virtuella Azure-agenten att ta bort specifika filer och data. Använd den `waagent` kommandot med de *-deprovision + användare* parameter på käll-Linux VM. Mer information finns i [Användarguide för Azure Linux Agent](../windows/agent-user-guide.md).

1. Ansluta till din Linux VM som använder en SSH-klient.
2. I fönstret SSH skriver du följande kommando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Endast köra kommandot på en virtuell dator som du vill samla in som en bild. Är det inte säkert att avbildningen är avmarkerad av all känslig information eller lämpar sig för omfördelning. Den *+ användaren* parametern tar också bort det senaste kontot för etablerad användare. Om du vill behålla autentiseringsuppgifter i den virtuella datorn bara använda *-deprovision* lämna användarkontot på plats.
 
3. Typen **y** att fortsätta. Du kan lägga till den **-tvinga** parametern för att undvika det här steget bekräftelse.
4. När kommandot har slutförts genom att skriva **avsluta**. Det här steget stänger SSH-klienten.

## <a name="step-2-create-vm-image"></a>Steg 2: Skapa VM-avbildning
Använda Azure CLI 2.0 för att markera den virtuella datorn som generaliserad och spara avbildningen. Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn inkluderar *myResourceGroup*, *myVnet*, och *myVM*.

1. Frigör den virtuella datorn som du avetableras med [az vm frigöra](/cli//azure/vm#deallocate). I följande exempel tar bort den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Markera den virtuella datorn som generaliserad med [az vm generalisera](/cli//azure/vm#generalize). Följande exempel märken på den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup* som generaliserad:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Nu skapa en avbildning av den Virtuella datorresursen med [az bild skapa](/cli/azure/image#az_image_create). I följande exempel skapas en bild med namnet *myImage* i resursgrupp med namnet *myResourceGroup* med hjälp av den Virtuella datorresursen med namnet *myVM*:
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Bilden har skapats i samma resursgrupp som ditt Virtuella källdatorn. Du kan skapa virtuella datorer i valfri resursgrupp i din prenumeration från den här avbildningen. Ur management kan du skapa en viss resursgrupp för VM-resurser och bilder.
   >
   > Om du vill lagra avbildningen i zonen flexibel måste du skapa i en region som stöder [tillgänglighet zoner](../../availability-zones/az-overview.md) och inkludera den `--zone-resilient true` parameter.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Steg 3: Skapa en virtuell dator från avbildningen
Skapa en virtuell dator med hjälp av den avbildning som du skapat med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMDeployed* från avbildningen med namnet *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Skapa den virtuella datorn i en annan resursgrupp 

Du kan skapa virtuella datorer från en avbildning i valfri resursgrupp i din prenumeration. Ange fullständiga resurs-ID i avbildningen för att skapa en virtuell dator i en annan resursgrupp än avbildningen. Använd [az bildlista](/cli/azure/image#az_image_list) att visa en lista över avbildningar. Utdata ser ut ungefär så här:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

I följande exempel används [az vm skapa](/cli/azure/vm#az_vm_create) att skapa en virtuell dator i en annan resursgrupp än källbilden genom att ange Bildresursen:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Steg 4: Verifiera distributionen

Nu SSH till den virtuella datorn som du skapade för att kontrollera distributionen och börja använda den nya virtuella datorn. För att ansluta via SSH, hitta IP-adress eller FQDN för den virtuella datorn med [az vm visa](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nästa steg
Du kan skapa flera virtuella datorer från din datakälla VM-avbildning. Om du behöver göra ändringar i avbildningen: 

- Skapa en virtuell dator från en avbildning.
- Se några uppdateringar eller ändringar i konfigurationen.
- Följ stegen igen för att ta bort etableringen, frigöra, generalisera och skapa en avbildning.
- Använd den här nya avbildningen för framtida distributioner. Om du vill ta bort den ursprungliga avbildningen.

Mer information om hur du hanterar dina virtuella datorer med CLI finns [Azure CLI 2.0](/cli/azure).
