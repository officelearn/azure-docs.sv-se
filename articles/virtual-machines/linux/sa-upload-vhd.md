---
title: Ladda upp en anpassad Linux-disk med Azure CLI | Microsoft Docs
description: Skapa och ladda upp en virtuell hårddisk (VHD) till Azure med hjälp av Resource Manager-distributionsmodellen och Azure CLI
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
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: 01d3a20022972b0e18de02bd2730ca31e57cd77a
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755048"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Ladda upp och skapa en Linux VM från anpassad disk med Azure CLI

Den här artikeln visar hur du överför en virtuell hårddisk (VHD) till ett Azure storage-konto med Azure CLI och skapa virtuella Linux-datorer från den här anpassade disken. Den här funktionen kan du installera och konfigurera en Linux-distribution enligt dina behov och sedan använda den virtuella Hårddisken för att snabbt skapa virtuella Azure-datorer (VM).

Det här avsnittet använder storage-konton för de sista virtuella hårddiskarna, men du kan också utföra dessa steg med [hanterade diskar](upload-vhd.md). 

## <a name="quick-commands"></a>Snabbkommandon
Om du behöver att snabbt utföra uppgiften, de följande avsnittet beskriver grundläggande kommandon för att ladda upp en virtuell Hårddisk till Azure. Mer detaljerad information och kontext för varje steg finns resten av dokumentet, [börjar visningen här](#requirements).

Se till att du har senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen ingår `myResourceGroup`, `mystorageaccount`, och `mydisks`.

Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Skapa ett lagringskonto för att lagra dina virtuella diskar med [az storage-konto skapar](/cli/azure/storage/account). I följande exempel skapas ett lagringskonto med namnet `mystorageaccount`:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Lista över åtkomstnycklarna för ditt lagringskonto med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys). Anteckna `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Skapa en behållare i ditt lagringskonto med hjälp av lagringskontots åtkomstnyckel du fick med [az storage container skapa](/cli/azure/storage/container). I följande exempel skapas en behållare med namnet `mydisks` med storage nyckelvärdet från `key1`:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Slutligen kan överföra en virtuell Hårddisk till den behållare du skapade med [az storage blob uppladdning](/cli/azure/storage/blob). Ange den lokala sökvägen till en virtuell Hårddisk under `/path/to/disk/mydisk.vhd`:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Ange URI: N på disken (`--image`) med [az vm skapa](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet `myVM` tidigare med hjälp av den virtuella disken har laddats upp:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Mållagringskontot måste vara samma som där du laddade upp din virtuella hårddisk till. Du måste också ange eller svar som frågar efter, alla ytterligare parametrar som krävs av den **az vm skapa** kommandot, till exempel virtuella nätverk, offentlig IP-adress, användarnamn och SSH-nycklar. Du kan läsa mer om den [tillgängliga CLI Resource Manager-parametrar](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines).

## <a name="requirements"></a>Krav
Du behöver följande för att slutföra följande steg:

* **Linux-operativsystem som är installerade i en VHD-fil** -installera en [Azure-godkända Linux-distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information om icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) på en virtuell disk i VHD-format . Det finns flera verktyg för att skapa en virtuell dator och virtuell Hårddisk:
  * Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](http://www.linux-kvm.org/page/RunningKVM), och ser till att använda virtuella Hårddisken som bildformat. Om det behövs kan du [konvertera en bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert`.
  * Du kan också använda Hyper-V [i Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [på Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nyare VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator kan du ange VHD som formatet. Om det behövs kan du konvertera VHDX-diskar till virtuell Hårddisk med hjälp av [ `qemu-img convert` ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [ `Convert-VHD` ](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Dessutom stöder inte Azure överför dynamiska virtuella hårddiskar, så måste du konvertera dessa diskar på statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för GO](https://github.com/Microsoft/azure-vhd-utils-for-go) konverterar dynamiska diskar under processen att överföra till Azure.
> 
> 

* Virtuella datorer som skapas från den anpassa disken måste finnas i samma lagringskonto som själva disken
  * Skapa ett lagringskonto och en behållare för både din anpassade disk och skapade virtuella datorer
  * När du har skapat dina virtuella datorer, kan du ta bort disken

Se till att du har senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

I följande exempel, ersätter du exempel parameternamn med dina egna värden. Parametern exempelnamnen ingår `myResourceGroup`, `mystorageaccount`, och `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Förbereda disken som ska laddas upp
Azure har stöd för olika Linux-distributioner (se [godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). I följande artiklar hjälper dig att förbereda de olika Linux-distributioner som stöds på Azure:

* **[CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Övrigt – icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Se även de **[Linux installationsinformation](create-upload-generic.md#general-linux-installation-notes)** mer allmänna tips om hur du förbereder Linux-avbildningar för Azure.

> [!NOTE]
> Den [Azure-plattformen SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller för datorer som kör Linux endast när en av de godkända distributionerna används med konfigurationsinformation som anges under ”versioner som stöds av' [Linux på Azure-godkända Distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Resursgrupper sammanför logiskt alla Azure-resurser för dina virtuella datorer, till exempel virtuella nätverk och lagring. Resursgrupper för mer information, se [resursgrupperna översikt](../../azure-resource-manager/resource-group-overview.md). Innan du överför din anpassade disk och skapa virtuella datorer, måste du först skapa en resursgrupp med [az gruppen skapa](/cli/azure/group).

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett lagringskonto för din anpassade disk och virtuella datorer med [az storage-konto skapar](/cli/azure/storage/account). Alla virtuella datorer med ohanterade diskar som du skapar från din anpassade disken måste vara i samma lagringskonto som disken. 

I följande exempel skapas ett lagringskonto med namnet `mystorageaccount` i resursgruppen som skapades tidigare:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Lista nycklar för lagringskonto
Azure skapar två 512-bitars åtkomstnycklar för varje lagringskonto. Åtkomstnycklarna används vid autentisering till lagringskontot, till exempel för att utföra skrivåtgärder. Läs mer om [hantera åtkomst till lagring här](../../storage/common/storage-account-manage.md#access-keys). Du visar åtkomstnycklarna med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys).

Visa åtkomstnycklar för det lagringskonto du skapade:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
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
På samma sätt som du skapar olika kataloger för att organisera logiskt ditt lokala filsystem, skapa behållare i ett lagringskonto för att organisera dina diskar. Ett lagringskonto kan innehålla valfritt antal behållare. Skapa en behållare med [az storage container skapa](/cli/azure/storage/container).

I följande exempel skapas en behållare med namnet `mydisks`:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Ladda upp VHD
Ladda upp den anpassa disken med [az storage blob uppladdning](/cli/azure/storage/blob). Du överför och lagrar anpassade disken som en sidblobb.

Ange din åtkomstnyckel, behållaren som du skapade i föregående steg och sökvägen till den anpassa disken på den lokala datorn:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn
Ange URI: N på disken för att skapa en virtuell dator med ohanterade diskar (`--image`) med [az vm skapa](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet `myVM` tidigare med hjälp av den virtuella disken har laddats upp:

Du anger den `--image` parameter med [az vm skapa](/cli/azure/vm) så att den pekar till din anpassade disk. Se till att `--storage-account` matchar det lagringskonto där din anpassade disk lagras. Du behöver inte använda samma behållare som den anpassa disken för att lagra dina virtuella datorer. Se till att skapa några ytterligare behållare på samma sätt som de föregående stegen innan du laddar upp din anpassade disk.

I följande exempel skapas en virtuell dator med namnet `myVM` från den anpassa disken:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Du måste fortfarande ange eller svar som frågar efter, alla ytterligare parametrar som krävs av den **az vm skapa** kommandot, till exempel användarnamn och SSH-nycklar.


## <a name="resource-manager-template"></a>Resource Manager-mall
Azure Resource Manager-mallar är JavaScript Object Notation (JSON) filer som definierar den miljö som du vill skapa. Mallarna är uppdelade i att olika resursproviders, till exempel beräkning eller nätverk. Du kan använda befintliga mallar eller Skriv din egen. Läs mer om [med Resource Manager och mallar](../../azure-resource-manager/resource-group-overview.md).

I den `Microsoft.Compute/virtualMachines` leverantör av din mall måste du ha en `storageProfile` nod som innehåller konfigurationsinformationen för den virtuella datorn. Så här redigerar du två huvudsakliga parametrarna är den `image` och `vhd` URI: er som pekar på din anpassade disk och din nya VM virtuell disk. Nedan visas ett exempel på JSON för att använda en anpassad disk:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Du kan använda [den här befintlig mall för att skapa en virtuell dator från en anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) eller läser du om [skapa egna mallar för Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md). 

När du har en mall kan du använda [az group deployment skapa](/cli/azure/group/deployment) att skapa dina virtuella datorer. Ange URI för JSON-mall med det `--template-uri` parameter:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Om du har en JSON-fil som lagras lokalt på din dator kan du använda den `--template-file` parameter i stället:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Nästa steg
När du har förberett och överföra din anpassade virtuella hårddisk kan du läsa mer om [med Resource Manager och mallar](../../azure-resource-manager/resource-group-overview.md). Du kanske också vill [lägga till en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) till din nya virtuella datorer. Om du har program som körs på dina virtuella datorer som du behöver komma åt, måste du [öppna portar och slutpunkter](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

