---
title: Ta en avbildning av en virtuell Linux-dator med Azure CLI
description: Samla en avbildning av en virtuell Azure-dator som ska användas för massdistributioner med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.openlocfilehash: 77f6244651551763f5460432655d66267775a256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250405"
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>Så här skapar du en avbildning av en virtuell dator eller VHD

Om du vill skapa flera kopior av en virtuell dator (VM) för användning i Azure tar du en avbildning av den virtuella datorn eller av virtuell hårddisk för operativsystemet. Om du vill skapa en avbildning för distribution måste du ta bort personlig kontoinformation. I följande steg avetableras du avetablera en befintlig virtuell dator, frigöra den och skapa en avbildning. Du kan använda den här avbildningen för att skapa virtuella datorer i alla resursgrupper i din prenumeration.

Om du vill skapa en kopia av din befintliga virtuella Linux-dator för säkerhetskopiering eller felsökning, eller för att ladda upp en specialiserad Virtuell Linux-hårddisk från en lokal virtuell dator, finns [i Ladda upp och skapa en Virtuell Linux-dator från anpassad diskavbildning](upload-vhd.md).  

Du kan använda Azure **VM Image Builder (Public Preview)** för att skapa din anpassade avbildning, inget behov av att lära sig några verktyg eller konfigurera build-pipelines, helt enkelt tillhandahålla en avbildningskonfiguration och Image Builder skapar avbildningen. Mer information finns i [Komma igång med Azure VM Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

Du behöver följande objekt innan du skapar en bild:

* En Azure VM som skapats i Resource Manager-distributionsmodellen som använder hanterade diskar. Om du ännu inte har skapat en virtuell Linux-dator kan du använda [mallarna](quick-create-portal.md) [Azure CLI](quick-create-cli.md)eller [Resource Manager](create-ssh-secured-vm-from-template.md). Konfigurera den virtuella datorn efter behov. Lägg till exempel [till datadiskar,](add-disk.md)installera uppdateringar och installera program. 

* Den senaste [Azure CLI](/cli/azure/install-az-cli2) installerat och loggas in på ett Azure-konto med [az-inloggning](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Föredrar du en handledning istället?

En förenklad version av den här artikeln och för testning, utvärdering eller inlärning om virtuella datorer i Azure finns i [Skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).  Annars, fortsätt läsa här för att få hela bilden.


## <a name="step-1-deprovision-the-vm"></a>Steg 1: Avetablera den virtuella datorn
Först ska du avetablera den virtuella datorn med hjälp av Azure VM-agenten för att ta bort datorspecifika filer och data. Använd `waagent` kommandot med `-deprovision+user` parametern på din källa Linux VM. Mer information finns i [Användarguide för Azure Linux Agent](../extensions/agent-linux.md).

1. Anslut till din virtuella Linux-dator med en SSH-klient.
2. Ange följande kommando i SSH-fönstret:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Kör bara det här kommandot på en virtuell dator som du ska ta som en avbildning. Det här kommandot garanterar inte att bilden rensas från all känslig information eller är lämplig för omdistribution. Parametern `+user` tar också bort det senast etablerade användarkontot. Om du vill behålla autentiseringsuppgifter `-deprovision`för användarkonton i den virtuella datorn använder du bara .
 
3. Ange **y** för att fortsätta. Du kan `-force` lägga till parametern för att undvika det här bekräftelsesteget.
4. När kommandot är klart anger du **avsluta** för att stänga SSH-klienten.  Den virtuella datorn kommer fortfarande att köras på denna punkt.

## <a name="step-2-create-vm-image"></a>Steg 2: Skapa vm-avbildning
Använd Azure CLI för att markera den virtuella datorn som generaliserad och fånga avbildningen. I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn inkluderar *myResourceGroup,* *myVnet*och *myVM*.

1. Deallocate den virtuella datorn som du avetableras med [az vm deallocate](/cli/azure/vm). I följande exempel frigörs den virtuella datorn med namnet *myVM* i resursgruppen *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```
    
    Vänta tills den virtuella datorn helt kan frigöras innan du går vidare. Detta kan ta några minuter att slutföra.  Den virtuella datorn stängs av under deallocation.

2. Markera den virtuella datorn som generaliserad med [az vm generalize](/cli/azure/vm). I följande exempel markeras den virtuella datorn som heter *myVM* i resursgruppen *myResourceGroup* som generaliserad.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

    En virtuell dator som har generaliserats kan inte längre startas om.

3. Skapa en avbildning av vm-resursen med [az image create](/cli/azure/image#az-image-create). I följande exempel skapas en avbildning med namnet *myImage* i resursgruppen *myResourceGroup* med den vm-resurs som heter *myVM*.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Avbildningen skapas i samma resursgrupp som källdatorn. Du kan skapa virtuella datorer i valfri resursgrupp i din prenumeration från den här avbildningen. Ur ett hanteringsperspektiv kanske du vill skapa en specifik resursgrupp för vm-resurser och avbildningar.
   >
   > Om du vill lagra avbildningen i zontålig lagring måste du skapa den i en `--zone-resilient true` region som stöder [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkludera parametern.
   
Det här kommandot returnerar JSON som beskriver vm-avbildningen. Spara utdata för senare referens.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Steg 3: Skapa en virtuell dator från den tagna avbildningen
Skapa en virtuell dator med hjälp av avbildningen som du skapade med [az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVMDeployed* från avbildningen *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Skapa den virtuella datorn i en annan resursgrupp 

Du kan skapa virtuella datorer från en avbildning i valfri resursgrupp i din prenumeration. Om du vill skapa en virtuell dator i en annan resursgrupp än avbildningen anger du det fullständiga resurs-ID:et för avbildningen. Använd [az bildlista](/cli/azure/image#az-image-list) för att visa en lista med bilder. De utdata som genereras påminner om de i följande exempel.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

I följande exempel används [az vm-skapa](/cli/azure/vm#az-vm-create) för att skapa en virtuell dator i en annan resursgrupp än källavbildningen, genom att ange avbildningsresurs-ID.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Steg 4: Verifiera distributionen

SSH till den virtuella datorn som du skapade för att verifiera distributionen och börja använda den nya virtuella datorn. Om du vill ansluta via SSH hittar du IP-adressen eller FQDN för din virtuella dator med [az vm show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nästa steg
Du kan skapa flera virtuella datorer från vm-avbildningen. Så här gör du ändringar i bilden: 

- Skapa en virtuell dator från avbildningen.
- Gör eventuella uppdateringar eller konfigurationsändringar.
- Följ stegen igen för att avetablera, deallocate, generalisera och skapa en bild.
- Använd den här nya avbildningen för framtida distributioner. Du kan ta bort den ursprungliga bilden.

Mer information om hur du hanterar dina virtuella datorer med CLI finns i [Azure CLI](/cli/azure).
