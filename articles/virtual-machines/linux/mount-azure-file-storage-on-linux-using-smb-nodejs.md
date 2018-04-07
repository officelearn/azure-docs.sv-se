---
title: Montera Azure File storage i virtuella Linux-datorer med hjälp av SMB med Azure CLI 1.0 | Microsoft Docs
description: Hur man monterar Azure File storage på Linux virtuella datorer med hjälp av SMB
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
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 442c08a03ff3eb8e4c86f8190e16b74744aa9dd3
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>Montera Azure File storage i virtuella Linux-datorer med hjälp av SMB med Azure CLI 1.0

Den här artikeln visar hur du monterar Azure File storage på en Linux-VM med hjälp av Server Message Block (SMB)-protokollet. File storage erbjuder filresurser i molnet via SMB-standardprotokollet. Kraven är:

* En [Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [Secure Shell (SSH) offentliga och privata nyckelfilerna](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>CLI-versioner som du kan använda
Du kan slutföra uppgiften med någon av följande versioner kommandoradsgränssnittet (CLI):

- [Azure CLI 1.0](#quick-commands) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)-vår nästa generations CLI för resursdistributionsmodell för hantering


## <a name="quick-commands"></a>Snabbkommandon
Följ stegen i det här avsnittet om du vill slutföra uppgiften snabbt. Mer detaljerad information och kontext som börjar vid den [”detaljerad genomgång”](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough) avsnitt.

### <a name="prerequisites"></a>Förutsättningar
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
Lägg till följande rad i `/etc/fstab`:

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång

File storage erbjuder filresurser i molnet som använder SMB-standardprotokollet. Du kan också montera en filresurs från alla operativsystem som stöder SMB 3.0 med den senaste versionen av File storage. När du använder en SMB-monteringspunkter på Linux hämta enkelt säkerhetskopieringar till en robust, permanenta arkivering lagringsplats som stöds av ett serviceavtal.

Flytta filer från en virtuell dator till en SMB-monteringspunkter som är värd för fillagring är ett bra sätt att debug-loggar. Det beror på att samma SMB-resursen kan monteras lokalt till din Mac, Linux eller Windows-arbetsstation. SMB är den bästa lösningen för direktuppspelning av Linux eller programloggar i realtid, eftersom SMB-protokollet inte har byggts för att hantera sådana uppgifter tunga loggning. En dedikerad, enhetlig loggning layer verktyg som till exempel Fluentd är ett bättre alternativ än SMB för att samla in Linux- och programmet loggning utdata.

För den här detaljerade genomgången ska vi skapa de förutsättningar som krävs för att först skapa fillagringsresursen och montera den via SMB på en Linux-VM.

1. Skapa ett Azure storage-konto med hjälp av följande kod:

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. Visa lagringskontonycklarna.

    När du skapar ett lagringskonto skapas nycklar för kontot i par så att de kan roteras utan några avbrott i tjänsten. När du växlar till den andra nyckeln i paret, skapar du en ny nyckel. Ny lagringskontonycklar skapas alltid parvis, se till att du alltid har minst en oanvända-lagringsnyckel är redo att växla till. Om du vill visa lagringskontonycklarna, använder du följande kod:

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. Skapa File storage-resurs.

    Fillagringsresursen innehåller SMB-resursen. Kvoten uttrycks alltid i gigabyte (GB). Använd följande kod för att skapa File storage-resurs:

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. Skapa katalogen monteringspunkt.

    Du måste skapa en lokal katalog i filsystemet Linux för att montera SMB-resursen. Något skrivs eller läsa från katalogen för lokal montering vidarebefordras till SMB-resurs som är värd för lagring av filer. Använd följande kod för att skapa katalogen:

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. Montera SMB-resursen med hjälp av följande kod:

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. Spara SMB montera via omstarter.

    När du startar om Linux VM är den monterade SMB-resursen omonterade vid avstängningen. Om du vill återansluta till SMB-resursen på Start, måste du lägga till en rad Linux /etc/fstab. Linux använder filen fstab för att lista filsystem som krävs för att montera under startprocessen. Lägger till SMB-resursen garanterar att File storage-resurs är en permanent anslutet filsystem för Linux-VM. Det är möjligt att lägga till File storage SMB-resurs i en ny virtuell dator när du använder molntjänster initiering.

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>Nästa steg

- [Med hjälp av molnet init för att anpassa en Linux VM under skapandet](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Lägg till en disk till en virtuell Linux-dator](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Kryptera diskar på en Linux-VM med hjälp av Azure CLI](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
