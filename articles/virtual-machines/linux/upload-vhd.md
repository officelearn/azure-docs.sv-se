---
title: Ladda upp eller kopiera en anpassad Linux VM med Azure CLI | Microsoft Docs
description: Ladda upp eller kopiera en anpassad virtuell dator med hjälp av Resource Manager-distributionsmodellen och Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: bbbcc1b3b505aae4bcc6869359ca27a8cd3fd1be
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638129"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Skapa en Linux VM från en anpassad disk med Azure CLI

<!-- rename to create-vm-specialized -->

Den här artikeln visar hur du överför en anpassad virtuell hårddisk (VHD) och hur du kopierar en befintlig virtuell Hårddisk i Azure. Den nyligen skapade virtuella Hårddisken används sedan för att skapa nya virtuella Linux-datorer (VM). Du kan installera och konfigurera en Linux-distribution enligt dina behov och sedan använda den virtuella Hårddisken för att skapa en ny Azure-dator.

Om du vill skapa flera virtuella datorer från den anpassade disken, skapa en avbildning från din virtuella dator eller VHD. Mer information finns i [skapa en anpassad avbildning av en Azure-dator med hjälp av CLI](tutorial-custom-images.md).

Du har två alternativ för att skapa en anpassad disk:
* [Överföra en virtuell hårddisk](#option-1-upload-a-specialized-vhd)
* [Kopiera en befintlig Azure VM](#option-2-copy-an-existing-azure-vm)

## <a name="quick-commands"></a>Snabbkommandon

När du skapar en ny virtuell dator med [az vm skapa](/cli/azure/vm#az-vm-create) från en anpassad eller specialiserad disk du **bifoga** disken (--bifoga-os-disk) i stället för att ange en anpassad eller marketplace-avbildning (--bild). I följande exempel skapas en virtuell dator med namnet *myVM* med hjälp av hanterad disk med namnet *myManagedDisk* skapas från en anpassad virtuell Hårddisk:

```azurecli
az vm create --resource-group myResourceGroup --location eastus --name myVM \
   --os-type linux --attach-os-disk myManagedDisk
```

## <a name="requirements"></a>Krav
För att slutföra följande steg behöver du:

* En Linux-dator som har förberetts för användning i Azure. Den [förbereda den virtuella datorn](#prepare-the-vm) i den här artikeln beskriver hur du hittar distribution-specifik information om hur du installerar Azure Linux-agenten (waagent), vilket krävs att ansluta till en virtuell dator med SSH.
* VHD-filen från en befintlig [Azure-godkända Linux-distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information om icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) på en virtuell disk i VHD-format. Det finns flera verktyg för att skapa en virtuell dator och virtuell Hårddisk:
  * Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), och ser till att använda virtuella Hårddisken som bildformat. Om det behövs kan du [konvertera en bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert`.
  * Du kan också använda Hyper-V [i Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [på Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nyare VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator kan du ange VHD som formatet. Om det behövs kan du konvertera VHDX-diskar till virtuell Hårddisk med [qemu img konvertera](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Azure har inte stöd för att ladda upp dynamiska virtuella hårddiskar, så måste du konvertera dessa diskar till statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för GO](https://github.com/Microsoft/azure-vhd-utils-for-go) konverterar dynamiska diskar under processen att överföra dem till Azure.
> 
> 


* Se till att du har senast [Azure CLI](/cli/azure/install-az-cli2) installerat och du är inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index#az-login).

I följande exempel ersätter exempel parameternamn med dina egna värden som *myResourceGroup*, *mystorageaccount*, och *mydisks*.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

Azure har stöd för olika Linux-distributioner (se [godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). I följande artiklar beskriver hur du förbereder de olika Linux-distributioner som stöds på Azure:

* [CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Andra: Icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se även de [Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes) mer allmänna tips om hur du förbereder Linux-avbildningar för Azure.

> [!NOTE]
> Den [Azure-plattformen SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller för datorer som kör Linux endast när en av de godkända distributionerna används med konfigurationsinformation som anges under ”versioner som stöds” [Linux på Azure-godkända Distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Alternativ 1: Ladda upp en virtuell Hårddisk

Du kan överföra en anpassad virtuell Hårddisk som du har som körs på en lokal dator eller som du exporterade från ett annat moln. Om du vill använda en virtuell Hårddisk för att skapa en ny virtuell Azure-dator, måste du överföra den virtuella Hårddisken till ett lagringskonto och skapa en hanterad disk från den virtuella Hårddisken. Mer information finns i [Översikt över Azure Managed Disks](../windows/managed-disks-overview.md).

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du överför din anpassade disk och skapa virtuella datorer, måste du skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az-group-create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create \
    --name myResourceGroup \
    --location eastus
```

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett lagringskonto för din anpassade disk och virtuella datorer med [az storage-konto skapar](/cli/azure/storage/account#az-storageaccount-create). I följande exempel skapas ett lagringskonto med namnet *mystorageaccount* i resursgruppen som skapades tidigare:

```azurecli
az storage account create \
    --resource-group myResourceGroup \
    --location eastus \
    --name mystorageaccount \
    --kind Storage \
    --sku Standard_LRS
```

### <a name="list-storage-account-keys"></a>Lista nycklar för lagringskonto
Azure skapar två 512-bitars åtkomstnycklar för varje lagringskonto. Åtkomstnycklarna används vid autentisering till lagringskontot, till exempel när utför skrivåtgärder. Mer information finns i [hantera åtkomst till lagring](../../storage/common/storage-account-manage.md#access-keys). 

Du visar åtkomstnycklarna med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys#az-storage-account-keys-list). Till exempel om du vill visa konto åtkomstnycklar för lagring som du skapade:

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
På samma sätt som du skapar olika kataloger för att organisera logiskt ditt lokala filsystem, ska du skapa behållare i ett lagringskonto för att organisera dina diskar. Ett lagringskonto kan innehålla många behållare. Skapa en behållare med [az storage container skapa](/cli/azure/storage/container#az-storage-container-create).

I följande exempel skapas en behållare med namnet *mydisks*:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

### <a name="upload-the-vhd"></a>Ladda upp den virtuella Hårddisken
Ladda upp den anpassa disken med [az storage blob uppladdning](/cli/azure/storage/blob#az-storage-blob-upload). Du överföra och lagra dina anpassade disk som en sidblobb.

Ange din åtkomstnyckel, behållaren som du skapade i föregående steg och sökvägen till den anpassa disken på den lokala datorn:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 \
    --container-name mydisks \
    --type page \
    --file /path/to/disk/mydisk.vhd \
    --name myDisk.vhd
```
Ladda upp den virtuella Hårddisken kan det ta en stund.

### <a name="create-a-managed-disk"></a>Skapa en hanterad disk


Skapa en hanterad disk från den virtuella Hårddisken med [az disk skapa](/cli/azure/disk#az-disk-create). I följande exempel skapas en hanterad disk med namnet *myManagedDisk* från den virtuella Hårddisken som du överfört till din namngivna lagringskontot och behållaren:

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
  --source https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd
```
## <a name="option-2-copy-an-existing-vm"></a>Alternativ 2: Kopiera en befintlig virtuell dator

Du kan också skapa en anpassad virtuell dator i Azure och sedan kopiera OS-disken och koppla den till en ny virtuell dator att skapa en annan kopia. Detta är bra för testning, men om du vill använda en befintlig Azure VM som modell för flera nya virtuella datorer, skapa en *bild* i stället. Läs mer om hur du skapar en avbildning från en befintlig Azure VM, [skapa en anpassad avbildning av en Azure-dator med hjälp av CLI](tutorial-custom-images.md).

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Det här exemplet skapar en ögonblicksbild av en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup* och skapar en ögonblicksbild med namnet *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Skapa hanterad disk

Skapa en ny hanterad disk från ögonblicksbilden.

Hämta ID för ögonblicksbilden. I det här exemplet heter ögonblicksbilden *osDiskSnapshot* och den är i den *myResourceGroup* resursgrupp.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Skapa den hantera disken. I det här exemplet skapar vi en hanterad disk med namnet *myManagedDisk* från våra ögonblicksbild där disken är i standardlagring och storlek på 128 GB.

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med [az vm skapa](/cli/azure/vm#az-vm-create) och bifoga (--bifoga-os-disk) hanterad disk som OS-disk. I följande exempel skapas en virtuell dator med namnet *myNewVM* hanterade diskar som du skapade från den överförda virtuella Hårddisken:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Du bör kunna att SSH till den virtuella datorn med autentiseringsuppgifter från den Virtuella källdatorn. 

## <a name="next-steps"></a>Nästa steg
När du har förberett och överföra din anpassade virtuella hårddisk kan du läsa mer om [med Resource Manager och mallar](../../azure-resource-manager/resource-group-overview.md). Du kanske också vill [lägga till en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) till din nya virtuella datorer. Om du har program som körs på dina virtuella datorer som du behöver komma åt, måste du [öppna portar och slutpunkter](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
