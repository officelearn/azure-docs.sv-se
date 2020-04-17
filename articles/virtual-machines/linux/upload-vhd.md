---
title: Ladda upp eller kopiera en anpassad virtuell Linux-dator med Azure CLI
description: Ladda upp eller kopiera en anpassad virtuell dator med hjälp av Distributionsmodellen för Resource Manager och Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 3306647078c46a7c66b3d7b257b213c7a48e690d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460434"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Skapa en Virtuell Linux-dator från en anpassad disk med Azure CLI

<!-- rename to create-vm-specialized -->

Den här artikeln visar hur du laddar upp en anpassad virtuell hårddisk (VHD) och hur du kopierar en befintlig virtuell hårddisk i Azure. Den nyskapade VIRTUELLA HÅRDDISKen används sedan för att skapa nya virtuella Linux-datorer (VIRTUELLA datorer). Du kan installera och konfigurera en Linux-distribution till dina behov och sedan använda den virtuella hårddisken för att skapa en ny virtuell Azure-dator.

Om du vill skapa flera virtuella datorer från den anpassade disken skapar du först en avbildning från den virtuella datorn eller den virtuella hårddisken. Mer information finns i [Skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).

Du har två alternativ för att skapa en anpassad disk:
* Ladda upp en virtuell hårddisk
* Kopiera en befintlig virtuell Azure-dator


## <a name="requirements"></a>Krav
För att kunna utföra följande steg måste du:

- En virtuell Linux-dator som har förberetts för användning i Azure. Avsnittet [Förbered den virtuella datorn i den](#prepare-the-vm) här artikeln beskriver hur du hittar distributionsspecifik information om hur du installerar Azure Linux Agent (waagent), som behövs för att du ska kunna ansluta till en virtuell dator med SSH.
- VHD-filen från en befintlig [Azure-stödd Linux-distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information för icke-godkända distributioner)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)till en virtuell disk i VHD-format. Det finns flera verktyg för att skapa en virtuell dator och en virtuell hårddisk:
  - Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](https://www.linux-kvm.org/page/RunningKVM)och var noga med att använda VHD som bildformat. Om det behövs kan du `qemu-img convert`konvertera en [bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med .
  - Du kan också använda Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Det nyare VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator anger du virtuell hårddisk som format. Om det behövs kan du konvertera VHDX-diskar till VHD med [qemu-img-konvertera](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Azure stöder inte överföring av dynamiska virtuella hårddiskar, så du måste konvertera sådana diskar till statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD Utilities for GO för](https://github.com/Microsoft/azure-vhd-utils-for-go) att konvertera dynamiska diskar under överföringen av dem till Azure.
> 
> 


- Se till att du har den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och du är inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index#az-login).

I följande exempel ersätter du exempelparameternamn `myResourceGroup`med `mystorageaccount`dina `mydisks`egna värden, till exempel , och .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

Azure stöder olika Linux-distributioner (se [Godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). I följande artiklar beskrivs hur du förbereder de olika Linux-distributioner som stöds på Azure:

* [CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES och openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Övrigt: Icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se även [Linux Installation Notes](create-upload-generic.md#general-linux-installation-notes) för mer allmänna tips om hur du förbereder Linux-avbildningar för Azure.

> [!NOTE]
> [Azure-plattformenSLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller endast virtuella datorer som kör Linux när en av de godkända distributionerna används med konfigurationsinformationen som anges under "Versioner som stöds" i [Linux på Azure-signerade distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Alternativ 1: Ladda upp en virtuell hårddisk

Du kan nu ladda upp VHD direkt till en hanterad disk. Instruktioner finns i [Ladda upp en virtuell hårddisk till Azure med Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Alternativ 2: Kopiera en befintlig virtuell dator

Du kan också skapa en anpassad virtuell dator i Azure och sedan kopiera OS-disken och bifoga den till en ny virtuell dator för att skapa en annan kopia. Detta är bra för testning, men om du vill använda en befintlig Azure VM som modell för flera nya virtuella datorer, skapa en *avbildning* istället. Mer information om hur du skapar en avbildning från en befintlig Virtuell Azure finns i [Skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).

Om du vill kopiera en befintlig virtuell dator till en annan region kanske du vill använda askakopior för att [skapa en kopia av en disk i en annan region](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

Annars bör du ta en ögonblicksbild av den virtuella datorn och sedan skapa en ny virtuell os-hårddisk från ögonblicksbilden.

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

I det här exemplet skapas en ögonblicksbild av en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup* och en ögonblicksbild med namnet *osDiskSnapshot*skapas .

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Skapa den hanterade disken

Skapa en ny hanterad disk från ögonblicksbilden.

Hämta ID:et för ögonblicksbilden. I det här exemplet heter ögonblicksbilden *osDiskSnapshot* och finns i resursgruppen *myResourceGroup.*

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Skapa den hanterade disken. I det här exemplet skapar vi en hanterad disk med namnet *myManagedDisk* från vår ögonblicksbild, där disken är i standardlagring och storlek på 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa din virtuella dator med [az vm skapa](/cli/azure/vm#az-vm-create) och bifoga (--attach-os-disk) den hanterade disken som OS-disken. I följande exempel skapas en virtuell dator med namnet *myNewVM* med den hanterade disk som du skapade från den uppladdade virtuella hårddisken:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Du bör kunna SSH i den virtuella datorn med autentiseringsuppgifterna från källdatorn. 

## <a name="next-steps"></a>Nästa steg
När du har förberett och laddat upp den anpassade virtuella disken kan du läsa mer om [hur du använder Resurshanteraren och mallar](../../azure-resource-manager/management/overview.md). Du kanske också vill lägga till [en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i de nya virtuella datorerna. Om du har program som körs på dina virtuella datorer som du behöver komma åt, se till att [öppna portar och slutpunkter](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
