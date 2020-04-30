---
title: Ladda upp en anpassad Linux-disk med Azure CLI
description: Skapa och ladda upp en virtuell hård disk (VHD) till Azure med distributions modellen Resource Manager och Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758569"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Ladda upp och skapa en virtuell Linux-dator från en anpassad disk med Azure CLI

Den här artikeln visar hur du laddar upp en virtuell hård disk (VHD) till ett Azure Storage-konto med Azure CLI och skapar virtuella Linux-datorer från den här anpassade disken. Med den här funktionen kan du installera och konfigurera en Linux-distribution på dina krav och sedan använda den virtuella hård disken för att snabbt skapa virtuella datorer i Azure.

I det här avsnittet används lagrings konton för de slutliga virtuella hård diskarna, men du kan också utföra dessa steg med [hanterade diskar](upload-vhd.md). 

## <a name="quick-commands"></a>Snabbkommandon
Om du snabbt behöver utföra uppgiften kan du använda följande avsnitt för att ladda upp en virtuell hård disk till Azure. Mer detaljerad information och kontext för varje steg hittar du i resten av dokumentet, som [börjar här](#requirements).

Kontrol lera att du har den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn `myResourceGroup`som `mystorageaccount`ingår, `mydisks`och.

Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Skapa ett lagrings konto för att lagra dina virtuella diskar med [AZ lagrings konto skapa](/cli/azure/storage/account). I följande exempel skapas ett lagrings konto `mystorageaccount`med namnet:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Visa lista över åtkomst nycklar för ditt lagrings konto med [AZ lagrings konto nycklar](/cli/azure/storage/account/keys). Anteckna `key1`:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Skapa en behållare i lagrings kontot med hjälp av lagrings nyckeln som du fick med [AZ Storage container Create](/cli/azure/storage/container). I följande exempel skapas en behållare med `mydisks` namnet med hjälp av värdet för `key1`lagrings nyckeln från:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Slutligen laddar du upp den virtuella hård disken till den behållare som du skapade med [AZ Storage BLOB upload](/cli/azure/storage/blob). Ange den lokala sökvägen till din virtuella hård `/path/to/disk/mydisk.vhd`disk under:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Ange URI: n till disken (`--image`) med [AZ VM Create](/cli/azure/vm). I följande exempel skapas en virtuell dator `myVM` med namnet med hjälp av den virtuella disk som tidigare laddats upp:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Mål lagrings kontot måste vara samma som när du laddade upp den virtuella disken till. Du måste också ange eller besvara svar på alla ytterligare parametrar som krävs av kommandot **AZ VM Create** , till exempel virtuellt nätverk, offentlig IP-adress, användar namn och SSH-nycklar. Du kan läsa mer om de [tillgängliga klassiska CLI Resource Manager-parametrarna](../azure-cli-arm-commands.md#virtual-machines).

## <a name="requirements"></a>Krav
För att utföra följande steg behöver du:

* **Linux-operativsystem installerat i en VHD-fil** – installera en [Azure-godkänd Linux-distribution](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (eller Visa [information om icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) till en virtuell disk i VHD-formatet. Det finns flera verktyg för att skapa en virtuell dator och en virtuell hård disk:
  * Installera och konfigurera [qemu](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [kvm](https://www.linux-kvm.org/page/RunningKVM)och ta hand om att använda VHD som avbildnings format. Om det behövs kan du [konvertera en avbildning](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert`hjälp av.
  * Du kan också använda Hyper-V [på Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) eller [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Det nya VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator anger du VHD som format. Vid behov kan du konvertera VHDX-diskar till en virtuell [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) hård disk [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) med hjälp av eller PowerShell-cmdleten. Dessutom stöder Azure inte överföring av dynamiska virtuella hård diskar, så du måste konvertera sådana diskar till statiska virtuella hård diskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för att gå](https://github.com/Microsoft/azure-vhd-utils-for-go) till konvertera dynamiska diskar under processen med att ladda upp till Azure.
> 
> 

* Virtuella datorer som skapats från din anpassade disk måste finnas i samma lagrings konto som själva disken
  * Skapa ett lagrings konto och en behållare för att hålla både din anpassade disk och skapade virtuella datorer
  * När du har skapat alla virtuella datorer kan du säkert ta bort disken

Kontrol lera att du har den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn `myResourceGroup`som `mystorageaccount`ingår, `mydisks`och.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Förbered disken för uppladdning
Azure har stöd för olika Linux-distributioner (se godkända [distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Följande artiklar vägleder dig genom hur du förbereder de olika Linux-distributioner som stöds i Azure:

* **[CentOS-baserade distributioner](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Andra-icke-godkända distributioner](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Mer information om hur du förbereder Linux-avbildningar för Azure finns också i **[Linux-installationsprogrammet](create-upload-generic.md#general-linux-installation-notes)** .

> [!NOTE]
> [Service avtalet för Azure-plattformen](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller endast virtuella datorer som kör Linux när en av de godkända distributionerna används med konfigurations informationen som anges under "versioner som stöds" i [Linux på Azure-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Resurs grupper sammanför logiskt alla Azure-resurser för att stödja dina virtuella datorer, till exempel virtuella nätverk och lagring. Mer information om resurs grupper finns i [Översikt över resurs grupper](../../azure-resource-manager/management/overview.md). Innan du laddar upp den anpassade disken och skapar virtuella datorer måste du först skapa en resurs grupp med [AZ Group Create](/cli/azure/group).

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett lagrings konto för din anpassade disk och virtuella datorer med [AZ lagrings konto](/cli/azure/storage/account). Alla virtuella datorer med ohanterade diskar som du skapar från den anpassade disken måste finnas i samma lagrings konto som den disken. 

I följande exempel skapas ett lagrings konto `mystorageaccount` med namnet i resurs gruppen som du skapade tidigare:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Lista lagrings konto nycklar
Azure genererar 2 512-bitars åtkomst nycklar för varje lagrings konto. Dessa åtkomst nycklar används vid autentisering till lagrings kontot, till exempel för att utföra Skriv åtgärder. Mer information om åtkomst nycklar för lagrings konton finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md). Du visar listan åtkomst nycklar med [AZ lagrings konto nycklar](/cli/azure/storage/account/keys).

Visa åtkomst nycklar för det lagrings konto som du skapade:

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

Anteckna `key1` som du kommer att använda den för att interagera med ditt lagrings konto i nästa steg.

## <a name="create-a-storage-container"></a>Skapa en lagrings behållare
På samma sätt som du skapar olika kataloger för att logiskt organisera ditt lokala fil system, skapar du behållare i ett lagrings konto för att ordna diskarna. Ett lagrings konto kan innehålla valfritt antal behållare. Skapa en behållare med [AZ Storage container Create](/cli/azure/storage/container).

I följande exempel skapas en behållare med `mydisks`namnet:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Ladda upp VHD
Ladda upp din anpassade disk med [AZ Storage BLOB upload](/cli/azure/storage/blob). Du laddar upp och lagrar den anpassade disken som en Page blob.

Ange din åtkomst nyckel, den behållare som du skapade i föregående steg och sedan sökvägen till den anpassade disken på den lokala datorn:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn
Om du vill skapa en virtuell dator med ohanterade diskar anger du URI till disken (`--image`) med [AZ VM Create](/cli/azure/vm). I följande exempel skapas en virtuell dator `myVM` med namnet med hjälp av den virtuella disk som tidigare laddats upp:

Du anger `--image` parametern med [AZ VM Create](/cli/azure/vm) för att peka på den anpassade disken. Se till `--storage-account` att matchar lagrings kontot där din anpassade disk lagras. Du behöver inte använda samma behållare som den anpassade disken för att lagra de virtuella datorerna. Se till att skapa ytterligare behållare på samma sätt som de tidigare stegen innan du laddar upp den anpassade disken.

I följande exempel skapas en virtuell dator `myVM` med namnet från din anpassade disk:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Du måste fortfarande ange eller besvara svar för alla ytterligare parametrar som krävs av kommandot **AZ VM Create** , som användar namn och SSH-nycklar.


## <a name="resource-manager-template"></a>Resource Manager-mall
Azure Resource Manager mallar är JavaScript Object Notation-filer (JSON) som definierar den miljö som du vill skapa. Mallarna är uppdelade i olika resurs leverantörer, t. ex. beräkning eller nätverk. Du kan använda befintliga mallar eller skriva egna. Läs mer om hur du [använder Resource Manager och mallar](../../azure-resource-manager/management/overview.md).

I din `Microsoft.Compute/virtualMachines` malls provider har du en `storageProfile` nod som innehåller konfigurations informationen för den virtuella datorn. De två huvud parametrarna som ska redige ras `image` är `vhd` de och URI: erna som pekar på din anpassade disk och den nya virtuella hård disken. Följande visar ett exempel på JSON för att använda en anpassad disk:

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

Du kan använda [den här befintliga mallen för att skapa en virtuell dator från en anpassad avbildning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) eller läsa om hur [du skapar egna Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md). 

När du har konfigurerat en mall använder du [AZ Group Deployment Create](/cli/azure/group/deployment) för att skapa dina virtuella datorer. Ange URI för din JSON-mall med `--template-uri` parametern:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Om du har en JSON-fil lagrad lokalt på datorn kan du använda- `--template-file` parametern i stället:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Nästa steg
När du har för berett och överfört din anpassade virtuella disk kan du läsa mer om hur du [använder Resource Manager och mallar](../../azure-resource-manager/management/overview.md). Du kanske också vill [lägga till en datadisk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) till de nya virtuella datorerna. Om du har program som körs på dina virtuella datorer som du behöver åtkomst till, måste du [öppna portar och slut punkter](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

