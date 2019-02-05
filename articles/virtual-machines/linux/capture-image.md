---
title: Spara en avbildning av en Linux-VM i Azure med Azure CLI | Microsoft Docs
description: Spara en avbildning av en Azure-dator som ska användas för drivrutiner för masslagring distributioner med hjälp av Azure CLI.
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
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 5022d765b5dfa4f1f973b7fb4370d5314bb887b8
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731945"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Så här skapar du en avbildning av en virtuell dator eller virtuell Hårddisk

<!-- generalize, image - extended version of the tutorial-->

Spara en avbildning av den virtuella datorn eller OS-VHD för att skapa flera kopior av en virtuell dator (VM) för användning i Azure. Om du vill skapa en avbildning för distribution, måste du ta bort personlig information. I följande steg, ta bort etableringen av en befintlig virtuell dator du, frigör den och skapa en avbildning. Du kan använda den här bilden för att skapa virtuella datorer mellan alla resursgrupper i din prenumeration.

Skapa en kopia av din befintliga Linux-VM för säkerhetskopiering eller felsökning eller ladda upp en specialiserad Linux-VHD från en lokal virtuell dator i avsnittet [ladda upp och skapa en Linux VM från anpassad diskavbildning](upload-vhd.md).  

Du kan också använda **Packer** att skapa din egen konfiguration. Mer information finns i [hur du använder Packer för att skapa Linux-avbildningar i Azure](build-image-with-packer.md).

Du behöver följande innan du skapar en avbildning:

* En Azure-dator som skapats i Resource Manager-distributionsmodellen som använder hanterade diskar. Om du inte har skapat en Linux VM, kan du använda den [portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md), eller [Resource Manager-mallar](create-ssh-secured-vm-from-template.md). Konfigurera den virtuella datorn efter behov. Till exempel [lägga till datadiskar](add-disk.md), tillämpa uppdateringar och installera program. 

* Senast [Azure CLI](/cli/azure/install-az-cli2) installerad och vara inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index#az-login).

## <a name="quick-commands"></a>Snabbkommandon

För en förenklad version av den här artikeln och för att testa, utvärderar eller lära dig om virtuella datorer i Azure finns [skapa en anpassad avbildning av en Azure-dator med hjälp av CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Steg 1: Avetablera den virtuella datorn
Först ska du avetablera den virtuella datorn med hjälp av Azure VM-agenten att ta bort datorspecifik filer och data. Använd den `waagent` med den `-deprovision+user` parametern på källan Linux VM. Mer information finns i [Användarguide för Azure Linux Agent](../extensions/agent-linux.md).

1. Ansluta till din Linux-VM med en SSH-klient.
2. I fönstret SSH anger du följande kommando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Kör bara det här kommandot på en virtuell dator som du ska samla in som en bild. Detta kommando garanterar inte att avbildningen är avmarkerad av all känslig information eller lämpar sig för Vidaredistribution. Den `+user` parametern tar också bort senast etablerade användarkontot. För att behålla autentiseringsuppgifter för användarkontot i den virtuella datorn, Använd bara `-deprovision`.
 
3. Ange **y** att fortsätta. Du kan lägga till den `-force` parametern för att undvika det här steget för bekräftelse.
4. När kommandot har slutförts kan du ange **avsluta** att Stäng SSH-klienten.

## <a name="step-2-create-vm-image"></a>Steg 2: Skapa VM-avbildning
Använda Azure CLI för att markera den virtuella datorn som generaliserad och spara avbildningen. I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar *myResourceGroup*, *myVnet*, och *myVM*.

1. Frigör den virtuella datorn som du tagit bort etableringen med [az vm deallocate](/cli/azure/vm). I följande exempel bort den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Markera den virtuella datorn som generaliserad med [az vm generalize](/cli/azure/vm). I följande exempel markeras den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup* som generaliserad.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Skapa en avbildning av den Virtuella datorresursen med [az bild skapa](/cli/azure/image#az-image-create). I följande exempel skapas en avbildning med namnet *myImage* i resursgruppen med namnet *myResourceGroup* med hjälp av VM-resurs med namnet *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Avbildningen skapas i samma resursgrupp som den Virtuella källdatorn. Du kan skapa virtuella datorer i valfri resursgrupp i din prenumeration från den här avbildningen. Du kanske vill skapa en specifik resursgrupp för VM-resurser och bilder från en management-perspektiv.
   >
   > Om du vill lagra avbildningen i zonen flexibel lagring måste du skapa den i en region som har stöd för [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera den `--zone-resilient true` parametern.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Steg 3: Skapa en virtuell dator från avbildningen
Skapa en virtuell dator med hjälp av avbildningen som du skapade med [az vm skapa](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVMDeployed* från avbildningen med namnet *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Skapa den virtuella datorn i en annan resursgrupp 

Du kan skapa virtuella datorer från en avbildning i alla resursgrupper i din prenumeration. Om du vill skapa en virtuell dator i en annan resursgrupp än avbildningen som du ange fullständigt resurs-ID i avbildningen. Använd [az bildlista](/cli/azure/image#az-image-list) att visa en lista över avbildningar. De utdata som genereras påminner om de i följande exempel.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

I följande exempel används [az vm skapa](/cli/azure/vm#az-vm-create) att skapa en virtuell dator i en annan resursgrupp än Källavbildningen, genom att ange avbildning resurs-ID.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Steg 4: Verifiera distributionen

SSH till den virtuella datorn som du skapade för att verifiera distributionen och börja använda den nya virtuella datorn. För att ansluta via SSH, hitta IP-adress eller FQDN för den virtuella datorn med [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nästa steg
Du kan skapa flera virtuella datorer från din käll-avbildning. Att göra ändringar i din avbildning: 

- Skapa en virtuell dator utifrån din avbildning.
- Se några uppdateringar eller konfigurationsändringar.
- Följ stegen igen för att avetablera, frigöra, generalisera och skapa en avbildning.
- Använd den nya avbildningen för framtida distributioner. Du kan ta bort den ursprungliga avbildningen.

Mer information om hur du hanterar dina virtuella datorer med CLI finns i [Azure CLI](/cli/azure).
