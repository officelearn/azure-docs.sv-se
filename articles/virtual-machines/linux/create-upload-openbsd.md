---
title: Skapa och ladda upp en OpenBSD-bild
description: Lär dig hur du skapar och laddar upp en virtuell hårddisk (VHD) som innehåller OpenBSD-operativsystemet för att skapa en virtuell Azure-dator via Azure CLI
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 1ad1a66d67be7aefe4d9a7acae993e8788cbb193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066746"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Skapa och ladda upp en OpenBSD-diskavbildning till Azure
Den här artikeln visar hur du skapar och laddar upp en virtuell hårddisk (VHD) som innehåller OpenBSD-operativsystemet. När du har laddat upp den kan du använda den som din egen avbildning för att skapa en virtuell dator (VM) i Azure via Azure CLI.


## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har följande objekt:

* **En Azure-prenumeration** – Om du inte har ett konto kan du skapa en på bara några minuter. Om du har en MSDN-prenumeration läser du [Månatlig Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Annars kan du läsa om hur du [skapar ett kostnadsfritt utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI** - Se till att du har den senaste [Azure CLI](/cli/azure/install-azure-cli) installerad och inloggad på ditt Azure-konto med [az-inloggning](/cli/azure/reference-index).
* **OpenBSD operativsystem installerat i en VHD-fil** - En stöds OpenBSD operativsystem[(6,6 version AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) måste installeras på en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer. Du kan till exempel använda en virtualiseringslösning som Hyper-V för att skapa VHD-filen och installera operativsystemet. Instruktioner om hur du installerar och använder Hyper-V finns i [Installera Hyper-V och skapa en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Förbereda OpenBSD-avbildning för Azure
På den virtuella datorn där du installerade OpenBSD-operativsystemet 6.1, som lade till Hyper-V-stöd, slutför du följande procedurer:

1. Om DHCP inte är aktiverat under installationen aktiverar du tjänsten på följande sätt:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Konfigurera en seriell konsol enligt följande:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Konfigurera paketinstallation enligt följande:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Som standard `root` är användaren inaktiverad på virtuella datorer i Azure. Användare kan köra kommandon med förhöjda `doas` privilegier med kommandot på OpenBSD VM. Doas är aktiverat som standard. Mer information finns i [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Installera och konfigurera förutsättningar för Azure-agenten enligt följande:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Den senaste versionen av Azure-agenten finns alltid på [GitHub](https://github.com/Azure/WALinuxAgent/releases). Så här installerar du agenten:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > När du har installerat Azure Agent kan det vara bra att kontrollera att den körs på följande sätt:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Avetablera systemet för att rengöra det och göra det lämpligt för ometablering. Följande kommando tar också bort det senast etablerade användarkontot och tillhörande data:

    ```sh
    waagent -deprovision+user -force
    ```

Nu kan du stänga av den virtuella datorn.


## <a name="prepare-the-vhd"></a>Förbered den virtuella hårddisken
VHDX-formatet stöds inte i Azure, endast **fast virtuell hårddisk**. Du kan konvertera disken till fast VIRTUELLD-format med Hyper-V-hanteraren eller Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) cmdlet. Ett exempel är följande.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Skapa lagringsresurser och ladda upp
Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Om du vill ladda upp din virtuella hårddisk skapar du ett lagringskonto med [az storage-konto skapa](/cli/azure/storage/account). Namn på lagringskonto måste vara unika, så ange ditt eget namn. I följande exempel skapas ett lagringskonto med namnet *mystorageaccount:*

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Om du vill styra åtkomsten till lagringskontot hämtar du lagringsnyckeln med [listan över az-lagringskontonycklar](/cli/azure/storage/account/keys) enligt följande:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Om du logiskt vill separera de virtuella hårddiskar du laddar upp skapar du en behållare i lagringskontot med [az-lagringsbehållare skapa:](/cli/azure/storage/container)

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Slutligen, ladda upp din virtuella hårddisk med [az lagring blob ladda upp](/cli/azure/storage/blob) enligt följande:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Skapa virtuell dator från din virtuella dator
Du kan skapa en virtuell dator med ett [exempelskript](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) eller direkt med [az vm create](/cli/azure/vm). Om du vill ange den openbsd-hårddisk som du har laddat upp använder du parametern `--image` på följande sätt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Hämta IP-adressen för din OpenBSD VM med [az vm list-ip-adresser](/cli/azure/vm) enligt följande:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nu kan du SSH till din OpenBSD VM som vanligt:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Hyper-V-stöd på OpenBSD6.1 läser du [OpenBSD 6.1](https://www.openbsd.org/61.html) och [hyperv.4](https://man.openbsd.org/hyperv.4).

Om du vill skapa en virtuell dator från hanterad disk läser du [az disk](/cli/azure/disk). 
