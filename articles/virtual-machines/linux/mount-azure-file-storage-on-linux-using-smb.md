---
title: Montera Azure File storage på virtuella Linux-datorer med hjälp av SMB | Microsoft Docs
description: Så här att montera Azure File storage på virtuella Linux-datorer med hjälp av SMB med Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.component: files
ms.openlocfilehash: 21c903176b0de3e557d575c448e925cfc95fac15
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468684"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montera Azure File storage på virtuella Linux-datorer med SMB

Den här artikeln visar hur du använder Azure File storage-tjänsten på en Linux VM med hjälp av en SMB-montera med Azure CLI. Azure File storage erbjuder filresurser i molnet med SMB-standardprotokollet. 

Fillagringen erbjuder filresurser i molnet som använder SMB-standardprotokollet. Du kan montera en filresurs från alla operativsystem som stöder SMB 3.0. När du använder en SMB-montera i Linux kan få du enkelt säkerhetskopieringar till en robust, permanent arkivering lagringsplats som stöds av ett serviceavtal.

Flytta filer från en virtuell dator till en SMB-monteringspunkt som är värd för File Storage är ett bra sätt att felsöka loggar. I samma SMB-resursen kan monteras lokalt på din Mac, Linux eller Windows-arbetsstation. SMB är inte den bästa lösningen för strömning av Linux eller programloggar i realtid, eftersom SMB-protokollet inte har utformats för att hantera sådana uppgifter för tung loggning. En dedikerad, enhetlig loggning layer-verktyg som till exempel Fluentd är bättre än SMB för att samla in Linux och program som loggning utdata.

Den här guiden kräver att du kör Azure CLI version 2.0.4 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med namnet *myResourceGroup* i den *USA, östra* plats.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett nytt lagringskonto i resursgruppen som du skapade med [az storage-konto skapar](/cli/azure/storage/account#create). Det här exemplet skapar ett lagringskonto med namnet *mySTORAGEACCT<random number>*  och placerar namnet på det lagringskontot i variabeln **STORAGEACCT**. Lagringskontonamn måste vara unikt, med hjälp av `$RANDOM` bifogas en siffra till slutet för att göra det unikt.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Hämta lagringskontots åtkomstnyckel

När du skapar ett lagringskonto skapas nycklar för kontot i par så att de kan roteras utan avbrott i tjänsten. När du växlar till den andra nyckeln i paret kan du skapa ett nytt nyckelpar. Ny lagringskontonycklar skapas alltid i par, så att du alltid har minst en oanvända lagringskontonyckel redo att växla till.

Visa lagringskontonycklarna med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys#list). Det här exemplet lagrar värdet för nyckel 1 i den **STORAGEKEY** variabeln.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Skapa en filresurs

Skapa filen storage resurs med [az-lagringsresursen skapa](/cli/azure/storage/share#create). 

Resursnamn måste vara alla gemena bokstäver, siffror och enskilda bindestreck, men får inte inledas med ett bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Det här exemplet skapar en resurs med namnet *myshare* med en 10-GiB kvot. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Skapa en monteringspunkt

Om du vill montera Azure-filresursen på Linux-datorn, måste du kontrollera att du har den **cifs-utils** paketet. Installationsanvisningar finns i [installera cifs-utils-paketet för din Linux-distribution](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files använder SMB-protokollet, som kommunicerar via TCP-port 445.  Om du har problem med att montera din Azure-filresurs kan du kontrollera att brandväggen inte blockerar TCP-port 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montera filresursen

Montera Azure-filresursen till den lokala katalogen. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Ovanstående kommando använder de [montera](https://linux.die.net/man/8/mount) kommando för att montera Azure-filresurs och alternativ som är specifika för [cifs](https://linux.die.net/man/8/mount.cifs). Mer specifikt file_mode och dir_mode alternativ uppsättning filer och kataloger till behörigheten `0777`. Den `0777` behörigheten ger läsa, skriva och körbehörighet till alla användare. Du kan ändra behörigheterna genom att ersätta värdena med andra [chmod behörigheter](https://en.wikipedia.org/wiki/Chmod). Du kan också använda andra [cifs](https://linux.die.net/man/8/mount.cifs) alternativ, till exempel gid eller uid. 


## <a name="persist-the-mount"></a>Spara monterings

När du startar om Linux VM är den monterade SMB-resursen demonterats under avstängning. Om du vill montera SMB-resurs på Start, till en rad i Linux/etc/fstab. Linux använder fstab-filen för att visa en lista över filsystem som krävs för att montera under startprocessen. Du lägger till SMB-resursen garanterar att fillagringsresursen är ett permanent monterade filsystem för Linux VM. Det är möjligt att lägga till File storage för SMB-resurs till en ny virtuell dator när du använder cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
För ökad säkerhet i produktionsmiljöer bör du lagra dina autentiseringsuppgifter utanför fstab.

## <a name="next-steps"></a>Nästa steg

- [Använd cloud-init för att anpassa en Linux VM när skapas](using-cloud-init.md)
- [Lägg till en disk till en virtuell Linux-dator](add-disk.md)
- [Kryptera diskar på en Linux VM med hjälp av Azure CLI](encrypt-disks.md)

