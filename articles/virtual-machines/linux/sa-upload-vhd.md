---
title: Ladda upp en anpassad Linux-disk med Azure CLI
description: Skapa och ladda upp en virtuell hårddisk (VHD) till Azure med hjälp av Resurshanterarens distributionsmodell och Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758569"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Ladda upp och skapa en virtuell Linux-dator från anpassad disk med Azure CLI

Den här artikeln visar hur du laddar upp en virtuell hårddisk (VHD) till ett Azure-lagringskonto med Azure CLI och skapar virtuella Linux-datorer från den här anpassade disken. Med den här funktionen kan du installera och konfigurera en Linux-distribution till dina behov och sedan använda den virtuella hårddisken för att snabbt skapa virtuella Azure-datorer (VIRTUELLA datorer).

Det här avsnittet använder lagringskonton för de slutliga virtuella hårddiskarna, men du kan också göra dessa steg med [hanterade diskar](upload-vhd.md). 

## <a name="quick-commands"></a>Snabbkommandon
Om du snabbt behöver utföra uppgiften beskrivs i följande avsnitt baskommandona för att överföra en virtuell hårddisk till Azure. Mer detaljerad information och sammanhang för varje steg finns resten av dokumentet, [med början här](#requirements).

Se till att du har den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn `myResourceGroup` `mystorageaccount`ingår `mydisks`, och .

Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Skapa ett lagringskonto för att lagra dina virtuella diskar med [az storage-konto skapa](/cli/azure/storage/account). I följande exempel skapas `mystorageaccount`ett lagringskonto med namnet :

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Lista åtkomstnycklarna för ditt lagringskonto med [listan över az-lagringskontonycklar](/cli/azure/storage/account/keys). Anteckna: `key1`

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Skapa en behållare i ditt lagringskonto med hjälp av lagringsnyckeln som du fick med [az-lagringsbehållare skapa](/cli/azure/storage/container). I följande exempel skapas `mydisks` en behållare med `key1`namnet med hjälp av lagringsnyckelvärdet från:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Slutligen, ladda upp din virtuella hårddisk till behållaren du skapade med [az lagring blob ladda upp](/cli/azure/storage/blob). Ange den lokala sökvägen `/path/to/disk/mydisk.vhd`till den virtuella hårddisken under :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Ange URI till disken (`--image`) med az vm [skapa](/cli/azure/vm). I följande exempel skapas `myVM` en virtuell dator med namnet med den virtuella disk som tidigare överförts:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Mållagringskontot måste vara detsamma som där du laddade upp den virtuella disken till. Du måste också ange, eller svara på, alla ytterligare parametrar som krävs av kommandot **az vm create,** till exempel virtuellt nätverk, offentlig IP-adress, användarnamn och SSH-nycklar. Du kan läsa mer om [de tillgängliga klassiska CLI Resource Manager-parametrarna](../azure-cli-arm-commands.md#virtual-machines).

## <a name="requirements"></a>Krav
För att kunna utföra följande steg behöver du:

* **Linux-operativsystem installerat i en VHD-fil** - Installera en [Azure-stödd Linux-distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller se [information för icke-godkända distributioner)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)till en virtuell disk i VHD-format. Det finns flera verktyg för att skapa en virtuell dator och en virtuell hårddisk:
  * Installera och konfigurera [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [KVM](https://www.linux-kvm.org/page/RunningKVM)och var noga med att använda VHD som bildformat. Om det behövs kan du `qemu-img convert`konvertera en [bild](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med .
  * Du kan också använda Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Det nyare VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator anger du virtuell hårddisk som format. Om det behövs kan du konvertera VHDX-diskar till VHD med eller [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Azure stöder inte vidare överföring av dynamiska virtuella hårddiskar, så du måste konvertera sådana diskar till statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD Utilities for GO för](https://github.com/Microsoft/azure-vhd-utils-for-go) att konvertera dynamiska diskar under överföringen till Azure.
> 
> 

* Virtuella datorer som skapas från den anpassade disken måste finnas i samma lagringskonto som själva disken
  * Skapa ett lagringskonto och en behållare för att lagra både din anpassade disk och skapade virtuella datorer
  * När du har skapat alla dina virtuella datorer kan du på ett säkert sätt ta bort disken

Se till att du har den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamn `myResourceGroup` `mystorageaccount`ingår `mydisks`, och .

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Förbered disken som ska laddas upp
Azure stöder olika Linux-distributioner (se [Godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Följande artiklar hjälper dig att förbereda de olika Linux-distributioner som stöds på Azure:

* **[CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & öppnaSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Övrigt - Icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Se även **[Linux Installation Notes](create-upload-generic.md#general-linux-installation-notes)** för mer allmänna tips om hur du förbereder Linux-avbildningar för Azure.

> [!NOTE]
> [Azure-plattformenSLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller endast virtuella datorer som kör Linux när en av de godkända distributionerna används med konfigurationsinformationen som anges under "Versioner som stöds" i [Linux på Azure-signerade distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Resursgrupper samlar logiskt alla Azure-resurser för att stödja dina virtuella datorer, till exempel virtuella nätverk och lagring. Mer information resursgrupper finns i [resursgrupper översikt](../../azure-resource-manager/management/overview.md). Innan du laddar upp den anpassade disken och skapar virtuella datorer måste du först skapa en resursgrupp med [az-gruppskapa](/cli/azure/group).

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett lagringskonto för din anpassade disk och virtuella datorer med [az storage-konto skapa](/cli/azure/storage/account). Alla virtuella datorer med ohanterade diskar som du skapar från din anpassade disk måste finnas i samma lagringskonto som disken. 

I följande exempel skapas `mystorageaccount` ett lagringskonto med namnet i resursgruppen som tidigare skapats:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Lista lagringskontonycklar
Azure genererar två 512-bitars åtkomstnycklar för varje lagringskonto. Dessa åtkomstnycklar används vid autentisering till lagringskontot, till exempel för att utföra skrivåtgärder. Mer information om åtkomstnycklar för lagringskonto finns i [Hantera åtkomstnycklar för lagringskonto](../../storage/common/storage-account-keys-manage.md). Du kan visa åtkomstnycklarna med listan över [az-lagringskontonycklar](/cli/azure/storage/account/keys).

Visa åtkomstnycklarna för lagringskontot som du skapade:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Utdatan liknar följande:

```output
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Anteckna `key1` när du använder den för att interagera med ditt lagringskonto i nästa steg.

## <a name="create-a-storage-container"></a>Skapa en lagringsbehållare
På samma sätt som du skapar olika kataloger för att logiskt organisera ditt lokala filsystem skapar du behållare i ett lagringskonto för att ordna diskarna. Ett lagringskonto kan innehålla valfritt antal behållare. Skapa en behållare med [az lagringsbehållare skapa](/cli/azure/storage/container).

I följande exempel skapas `mydisks`en behållare med namnet :

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Ladda upp VHD
Nu ladda upp din anpassade disk med [az lagring blob ladda upp](/cli/azure/storage/blob). Du laddar upp och lagrar din anpassade disk som en sidblob.

Ange åtkomstnyckeln, behållaren som du skapade i föregående steg och sökvägen till den anpassade disken på den lokala datorn:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn
Om du vill skapa en virtuell dator med ohanterade`--image`diskar anger du URI:n till disken ( ) med [az vm create](/cli/azure/vm). I följande exempel skapas `myVM` en virtuell dator med namnet med den virtuella disk som tidigare överförts:

Du anger `--image` parametern med [az vm skapa](/cli/azure/vm) för att peka på din anpassade disk. Kontrollera `--storage-account` att matchar lagringskontot där den anpassade disken lagras. Du behöver inte använda samma behållare som den anpassade disken för att lagra dina virtuella datorer. Se till att skapa ytterligare behållare på samma sätt som de tidigare stegen innan du laddar upp den anpassade disken.

I följande exempel skapas `myVM` en virtuell dator som heter från den anpassade disken:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Du måste fortfarande ange, eller svara på, alla ytterligare parametrar som krävs av **az vm skapa** kommandot såsom användarnamn och SSH nycklar.


## <a name="resource-manager-template"></a>Resource Manager-mall
Azure Resource Manager-mallar är JSON-filer (JavaScript Object Notation) som definierar den miljö du vill skapa. Mallarna är uppdelade på olika resursleverantörer, till exempel beräkning eller nätverk. Du kan använda befintliga mallar eller skriva egna. Läs mer om [hur du använder Resurshanteraren och mallar](../../azure-resource-manager/management/overview.md).

I `Microsoft.Compute/virtualMachines` mallens leverantör har `storageProfile` du en nod som innehåller konfigurationsinformationen för den virtuella datorn. De två huvudparametrar `image` `vhd` som ska redigeras är de och URI:er som pekar på den anpassade disken och den nya virtuella datorns virtuella disk. Följande visar ett exempel på JSON för att använda en anpassad disk:

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

Du kan använda [den här befintliga mallen för att skapa en virtuell dator från en anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) eller läsa om [hur du skapar egna Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md). 

När du har konfigurerat en mall använder du [az-gruppdistributionsskapande för](/cli/azure/group/deployment) att skapa dina virtuella datorer. Ange URI för din JSON-mall med parametern: `--template-uri`

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Om du har en JSON-fil lagrad lokalt `--template-file` på datorn kan du använda parametern i stället:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Nästa steg
När du har förberett och laddat upp den anpassade virtuella disken kan du läsa mer om [hur du använder Resurshanteraren och mallar](../../azure-resource-manager/management/overview.md). Du kanske också vill lägga till [en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) i de nya virtuella datorerna. Om du har program som körs på dina virtuella datorer som du behöver komma åt, se till att [öppna portar och slutpunkter](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

