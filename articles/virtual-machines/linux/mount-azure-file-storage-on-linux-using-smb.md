---
title: Montera Azure File storage i virtuella Linux-datorer med hjälp av SMB | Microsoft Docs
description: Hur man monterar Azure File storage på virtuella Linux-datorer med hjälp av SMB med Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2017
ms.author: v-livech
ms.openlocfilehash: de200c9b18b9d27325bcb92e0d27e83ad7c65811
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montera Azure File storage i virtuella Linux-datorer med hjälp av SMB

Den här artikeln visar hur du använder tjänsten Azure File storage på en Linux-VM i en SMB-montering med Azure CLI 2.0. Azure File storage erbjuder filresurser i molnet genom att använda SMB-standardprotokollet. Du kan också utföra dessa steg med [Azure CLI 1.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Kraven är:

- [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
- [offentliga och privata SSH-nyckelfiler](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Snabbkommandon

* En resursgrupp
* Azure-nätverk
* En nätverkssäkerhetsgrupp med en SSH inkommande
* Ett undernät
* Ett Azure storage-konto
* Azure lagringskontonycklar
* Ett Azure File storage-resurs
* En virtuell Linux-dator

Ersätt alla exempel med dina egna inställningar.

### <a name="create-a-directory-for-the-local-mount"></a>Skapa en katalog för lokal montering

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>Montera fillagring SMB-resurs till monteringspunkten

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>Monteringen är kvar efter en omstart
Om du vill göra det lägger du till följande rad i den `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

File storage erbjuder filresurser i molnet som använder SMB-standardprotokollet. Du kan också montera en filresurs från alla operativsystem som stöder SMB 3.0 med den senaste versionen av File storage. När du använder en SMB-monteringspunkter på Linux hämta enkelt säkerhetskopieringar till en robust, permanenta arkivering lagringsplats som stöds av ett serviceavtal.

Flytta filer från en virtuell dator till en SMB-monteringspunkter som är värd för fillagring är ett bra sätt att debug-loggar. Det beror på att samma SMB-resursen kan monteras lokalt till din Mac, Linux eller Windows-arbetsstation. SMB är den bästa lösningen för direktuppspelning av Linux eller programloggar i realtid, eftersom SMB-protokollet inte har byggts för att hantera sådana uppgifter tunga loggning. En dedikerad, enhetlig loggning layer verktyg som till exempel Fluentd är ett bättre alternativ än SMB för att samla in Linux- och programmet loggning utdata.

För den här detaljerade genomgången ska vi skapa de förutsättningar som krävs för att först skapa fillagringsresursen och montera den via SMB på en Linux-VM.

1. Skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#az_group_create) för filresursen.

    Så här skapar du en resursgrupp med namnet `myResourceGroup` på plats ”USA, västra”, Använd följande exempel:

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

2. Skapa ett Azure storage-konto med [az storage-konto skapar](/cli/azure/storage/account#az_storage_account_create) de faktiska filerna.

    Använd följande exempel för att skapa ett lagringskonto med namnet mittlagringskonto med hjälp av Standard_LRS lagring SKU:

    ```azurecli
    az storage account create --resource-group myResourceGroup \
        --name mystorageaccount \
        --location westus \
        --sku Standard_LRS
    ```

3. Visa lagringskontonycklarna.

    När du skapar ett lagringskonto skapas nycklar för kontot i par så att de kan roteras utan några avbrott i tjänsten. När du växlar till den andra nyckeln i paret, skapar du en ny nyckel. Ny lagringskontonycklar skapas alltid parvis, se till att du alltid har minst en oanvända lagringskontonyckel redo att växla till.

    Visa lagringskontonycklar med den [az nycklar lagringskontolistan](/cli/azure/storage/account/keys#az_storage_account_keys_list). Lagringskontot nycklar för den namngivna `mystorageaccount` visas i följande exempel:

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount
    ```

    Om du vill extrahera en enskild nyckel måste använda den `--query` flaggan. I följande exempel hämtar den första nyckeln (`[0]`):

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --account-name mystorageaccount \
        --query '[0].{Key:value}' --output tsv
    ```

4. Skapa File storage-resurs.

    Fillagringsresursen innehåller SMB-resursen med [az lagringsresurs skapa](/cli/azure/storage/share#az_storage_share_create). Kvoten uttrycks alltid i gigabyte (GB). Pass i en av nycklarna från den föregående `az storage account keys list` kommando. Skapa en resurs med namnet mystorageshare med en 10 GB kvot genom att använda följande exempel:

    ```azurecli
    az storage share create --name mystorageshare \
        --quota 10 \
        --account-name mystorageaccount \
        --account-key nPOgPR<--snip-->4Q==
    ```

5. Skapa en monteringspunkt katalog.

    Skapa en lokal katalog i filsystemet Linux för att montera SMB-resursen. Något skrivs eller läsa från katalogen för lokal montering vidarebefordras till SMB-resurs som är värd för lagring av filer. Om du vill skapa en lokal katalog i /mnt/mymountdirectory kan du använda följande exempel:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

6. Montera SMB-resursen till den lokala katalogen.

    Ange dina egna lagring Kontoanvändarnamn och lagringskontonyckel för monteringspunkter på följande sätt:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
    ```

7. Spara SMB montera via omstarter.

    När du startar om Linux VM är den monterade SMB-resursen omonterade vid avstängningen. Lägga till en rad Linux /etc/fstab om du vill återansluta till SMB-resursen på Start. Linux använder filen fstab för att lista filsystem som krävs för att montera under startprocessen. Lägger till SMB-resursen garanterar att File storage-resurs är en permanent anslutet filsystem för Linux-VM. Det är möjligt att lägga till File storage SMB-resurs i en ny virtuell dator när du använder molntjänster initiering.

    ```bash
    //myaccountname.file.core.windows.net/mystorageshare /mnt/mymountdirectory cifs vers=3.0,username=mystorageaccount,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Nästa steg

- [Med hjälp av molnet init för att anpassa en Linux VM under skapandet](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Lägg till en disk till en virtuell Linux-dator](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Kryptera diskar på en Linux-VM med hjälp av Azure CLI](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
