---
title: Skapa och ladda upp en Linux VHD till Azure | Microsoft Docs
description: Skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller Linux-operativsystem som använder den klassiska distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 8058ff98-db03-4309-9bf4-69842bd64dd4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: iainfou
ms.openlocfilehash: 1ba568eeaf3bbc3d786cc48e54404aa65a00fecc
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841902"
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Skapa och ladda upp en virtuell hårddisk som innehåller operativsystemet Linux
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Du kan också [ladda upp en anpassad avbildning med Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Den här artikeln visar hur du skapar och överför en virtuell hårddisk (VHD) så att du kan använda den som en egen avbildning för att skapa virtuella datorer i Azure. Lär dig hur du förbereder operativsystemet så du kan använda för att skapa flera virtuella datorer baserat på avbildningen. 


## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har följande objekt:

* **Linux-operativsystem i en VHD-fil** -du har installerat en [Azure-godkända Linux-distribution](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information för icke-godkända distributioner](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) till en virtuell disk i den VHD-format. Det finns flera verktyg för att skapa en virtuell dator och virtuell Hårddisk:
  * Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), och ser till att använda VHD som bildformat. Om det behövs kan du [konvertera en bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert`.
  * Du kan också använda Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [på Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nyare VHDX-format stöds inte i Azure. När du skapar en virtuell dator kan du ange VHD som formatet. Om det behövs, du kan konvertera VHDX-diskar på VHD med [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Dessutom stöder inte Azure överför dynamiska virtuella hårddiskar, så du måste konvertera dessa diskar till statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för att gå](https://github.com/Microsoft/azure-vhd-utils-for-go) att konvertera dynamiska diskar under processen med att ladda upp till Azure.

* **Azure-kommandoradsgränssnittet** -installera senast [Azure-kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) att överföra den virtuella Hårddisken.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Steg 1: Förbered avbildningen som skulle överföras
Azure stöder olika Linux-distributioner (se [godkända distributioner](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). I följande artiklar leder dig igenom hur du förbereder de olika Linux-distributioner som stöds på Azure. När du har slutfört stegen i följande guider återvända hit när du har en VHD-fil som är redo att överföra till Azure:

* **[CentOS-based Distributions](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Andra - icke-godkända distributioner](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Azure-plattformen SLA gäller för virtuella datorer som kör Operativsystemet Linux endast när en av de påtecknade distributioner används med konfigurationen av information som anges under stöds versioner i [Linux på Azure-Endorsed distributioner](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Alla Linux-distributioner i galleriet Azure-avbildning är påtecknade distributioner med önskad konfiguration.
> 
> 

Se även den **[Linux installationsinformation](../create-upload-generic.md#general-linux-installation-notes)** mer allmänna tips om hur du förbereder Linux avbildningar för Azure.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Steg 2: Förbered anslutningen till Azure
Kontrollera att du använder Azure CLI i den klassiska distributionsmodellen (`azure config mode asm`), sedan logga in på ditt konto:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Steg 3: Överför avbildningen till Azure
Du behöver ett lagringskonto för att överföra VHD-filen till. Du kan antingen välja ett befintligt lagringskonto eller [skapa en ny](../../../storage/common/storage-create-storage-account.md).

Använda Azure CLI för att överföra avbildningen med hjälp av följande kommando:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

I det förra exemplet:

* **BlobStorageURL** är URL-Adressen för det lagringskontot som du planerar att använda
* **YourImagesFolder** är behållare i blob-lagring där du vill lagra dina bilder
* **VHDName** är etiketten som visas i portalen för att identifiera den virtuella hårddisken.
* **PathToVHDFile** är den fullständiga sökvägen och namnet på VHD-filen på din dator.

Följande kommando visar en komplett exempel:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Steg 4: Skapa en virtuell dator från avbildningen
Du skapar en virtuell dator med hjälp av `azure vm create` på samma sätt som vanliga virtuell dator. Ange det namn du gav avbildningen i föregående steg. I följande exempel tar vi använda den **myImage** Avbildningsnamnet som anges i föregående steg:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Ange egna användarnamn + lösenord, plats, DNS-namn och namn om du vill skapa egna virtuella datorer.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure CLI-referens för Azure klassiska distributionsmodellen](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
