---
title: Skapa och ladda upp en Linux-VHD till Azure | Microsoft Docs
description: Skapa och ladda upp en Azure virtuell hårddisk (VHD) som innehåller operativsystemet Linux med hjälp av den klassiska distributionsmodellen
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: cdbe6aa5683ecf9d8bdaf6bbf9503ddc455f03ee
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928275"
---
# <a name="creating-and-uploading-a-virtual-hard-disk-that-contains-the-linux-operating-system"></a>Skapa och ladda upp en virtuell hårddisk som innehåller operativsystemet Linux
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Du kan också [ladda upp en anpassad diskavbildning med Azure Resource Manager](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Den här artikeln visar hur du skapar och laddar upp en virtuell hårddisk (VHD) så att du kan använda den som en egen avbildning för att skapa virtuella datorer i Azure. Lär dig hur du förbereder operativsystemet så att du kan använda den för att skapa flera virtuella datorer baserat på avbildningen. 


## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har följande objekt:

* **Linux-operativsystem som är installerade i en VHD-fil** -du har installerat en [Azure-godkända Linux-distribution](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information om icke-godkända distributioner](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) på en virtuell disk i den VHD-format. Det finns flera verktyg för att skapa en virtuell dator och virtuell Hårddisk:
  * Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), och ser till att använda virtuella Hårddisken som bildformat. Om det behövs kan du [konvertera en bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert`.
  * Du kan också använda Hyper-V [i Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [på Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nyare VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator kan du ange VHD som formatet. Om det behövs kan du konvertera VHDX-diskar till virtuell Hårddisk med hjälp av [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Dessutom stöder inte Azure överför dynamiska virtuella hårddiskar, så måste du konvertera dessa diskar på statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för GO](https://github.com/Microsoft/azure-vhd-utils-for-go) konverterar dynamiska diskar under processen att överföra till Azure.

* **Azure-kommandoradsgränssnittet** -installera senast [Azure-kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) att överföra den virtuella Hårddisken.

<a id="prepimage"> </a>

## <a name="step-1-prepare-the-image-to-be-uploaded"></a>Steg 1: Förbereda avbildningen som ska laddas upp
Azure har stöd för olika Linux-distributioner (se [godkända distributioner](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). I följande artiklar hjälper dig att förbereda de olika Linux-distributioner som stöds på Azure. När du har slutfört stegen i följande handböcker hit igen när du har en VHD-fil som är redo att överföra till Azure:

* **[CentOS-baserade distributioner](../create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](../debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](../oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](../redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](../suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](../create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Övrigt – icke-godkända distributioner](../create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

> [!NOTE]
> Azure-plattformen serviceavtalet gäller för virtuella datorer som kör Linux-operativsystem endast när en av de godkända distributionerna används med konfigurationen av information som anges under ”versioner som stöds av' i [Linux på Azure-godkända distributioner](../endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Alla Linux-distributioner i Azures avbildningsgalleri är godkända distributioner med önskad konfiguration.
> 
> 

Se även de **[Linux installationsinformation](../create-upload-generic.md#general-linux-installation-notes)** mer allmänna tips om hur du förbereder Linux-avbildningar för Azure.

<a id="connect"> </a>

## <a name="step-2-prepare-the-connection-to-azure"></a>Steg 2: Förbereda anslutning till Azure
Kontrollera att du använder Azure CLI i den klassiska distributionsmodellen (`azure config mode asm`), sedan logga in på ditt konto:

```azurecli
azure login
```


<a id="upload"> </a>

## <a name="step-3-upload-the-image-to-azure"></a>Steg 3: Ladda upp avbildningen till Azure
Du behöver ett lagringskonto för att överföra VHD-filen till. Du kan antingen välja ett befintligt lagringskonto eller [skapa en ny](../../../storage/common/storage-create-storage-account.md).

Använd Azure CLI för att ladda upp avbildningen med hjälp av följande kommando:

```azurecli
azure vm image create <ImageName> `
    --blob-url <BlobStorageURL>/<YourImagesFolder>/<VHDName> `
    --os Linux <PathToVHDFile>
```

I exemplet ovan:

* **BlobStorageURL** är URL-Adressen för det lagringskonto som du planerar att använda
* **YourImagesFolder** är en behållare i blob storage där du vill lagra dina avbildningar
* **VHDName** den etikett som visas i portalen för att identifiera den virtuella hårddisken.
* **PathToVHDFile** är den fullständiga sökvägen och namnet på VHD-filen på din dator.

Följande kommando visar ett komplett exempel:

```azurecli
azure vm image create myImage `
    --blob-url https://mystorage.blob.core.windows.net/vhds/myimage.vhd `
    --os Linux /home/ahmet/myimage.vhd
```

## <a name="step-4-create-a-vm-from-the-image"></a>Steg 4: Skapa en virtuell dator från avbildningen
Du skapar en virtuell dator med `azure vm create` på samma sätt som en vanlig virtuell dator. Ange det namn du gav din avbildning i föregående steg. I följande exempel använder vi den **myImage** Bildnamn du angav i föregående steg:

```azurecli
azure vm create --userName ops --password P@ssw0rd! --vm-size Small --ssh `
    --location "West US" "myDeployedVM" myImage
```

Ange din egen användarnamn + lösenord, plats, DNS-namnet och Avbildningsnamnet för att skapa dina egna virtuella datorer.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Azure CLI-referensen för den klassiska distributionsmodellen](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

[Step 1: Prepare the image to be uploaded]:#prepimage
[Step 2: Prepare the connection to Azure]:#connect
[Step 3: Upload the image to Azure]:#upload
