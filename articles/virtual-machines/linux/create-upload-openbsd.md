---
title: Skapa och ladda upp en OpenBSD VM-avbildning till Azure | Microsoft Docs
description: Lär dig hur du skapar och överför en virtuell hårddisk (VHD) som innehåller OpenBSD operativsystem om du vill skapa en virtuell Azure-dator via Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 23ca965b12afc8931a67d32e3a11a4aa9777a5d5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Skapa och ladda upp en diskavbildning OpenBSD till Azure
Den här artikeln visar hur du skapar och överför en virtuell hårddisk (VHD) som innehåller operativsystemet OpenBSD. När du har överfört kan du använda den som en egen avbildning för att skapa en virtuell dator (VM) i Azure med Azure CLI.


## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har följande objekt:

* **En Azure-prenumeration** -om du inte har ett konto kan du skapa en på bara några minuter. Om du har en MSDN-prenumeration, se [månatliga Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Annars Lär dig hur du [skapa ett kostnadsfritt utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI 2.0** -Kontrollera att du har senast [Azure CLI 2.0](/cli/azure/install-azure-cli) installerad och inloggad på ditt Azure-konto med [az inloggningen](/cli/azure/reference-index#az_login).
* **OpenBSD operativsystem i en VHD-fil** -en OpenBSD operativsystem som stöds (version 6.1) måste installeras på en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer. Du kan till exempel använda en virtualiseringslösning som Hyper-V för att skapa VHD-filen och installera operativsystemet. Instruktioner om hur du installerar och använder Hyper-V finns i [installera Hyper-V och skapa en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Förbereda OpenBSD bild för Azure
Stöd för på den virtuella datorn där du installerade OpenBSD operativsystemet 6.1, som läggs till Hyper-V, Slutför följande procedurer:

1. Om DHCP inte är aktiverad under installationen, aktivera tjänsten på följande sätt:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Ställ in en seriekonsolen enligt följande:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Konfigurera installationen enligt följande:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Som standard den `root` användaren är inaktiverad på virtuella datorer i Azure. Användare kan köra kommandon med utökade privilegier med hjälp av den `doas` på OpenBSD VM. Doas är aktiverad som standard. Mer information finns i [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Installera och konfigurera krav för Azure-agenten på följande sätt:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Den senaste versionen av Azure-agenten alltid finns på [Github](https://github.com/Azure/WALinuxAgent/releases). Installera agenten på följande sätt:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > När du har installerat Azure-agenten är en bra idé att kontrollera att den körs på följande sätt:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Ta bort etableringen av systemet för att rensa den och gör den lämplig för reprovisioning. Följande kommando tar också bort det senaste kontot för etablerad användare och associerade data:

    ```sh
    waagent -deprovision+user -force
    ```

Nu kan du stänga av den virtuella datorn.


## <a name="prepare-the-vhd"></a>Förbereda den virtuella Hårddisken
VHDX-format stöds inte i Azure, endast **fast virtuell Hårddisk**. Du kan konvertera disken till fast VHD-format med hjälp av Hyper-V Manager eller Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) cmdlet. Ett exempel är följande.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Skapa storage-resurser och ladda upp
Skapa först en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Överför den virtuella Hårddisken genom att skapa ett lagringskonto med [az storage-konto skapar](/cli/azure/storage/account#az_storage_account_create). Lagringskontonamn måste vara unikt, så ange dina egna namn. I följande exempel skapas ett lagringskonto med namnet *mittlagringskonto*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

För att styra åtkomst till lagringskontot, Hämta nyckel för säkerhetslagring med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys#az_storage_account_keys_list) på följande sätt:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Logiskt separata de virtuella hårddiskarna som du laddar upp, skapa en behållare i lagringskontot med [az lagringsbehållaren skapa](/cli/azure/storage/container#az_storage_container_create):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Slutligen överför den virtuella Hårddisken med [az storage blob överför](/cli/azure/storage/blob#az_storage_blob_upload) på följande sätt:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Skapa virtuell dator från den virtuella Hårddisken
Du kan skapa en virtuell dator med en [exempel på skript](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) eller direkt med [az vm skapa](/cli/azure/vm#az_vm_create). Ange OpenBSD VHD du överförde den `--image` parameter på följande sätt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Skaffa IP-adressen för din OpenBSD VM med [az vm lista-ip-adresser](/cli/azure/vm#list-ip-addresses) på följande sätt:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nu kan du SSH för din OpenBSD VM som vanligt:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Hyper-V-stöd på OpenBSD6.1 läsa [OpenBSD 6.1](https://www.openbsd.org/61.html) och [hyperv.4](http://man.openbsd.org/hyperv.4).

Om du vill skapa en virtuell dator från hanterade diskar, kan du läsa [az disk](/cli/azure/disk). 
