---
title: Skapa och ladda upp en OpenBSD VM-avbildning till Azure | Microsoft Docs
description: Lär dig hur du skapar och laddar upp en virtuell hårddisk (VHD) som innehåller OpenBSD operativsystem om du vill skapa en Azure virtuell dator med Azure CLI
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
ms.openlocfilehash: 332382282c2b55b52bb23f278a25868c09360619
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55729361"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Skapa och ladda upp en diskavbildning i OpenBSD till Azure
Den här artikeln visar hur du skapar och laddar upp en virtuell hårddisk (VHD) som innehåller OpenBSD-operativsystem. När du har överfört kan du använda den som en egen avbildning för att skapa en virtuell dator (VM) i Azure med Azure CLI.


## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har följande objekt:

* **En Azure-prenumeration** -om du inte har ett konto kan du skapa en på bara några minuter. Om du har en MSDN-prenumeration, se [månatliga Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). I annat fall Lär dig hur du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI** – Kontrollera att du har senast [Azure CLI](/cli/azure/install-azure-cli) installerat och loggat in på Azure-kontot med [az-inloggning](/cli/azure/reference-index).
* **OpenBSD operativsystem är installerat i en VHD-fil** – en stöds OpenBSD operativsystem ([version 6.1 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/)) måste vara installerad på en virtuell hårddisk. Det finns flera olika verktyg för att skapa VHD-filer. Du kan till exempel använda en virtualiseringslösning, till exempel Hyper-V för att skapa VHD-filen och installera operativsystemet. Anvisningar om hur du installerar och använder Hyper-V finns i [installera Hyper-V och skapa en virtuell dator](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Förbered OpenBSD-avbildning för Azure
På den virtuella datorn där du installerade OpenBSD operativsystemet 6.1, som har lagts till Hyper-V stöder, Slutför följande procedurer:

1. Om DHCP inte är aktiverad under installationen kan du aktivera tjänsten på följande sätt:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Konfigurera en seriell konsol enligt följande:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Konfigurera installationen enligt följande:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Som standard den `root` användaren är inaktiverad på virtuella datorer i Azure. Användare kan köra kommandon med utökade privilegier med hjälp av den `doas` på OpenBSD VM. Doas är aktiverat som standard. Mer information finns i [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Installera och konfigurera krav för Azure-agenten på följande sätt:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Den senaste versionen av Azure-agenten finns på alltid [GitHub](https://github.com/Azure/WALinuxAgent/releases). Installera agenten på följande sätt:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > När du har installerat Azure-agenten är det en bra idé att kontrollera att den körs på följande sätt:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Ta bort etableringen av systemet för att rensa den och gör den lämplig för reprovisioning. Följande kommando tar också bort senast etablerade användarkontot och associerade data:

    ```sh
    waagent -deprovision+user -force
    ```

Du kan nu stänga av den virtuella datorn.


## <a name="prepare-the-vhd"></a>Förbereda den virtuella Hårddisken
VHDX-formatet stöds inte i Azure, endast **fast virtuell Hårddisk**. Du kan konvertera disken till fast VHD-format med hjälp av Hyper-V Manager eller Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) cmdlet. Ett exempel är som följande.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Skapa lagringsresurser och ladda upp
Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Om du vill överföra en virtuell Hårddisk, skapa ett lagringskonto med [az storage-konto skapar](/cli/azure/storage/account). Lagringskontonamn måste vara unikt, så ange egna namn. I följande exempel skapas ett lagringskonto med namnet *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

För att styra åtkomsten till lagringskontot, hämta lagringsnyckeln med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys) på följande sätt:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

För att logiskt separera de virtuella hårddiskarna som du laddar upp, skapa en behållare i lagringskontot med [az storage container skapa](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Slutligen kan överföra en virtuell Hårddisk med [az storage blob uppladdning](/cli/azure/storage/blob) på följande sätt:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Skapa virtuell dator från en virtuell Hårddisk
Du kan skapa en virtuell dator med en [exempel på skript](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) eller direkt via [az vm skapa](/cli/azure/vm). Om du vill OpenBSD VHD du laddade upp, använder de `--image` parametern på följande sätt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Skaffa IP-adressen för din OpenBSD VM med [az vm list-ip-adresser](/cli/azure/vm) på följande sätt:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nu kan du SSH till din OpenBSD VM som vanligt:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om stöd för Hyper-V på OpenBSD6.1 läsa [OpenBSD 6.1](https://www.openbsd.org/61.html) och [hyperv.4](http://man.openbsd.org/hyperv.4).

Om du vill skapa en virtuell dator från en hanterad disk kan du läsa [az disk](/cli/azure/disk). 
