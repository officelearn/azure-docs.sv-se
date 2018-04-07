---
title: Ladda upp en anpassad avbildning Linux med Azure CLI 1.0 | Microsoft Docs
description: Skapa och ladda upp en virtuell hårddisk (VHD) till Azure med en anpassad avbildning för Linux med hjälp av Resource Manager-distributionsmodellen och Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: 6eb0cae2b70e0cbb9a4fb5fcab3a58d566d0f4d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>Ladda upp och skapa en Linux VM från anpassad avbildning med hjälp av Azure CLI 1.0
Den här artikeln visar hur du överför en virtuell hårddisk (VHD) till Azure med hjälp av Resource Manager-distributionsmodellen och skapar virtuella Linux-datorer från den här anpassade avbildningen. Den här funktionen kan du installera och konfigurera en Linux-distro enligt dina behov och sedan använda den virtuella Hårddisken för att snabbt skapa virtuella Azure-datorer (VM).


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#quick-commands) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering


## <a name="quick-commands"></a>Snabbkommandon
Om du behöver utföra aktiviteten följande avsnittet beskriver grundläggande kommandon för att överföra en virtuell dator till Azure. Mer detaljerad information och kontext för varje steg finns resten av dokumentet [startar här](#requirements).

Se till att du har [Azure CLI 1.0](../../cli-install-nodejs.md) loggas och med hjälp av Resource Manager-läge:

```azurecli
azure config mode arm
```

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår `myResourceGroup`, `mystorageaccount`, och `myimages`.

Först skapa en resursgrupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i den `WestUs` plats:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

Skapa ett lagringskonto för att lagra dina virtuella diskar. I följande exempel skapas ett lagringskonto med namnet `mystorageaccount`:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

Lista över åtkomstnycklarna för ditt lagringskonto. Anteckna `key1`:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

Skapa en behållare i ditt lagringskonto med hjälp av lagringsnyckeln som du har köpt. I följande exempel skapas en behållare med namnet `myimages` med lagring nyckelvärdet från `key1`:

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

Slutligen överför den virtuella Hårddisken till den behållare som du skapade. Ange den lokala sökvägen till den virtuella Hårddisken under `/path/to/disk/mydisk.vhd`:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

Nu kan du skapa en virtuell dator från den överförda virtuella hårddisken [med hjälp av en Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd). Du kan också använda CLI genom att ange URI: N till hårddisken (`--image-urn`). I följande exempel skapas en virtuell dator med namnet `myVM` med hjälp av den virtuella disken överfört tidigare:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

Mål-lagringskontot måste vara samma som om du har överfört din virtuell disk till. Du måste också ange eller svar efterfrågar alla ytterligare parametrar som krävs av den `azure vm create` kommandot, till exempel virtuella nätverk, offentlig IP-adress, användarnamn och SSH-nycklar. Du kan läsa mer om den [tillgängliga CLI Resource Manager parametrar](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Krav
Du behöver följande för att slutföra följande steg:

* **Linux-operativsystem i en VHD-fil** -installera en [Azure-godkända Linux-distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information för icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) till en virtuell disk i VHD-format . Det finns flera verktyg för att skapa en virtuell dator och virtuell Hårddisk:
  * Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), och ser till att använda VHD som bildformat. Om det behövs kan du [konvertera en bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert`.
  * Du kan också använda Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [på Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nyare VHDX-format stöds inte i Azure. När du skapar en virtuell dator kan du ange VHD som formatet. Om det behövs, du kan konvertera VHDX-diskar på VHD med [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Dessutom stöder inte Azure överför dynamiska virtuella hårddiskar, så du måste konvertera dessa diskar till statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för att gå](https://github.com/Microsoft/azure-vhd-utils-for-go) att konvertera dynamiska diskar under processen med att ladda upp till Azure.
> 
> 

* Virtuella datorer skapas från den anpassade avbildningen måste finnas i samma lagringskonto som själva bilden
  * Skapa ett lagringskonto och en behållare för både dina anpassade avbildningen och skapade virtuella datorer
  * När du har skapat din virtuella dator, kan du ta bort bilden

Se till att du har [Azure CLI 1.0](../../cli-install-nodejs.md) loggas och med hjälp av Resource Manager-läge:

```azurecli
azure config mode arm
```

Ersätt exempel parameternamn med egna värden i följande exempel. Exempel parameternamn ingår `myResourceGroup`, `mystorageaccount`, och `myimages`.

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>Förbereda avbildningen som skulle överföras
Azure stöder olika Linux-distributioner (se [godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). I följande artiklar när du går igenom hur du förbereder de olika Linux-distributioner som stöds i Azure:

* **[CentOS-based Distributions](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Andra - icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Se även den **[Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes)** mer allmänna tips om hur du förbereder Linux avbildningar för Azure.

> [!NOTE]
> Den [Azure-plattformen SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller för virtuella datorer som kör Linux endast när en av de påtecknade distributioner används med konfigurationsinformation som anges under stöds versioner i [Linux på Azure-Endorsed distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Resursgrupper samordnar logiskt alla Azure-resurser som stöder virtuella datorer, till exempel virtuella nätverk och lagring. Läs mer om [Azure resursgrupper här](../../azure-resource-manager/resource-group-overview.md). Innan du överför din anpassade diskavbildning och skapa virtuella datorer, måste du först skapa en resursgrupp. 

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` i den `WestUS` plats:

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Virtuella datorer lagras som sidblobbar i ett lagringskonto. Läs mer om [Azure-blobblagring här](../../storage/common/storage-introduction.md#blob-storage). Du skapar ett lagringskonto för dina anpassade diskavbildning och virtuella datorer. Virtuella datorer som du skapar från din anpassade diskavbildning måste vara i samma lagringskonto som den bilden.

I följande exempel skapas ett lagringskonto med namnet `mystorageaccount` i resursgruppen som du skapade tidigare:

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>Lista nycklar för lagringskonto
Två 512-bitars åtkomstnycklar för varje lagringskonto genererar Azure. Åtkomstnycklarna används vid autentisering till lagringskontot, såsom för att utföra skrivåtgärder. Läs mer om [hantera åtkomst till lagring här](../../storage/common/storage-create-storage-account.md#manage-your-storage-account). Du kan visa snabbtangenter med den `azure storage account keys list` kommando.

Visa åtkomstnycklar för lagringskontot som du skapade:

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
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
Anteckna `key1` eftersom du ska använda för att interagera med ditt lagringskonto i nästa steg.

## <a name="create-a-storage-container"></a>Skapa en lagringsbehållare
På samma sätt som du skapar olika kataloger för att organisera logiskt det lokala filsystemet, kan du skapa behållare i ett lagringskonto för att organisera dina virtuella diskar och bilder. Ett lagringskonto kan innehålla valfritt antal behållare. 

I följande exempel skapas en behållare med namnet `myimages`, ange snabbtangent hämtades i föregående steg (`key1`):

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>Överför VHD
Nu kan du faktiskt överföra din egen avbildning. Som med alla virtuella diskar som används av virtuella datorer kan du ladda upp och lagra din egen avbildning som en sidblobb.

Ange din snabbtangent, den behållare som du skapade i föregående steg och sökvägen till den anpassade diskavbildning på den lokala datorn:

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>Skapa virtuell dator från en anpassad avbildning
När du skapar virtuella datorer från din anpassade diskavbildning ange URI till disk image. Se till att målet lagring kontot matchar där din anpassade diskavbildning lagras. Du kan skapa den virtuella datorn med hjälp av Azure CLI eller Resource Manager JSON-mall.

### <a name="create-a-vm-using-the-azure-cli"></a>Skapa en virtuell dator med hjälp av Azure CLI
Du anger den `--image-urn` parameter med det `azure vm create` kommando för att peka på din egen avbildning. Se till att `--storage-account-name` matchar det lagringskonto där din anpassade diskavbildning lagras. Du behöver inte använda samma behållare som anpassade disk image för att lagra dina virtuella datorer. Se till att skapa några ytterligare behållare på samma sätt som de föregående stegen innan du laddar upp din anpassade diskavbildningar.

I följande exempel skapas en virtuell dator med namnet `myVM` från din egen avbildning:

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

Du måste ange eller svar efterfrågar alla ytterligare parametrar som krävs av den `azure vm create` kommandot, till exempel virtuella nätverk, offentlig IP-adress, användarnamn och SSH-nycklar. Läs mer om den [tillgängliga CLI Resource Manager parametrar](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

### <a name="create-a-vm-using-a-json-template"></a>Skapa en virtuell dator med en JSON-mall
Azure Resource Manager-mallar är JavaScript Object Notation (JSON) filer som definierar den miljö du vill skapa. Mallarna är uppdelade att olika resursproviders, till exempel beräkning eller nätverket. Du kan använda befintliga mallar eller skriva egna. Läs mer om [med hjälp av hanteraren för filserverresurser och mallar](../../azure-resource-manager/resource-group-overview.md).

I den `Microsoft.Compute/virtualMachines` providern av mallen som du har en `storageProfile` nod som innehåller konfigurationsinformation för den virtuella datorn. Så här redigerar du två huvudsakliga parametrarna är den `image` och `vhd` URI: er som pekar på din egen avbildning och din nya VM virtuell disk. Nedan visas ett exempel på JSON för att använda en anpassad avbildning:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Du kan använda [denna befintlig mall för att skapa en virtuell dator från en anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) eller läser du om [skapa egna mallar för Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

När du har en mall kan du skapa dina virtuella datorer med hjälp av den `azure group deployment create` kommando. Ange URI för JSON-mall med det `--template-uri` parameter:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

Om du har en JSON-fil som lagras lokalt på datorn, kan du använda den `--template-file` parameter i stället:

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Nästa steg
När du har förberett och överföra din egen virtuell disk, kan du läsa mer om [med hjälp av hanteraren för filserverresurser och mallar](../../azure-resource-manager/resource-group-overview.md). Du kan också [lägger till en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) till din nya virtuella datorer. Om du har program som körs på din virtuella dator som du behöver åtkomst till [öppna portar och slutpunkter](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

