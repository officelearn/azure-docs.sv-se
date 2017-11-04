---
title: Ladda upp eller kopiera en anpassad Linux VM med Azure CLI 2.0 | Microsoft Docs
description: Ladda upp eller kopiera en anpassad virtuell dator med Resource Manager-distributionsmodellen och Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/06/2017
ms.author: cynthn
ms.openlocfilehash: 7c297725c26ea6c44403a10ecdcc3542f89f10b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-vm-from-custom-disk-with-the-azure-cli-20"></a>Skapa en Linux VM från anpassade disken med Azure CLI 2.0

<!-- rename to create-vm-specialized -->

Den här artikeln visar hur du överför en anpassad virtuell hårddisk (VHD) eller kopiera en en befintlig virtuell Hårddisk i Azure och skapa nya virtuella Linux-datorer (VM) från den anpassa disken. Du kan installera och konfigurera en Linux-distro enligt dina behov och sedan använda den virtuella Hårddisken för att snabbt skapa en ny Azure virtuell dator.

Om du vill skapa flera virtuella datorer från din anpassade disken bör du skapa en avbildning från virtuell dator eller virtuell Hårddisk. Mer information finns i [skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).

Du kan välja mellan två alternativ:
* [Överföra en virtuell hårddisk](#option-1-upload-a-specialized-vhd)
* [Kopiera en befintlig virtuell Azure-dator](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Snabbkommandon

När du skapar en ny virtuell dator med hjälp av [az vm skapa](/cli/azure/vm#create) från en anpassad eller särskilda disk du **bifoga** disken (--bifoga-os-disk) istället för att ange en anpassad eller marketplace-avbildning (--bilden). I följande exempel skapas en virtuell dator med namnet *myVM* med den hantera disken med namnet *myManagedDisk* skapas från den anpassade virtuella Hårddisken:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Krav
Du behöver följande för att slutföra följande steg:

* En virtuell Linux-dator som har förberetts för användning i Azure. Den [förbereda den virtuella datorn](#prepare-the-vm) i den här artikeln beskriver hur du hittar distro specifik information om hur du installerar Azure Linux-agenten (waagent) som krävs för den virtuella datorn ska fungera korrekt i Azure och du ska kunna ansluta till den via SSH.
* VHD-filen från en befintlig [Azure-godkända Linux-distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information för icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) till en virtuell disk i VHD-format. Det finns flera verktyg för att skapa en virtuell dator och virtuell Hårddisk:
  * Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), och ser till att använda VHD som bildformat. Om det behövs kan du [konvertera en bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med **qemu img konvertera**.
  * Du kan också använda Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [på Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nyare VHDX-format stöds inte i Azure. När du skapar en virtuell dator kan du ange VHD som formatet. Om det behövs, du kan konvertera VHDX-diskar på VHD med [qemu img konvertera](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Dessutom stöder inte Azure överför dynamiska virtuella hårddiskar, så du måste konvertera dessa diskar till statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för att gå](https://github.com/Microsoft/azure-vhd-utils-for-go) att konvertera dynamiska diskar under processen med att ladda upp till Azure.
> 
> 


* Se till att du har senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login).

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår *myResourceGroup*, *mittlagringskonto*, och *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

Azure stöder olika Linux-distributioner (se [godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). I följande artiklar när du går igenom hur du förbereder de olika Linux-distributioner som stöds i Azure:

* [CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Andra - icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se även den [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) mer allmänna tips om hur du förbereder Linux avbildningar för Azure.

> [!NOTE]
> Den [Azure-plattformen SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller för virtuella datorer som kör Linux endast när en av de påtecknade distributioner används med konfigurationsinformation som anges under stöds versioner i [Linux på Azure-Endorsed distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Alternativ 1: Ladda upp en virtuell Hårddisk

Du kan överföra en anpassad virtuell Hårddisk som du har körs på en lokal dator eller som du exporterade från ett annat moln. Om du vill använda den virtuella Hårddisken för att skapa en ny Azure VM, måste du överföra den virtuella Hårddisken till ett lagringskonto och skapa en hanterade diskar från den virtuella Hårddisken. 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du laddar upp din anpassade disk och skapar virtuella datorer, måste du först skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* plats: [översikt över Azure hanterade diskar](../windows/managed-disks-overview.md)
```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett lagringskonto för anpassade disk- och virtuella datorer med [az storage-konto skapar](/cli/azure/storage/account#create). 

I följande exempel skapas ett lagringskonto med namnet *mittlagringskonto* i resursgruppen som du skapade tidigare:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Lista nycklar för lagringskonto
Två 512-bitars åtkomstnycklar för varje lagringskonto genererar Azure. Dessa snabbtangenter används vid autentisering till storage-konto som utför skrivåtgärder. Läs mer om [hantera åtkomst till lagring här](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Du kan visa snabbtangenterna med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys#list).

Visa åtkomstnycklar för lagringskontot som du skapade:

```azurecli
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount
```

Utdatan liknar följande:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Anteckna **key1** eftersom du ska använda för att interagera med ditt lagringskonto i nästa steg.

### <a name="create-a-storage-container"></a>Skapa en lagringsbehållare
På samma sätt som du skapar olika kataloger för att organisera logiskt det lokala filsystemet, kan du skapa behållare i ett lagringskonto för att organisera dina diskar. Ett lagringskonto kan innehålla valfritt antal behållare. Skapa en behållare med [az lagringsbehållaren skapa](/cli/azure/storage/container#create).

I följande exempel skapas en behållare med namnet *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Överför den virtuella Hårddisken
Ladda upp din anpassade disk med [az storage blob överför](/cli/azure/storage/blob#upload). Du överför och lagra anpassade disken som en sidblobb.

Ange din snabbtangent, den behållare som du skapade i föregående steg och sökvägen till den anpassa disken på den lokala datorn:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Överför den virtuella Hårddisken kan ta en stund.

### <a name="create-a-managed-disk"></a>Skapa en hanterad disk


Skapa en hanterade diskar från den virtuella Hårddisken med hjälp av [az disk skapa](/cli/azure/disk#create). I följande exempel skapas en hanterad disk med namnet *myManagedDisk* från den virtuella Hårddisken som du har överfört till namngivna storage-konto och behållare:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Alternativ 2: Kopiera en befintlig virtuell dator

Du kan också skapa anpassade VM i Azure och sedan kopiera OS-disk och koppla den till en ny virtuell dator att skapa en annan kopia. Det här är bra för testning, men om du vill använda en befintlig virtuell Azure-dator som modell för flera nya virtuella datorer verkligen bör du skapa en **bild** i stället. Mer information om hur du skapar en avbildning från en befintlig Azure-VM finns [skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md)

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Det här exemplet skapas en ögonblicksbild av en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup* och skapar en ögonblicksbild med namnet *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Skapa den hantera disken

Skapa en ny hanterade disk från ögonblicksbilden.

Hämta ID för ögonblicksbilden. I det här exemplet ögonblicksbilden heter *osDiskSnapshot* och finns i den *myResourceGroup* resursgruppen.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Skapa den hantera disken. I det här exemplet skapar vi en hanterad disk med namnet *myManagedDisk* från våra ögonblicksbild som är 128 GB i storlek i standardlagring.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Nu ska du skapa den virtuella datorn med [az vm skapa](/cli/azure/vm#create) och bifoga (--bifoga-os-disk) hanterade disken som OS-disk. I följande exempel skapas en virtuell dator med namnet *myNewVM* med den hantera disken som skapas från den överförda virtuella Hårddisken:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Du bör kunna SSH till den virtuella datorn med autentiseringsuppgifter från den Virtuella källdatorn. 

## <a name="next-steps"></a>Nästa steg
När du har förberett och överföra din egen virtuell disk, kan du läsa mer om [med hjälp av hanteraren för filserverresurser och mallar](../../azure-resource-manager/resource-group-overview.md). Du kan också [lägger till en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) till din nya virtuella datorer. Om du har program som körs på din virtuella dator som du behöver åtkomst till [öppna portar och slutpunkter](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

