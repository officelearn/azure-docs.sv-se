---
title: Montera Azure File storage i virtuella Linux-datorer med hjälp av SMB | Microsoft Docs
description: Hur man monterar Azure File storage på virtuella Linux-datorer med hjälp av SMB med Azure CLI
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
ms.openlocfilehash: 2019324030b2e4c469d0b9ba937fb40a9d0675f1
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099719"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montera Azure File storage i virtuella Linux-datorer med hjälp av SMB


Den här artikeln visar hur du använder Azure File storage-tjänst på en Linux-VM med hjälp av en SMB-montering med Azure CLI. Azure File storage erbjuder filresurser i molnet genom att använda SMB-standardprotokollet. 

File storage erbjuder filresurser i molnet som använder SMB-standardprotokollet. Du kan montera en filresurs från alla operativsystem som stöder SMB 3.0. När du använder en SMB-monteringspunkter på Linux hämta enkelt säkerhetskopieringar till en robust, permanenta arkivering lagringsplats som stöds av ett serviceavtal.

Flytta filer från en virtuell dator till en SMB-monteringspunkter som är värd för fillagring är ett bra sätt att debug-loggar. Samma SMB-resursen kan monteras lokalt till din Mac, Linux eller Windows-arbetsstation. SMB är den bästa lösningen för direktuppspelning av Linux eller programloggar i realtid, eftersom SMB-protokollet inte har byggts för att hantera sådana uppgifter tunga loggning. En dedikerad, enhetlig loggning layer verktyg som till exempel Fluentd är ett bättre alternativ än SMB för att samla in Linux- och programmet loggning utdata.

Den här guiden kräver att du använder Azure CLI version 2.0.4 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med namnet *myResourceGroup* i den *östra USA* plats.

```bash
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett nytt lagringskonto i resursgruppen som du skapat med [az storage-konto skapar](/cli/azure/storage/account#create). Det här exemplet skapas ett lagringskonto med namnet *mySTORAGEACCT<random number>*  och placerar namnet på det lagringskontot i variabeln **STORAGEACCT**. Lagringskontonamn måste vara unikt, med hjälp av `$RANDOM` lägger till ett nummer i slutet som gör det unikt.

```bash
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Hämta lagringsnyckel för

När du skapar ett lagringskonto skapas nycklar för kontot i par så att de kan roteras utan några avbrott i tjänsten. När du växlar till den andra nyckeln i paret, skapar du en ny nyckel. Ny lagringskontonycklar skapas alltid parvis, så att du alltid har minst en oanvända lagringskontonyckel redo att växla till.

Visa lagringskontonycklar med [az nycklar lagringskontolistan](/cli/azure/storage/account/keys#list). Det här exemplet lagrar värdet för nyckel 1 i den **STORAGEKEY** variabeln.

```bash
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Skapa en filresurs

Skapa filen lagring resursen med [az lagringsresurs skapa](/cli/azure/storage/share#create). 

Resursnamn måste vara alla gemena bokstäver, siffror och bindestreck enda men får inte börja med ett bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Det här exemplet skapar en resurs med namnet *minresurs* med en 10 GiB kvot. 

```bash
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Skapa en monteringspunkt

Om du vill ansluta till Azure-filresursen på Linux-dator, måste du kontrollera att du har den **cifs-verktyg för webbplatsuppgradering** paketet är installerat. Installationsanvisningar finns i [installationspaket cifs-verktyg för webbplatsuppgradering för Linux-distribution](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files använder SMB-protokollet som kommunicerar via TCP-port 445.  Om du har problem med din Azure-filresursen att montera, kontrollera att brandväggen inte blockerar TCP-port 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montera resursen

Montera filresursen Azure till den lokala katalogen. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```



## <a name="persist-the-mount"></a>Spara monterings

När du startar om Linux VM är den monterade SMB-resursen omonterade vid avstängningen. Lägga till en rad Linux /etc/fstab om du vill återansluta till SMB-resursen på Start. Linux använder filen fstab för att lista filsystem som krävs för att montera under startprocessen. Lägger till SMB-resursen garanterar att File storage-resurs är en permanent anslutet filsystem för Linux-VM. Det är möjligt att lägga till File storage SMB-resurs i en ny virtuell dator när du använder molntjänster initiering.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```
För ökad säkerhet i produktionsmiljöer bör du lagra autentiseringsuppgifterna utanför fstab.

## <a name="next-steps"></a>Nästa steg

- [Med hjälp av molnet init för att anpassa en Linux VM under skapandet](using-cloud-init.md)
- [Lägg till en disk till en virtuell Linux-dator](add-disk.md)
- [Kryptera diskar på en Linux-VM med hjälp av Azure CLI](encrypt-disks.md)

