---
title: Spara en avbildning av en Linux-VM i Azure med Azure CLI | Microsoft Docs
description: Spara en avbildning av en Azure-dator som ska användas för drivrutiner för masslagring distributioner med Azure CLI.
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
ms.openlocfilehash: 98d98c1337830ce54c7ff96c19812169be129584
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946824"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Så här skapar du en avbildning av en virtuell dator eller virtuell Hårddisk

<!-- generalize, image - extended version of the tutorial-->

Om du vill skapa flera kopior av en virtuell dator (VM) för att använda i Azure, hämta en avbildning av den virtuella datorn eller OS-VHD. Om du vill skapa en avbildning måste du ta bort personlig information som gör det säkrast att distribuera flera gånger. I följande steg avetablera en befintlig virtuell dator du, frigöra och skapa en avbildning. Du kan använda den här bilden för att skapa virtuella datorer mellan alla resursgrupper i din prenumeration.

Om du vill skapa en kopia av din befintliga Linux-VM för säkerhetskopiering och felsökning, eller ladda upp en specialiserad Linux-VHD från en lokal virtuell dator kan du läsa [ladda upp och skapa en Linux VM från anpassad diskavbildning](upload-vhd.md).  

Du kan också använda **Packer** att skapa din egen konfiguration. Läs mer om hur du använder Packer [hur du använder Packer för att skapa Linux-avbildningar i Azure](build-image-with-packer.md).


## <a name="before-you-begin"></a>Innan du börjar
Se till att du uppfyller följande krav:

* Du behöver en Azure-dator som skapats i Resource Manager-distributionsmodellen som använder hanterade diskar. Om du inte har skapat en Linux VM, kan du använda den [portal](quick-create-portal.md), [Azure CLI](quick-create-cli.md), eller [Resource Manager-mallar](create-ssh-secured-vm-from-template.md). Konfigurera den virtuella datorn efter behov. Till exempel [lägga till datadiskar](add-disk.md), tillämpa uppdateringar och installera program. 

* Du måste också ha senast [Azure CLI](/cli/azure/install-az-cli2) installerad och vara inloggad på ett Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index#az_login).

## <a name="quick-commands"></a>Snabbkommandon

En förenklad version av det här avsnittet för att testa, utvärderar eller lära dig om virtuella datorer i Azure finns [skapa en anpassad avbildning av en Azure-dator med hjälp av CLI](tutorial-custom-images.md).


## <a name="step-1-deprovision-the-vm"></a>Steg 1: Ta bort etableringen av den virtuella datorn
Du avetablera den virtuella datorn, med hjälp av Azure VM-agenten att ta bort specifika filer och data. Använd den `waagent` med den *-avetablering + användare* parametern på källan Linux VM. Mer information finns i [Användarguide för Azure Linux Agent](../extensions/agent-linux.md).

1. Anslut till din Linux-VM med hjälp av en SSH-klient.
2. I fönstret SSH skriver du följande kommando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > Kör bara det här kommandot på en virtuell dator som du vill spara som en bild. Det garanterar inte att avbildningen är avmarkerad av all känslig information eller lämpar sig för Vidaredistribution. Den *+ användare* parametern tar också bort senast etablerade användarkontot. Om du vill behålla autentiseringsuppgifter i den virtuella datorn kan bara använda *-avetablering* och ha kvar användarkontot datorn.
 
3. Typ **y** att fortsätta. Du kan lägga till den **-tvinga** parametern för att undvika det här steget för bekräftelse.
4. När kommandot har slutförts, skriver **avsluta**. Det här steget stängs SSH-klienten.

## <a name="step-2-create-vm-image"></a>Steg 2: Skapa VM-avbildning
Använda Azure CLI för att markera den virtuella datorn som generaliserad och spara avbildningen. I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen inkluderar *myResourceGroup*, *myVnet*, och *myVM*.

1. Frigör den virtuella datorn som du tagit bort etableringen med [az vm deallocate](/cli//azure/vm#deallocate). I följande exempel bort den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup*:
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. Markera den virtuella datorn som generaliserad med [az vm generalize](/cli//azure/vm#generalize). I följande exempel markeras den virtuella datorn med namnet *myVM* i resursgruppen med namnet *myResourceGroup* som generaliserad:
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. Nu skapa en avbildning av den Virtuella datorresursen med [az bild skapa](/cli/azure/image#az_image_create). I följande exempel skapas en avbildning med namnet *myImage* i resursgruppen med namnet *myResourceGroup* med hjälp av VM-resurs med namnet *myVM*:
   
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
Skapa en virtuell dator med hjälp av den avbildning som du skapade med [az vm skapa](/cli/azure/vm#az_vm_create). I följande exempel skapas en virtuell dator med namnet *myVMDeployed* från avbildningen med namnet *myImage*:

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Skapa den virtuella datorn i en annan resursgrupp 

Du kan skapa virtuella datorer från en avbildning i alla resursgrupper i din prenumeration. Om du vill skapa en virtuell dator i en annan resursgrupp än avbildningen som du ange fullständigt resurs-ID i avbildningen. Använd [az bildlista](/cli/azure/image#az_image_list) att visa en lista över avbildningar. Utdata ser ut ungefär så här:

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

I följande exempel används [az vm skapa](/cli/azure/vm#az_vm_create) skapa en virtuell dator i en annan resursgrupp än avbildningen som källa genom att ange resurs-ID för avbildningen:

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Steg 4: Kontrollera distributionen

Nu SSH till den virtuella datorn som du skapade för att verifiera distributionen och börja använda den nya virtuella datorn. För att ansluta via SSH, hitta IP-adress eller FQDN för den virtuella datorn med [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nästa steg
Du kan skapa flera virtuella datorer från din käll-avbildning. Om du behöver göra ändringar i din avbildning: 

- Skapa en virtuell dator utifrån din avbildning.
- Se några uppdateringar eller konfigurationsändringar.
- Följ stegen igen för att avetablera, frigöra, generalisera och skapa en avbildning.
- Använd den nya avbildningen för framtida distributioner. Om du vill kan du ta bort den ursprungliga avbildningen.

Mer information om hur du hanterar dina virtuella datorer med CLI finns i [Azure CLI](/cli/azure).
