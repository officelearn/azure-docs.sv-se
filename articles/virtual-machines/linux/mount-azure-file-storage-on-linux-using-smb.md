---
title: Montera Azure File Storage på virtuella Linux-datorer med SMB
description: Så här monterar du Azure File Storage på virtuella Linux-datorer med SMB med Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 0314095a053087a7d490926c41c6ae386c304919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066650"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Montera Azure File Storage på virtuella Linux-datorer med SMB

Den här artikeln visar hur du använder Azure File Storage-tjänsten på en Virtuell Linux-dator med hjälp av ett SMB-fäste med Azure CLI. Azure File storage erbjuder filresurser i molnet med hjälp av standard-SMB-protokollet. 

Fillagring erbjuder filresurser i molnet som använder standard-SMB-protokollet. Du kan montera en filresurs från alla operativsystem som stöder SMB 3.0. När du använder ett SMB-fäste på Linux får du enkla säkerhetskopior till en robust, permanent lagringsplats som stöds av ett serviceavtal.

Att flytta filer från en virtuell dator till ett SMB-fäste som finns på Fillagring är ett bra sätt att felsöka loggar. Samma SMB-resurs kan monteras lokalt på din Mac-, Linux- eller Windows-arbetsstation. SMB är inte den bästa lösningen för streaming Linux eller programloggar i realtid, eftersom SMB-protokollet inte är byggt för att hantera sådana tunga loggningsuppgifter. En dedikerad, enhetlig loggning lager verktyg som Fluentd skulle vara ett bättre val än SMB för att samla Linux och program loggning utgång.

Den här guiden kräver att du kör Azure CLI version 2.0.4 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med namnet *myResourceGroup* på platsen för *östra USA.*

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett nytt lagringskonto inom resursgruppen som du skapade med hjälp av [az storage-konto skapa](/cli/azure/storage/account). I det här exemplet skapas ett lagringskonto med namnet *mySTORAGEACCT-slumptal\<>* och placeras namnet på det lagringskontot i variabeln **STORAGEACCT**. Lagringskontonamn måste vara `$RANDOM` unika med hjälp av tillägg till ett nummer till slutet för att göra det unikt.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>Hämta lagringsnyckeln

När du skapar ett lagringskonto skapas kontonycklarna i par så att de kan roteras utan avbrott i tjänsten. När du växlar till den andra nyckeln i paret skapar du ett nytt nyckelpar. Nya lagringskontonycklar skapas alltid i par, så du har alltid minst en oanvänd lagringskontonyckel som är redo att växla till.

Visa lagringskontonycklarna med hjälp av listan över [az-lagringskontonycklar](/cli/azure/storage/account/keys). I det här exemplet lagras värdet för nyckel 1 i **STORAGEKEY-variabeln.**

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Skapa en filresurs

Skapa fillagringsresursen med [az storage share create](/cli/azure/storage/share). 

Resursnamn måste vara alla gemener, siffror och enstaka bindestreck, men kan inte börja med ett bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

I det här exemplet skapas en resurs med namnet *myshare* med en 10-GiB-kvot. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Skapa en monteringspunkt

Om du vill montera Azure-filresursen på din Linux-dator måste du se till att du har **cifs-utils-paketet** installerat. Installationsinstruktioner finns [i Installera cifs-utils-paketet för din Linux-distribution](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files använder SMB-protokoll som kommunicerar via TCP-port 445.  Om du har problem med att montera din Azure-filresurs kontrollerar du att brandväggen inte blockerar TCP-port 445.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Montera aktien

Montera Azure-filresursen till den lokala katalogen. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

Kommandot Ovan använder [kommandot montera](https://linux.die.net/man/8/mount) för att montera Azure-filresursen och alternativ som är specifika för [cifs](https://linux.die.net/man/8/mount.cifs). I file_mode- och dir_mode-alternativen anges filer och kataloger `0777`till behörighet . Behörigheten `0777` ger läs-, skriv- och körningsbehörighet till alla användare. Du kan ändra dessa behörigheter genom att ersätta värdena med andra [chmod-behörigheter](https://en.wikipedia.org/wiki/Chmod). Du kan också använda andra [cifs-alternativ](https://linux.die.net/man/8/mount.cifs) som gid eller uid. 


## <a name="persist-the-mount"></a>Fäst monteringen

När du startar om Linux-vm avmonteras den monterade SMB-resursen under avstängningen. Om du vill montera upp SMB-resursen igen vid uppstart lägger du till en rad i Linux /etc/fstab. Linux använder fstab-filen för att lista de filsystem som den behöver montera under startprocessen. Genom att lägga till SMB-resursen säkerställs att fillagringsresursen är ett permanent monterat filsystem för Linux-vm. Det går att lägga till SMB-resursen för fillagring i en ny virtuell dator när du använder cloud-init.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

För ökad säkerhet i produktionsmiljöer bör du lagra dina autentiseringsuppgifter utanför fstab.

## <a name="next-steps"></a>Nästa steg

- [Använda cloud-init för att anpassa en Virtuell Linux-dator när du skapar](using-cloud-init.md)
- [Lägg till en disk till en virtuell Linux-dator](add-disk.md)
- [Virtuella Azure-diskkryptering för virtuella Linux-datorer](disk-encryption-overview.md)

