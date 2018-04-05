---
title: Hantera Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att hantera Azure Files.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>Hantera Azure-filresurser med Azure CLI
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras i Windows, Linux och macOS. Den här guiden vägleder dig igenom grunderna i att arbeta med Azure-filresurser med Azure CLI. Lär dig att: 

> [!div class="checklist"]
> * Skapa en resursgrupp och ett lagringskonto
> * Skapa en Azure-filresurs 
> * Skapa en katalog
> * Överför en fil 
> * Hämta en fil
> * Skapa och använda en ögonblicksbild av en resurs

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt kräver den här guiden att du kör Azure CLI version 2.0.4 eller senare. Kör **az --version** om du vill se versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

Azure CLI-kommandona returnerar som standard JSON (JavaScript Object Notation), vilket är det faktiska sättet att skicka och ta emot meddelanden från REST API:er. För att underlätta arbetet med JSON-svaren, så använder vissa av exemplen i den här guiden frågeparametern för Azure CLI-kommandona. Den här parametern använder [JMESPath-frågespråket](http://jmespath.org/) för JSON-parsning. Du kan lära dig mer om hur man hanterar Azure CLI-kommandonas resultat genom att följa [JMESPath-självstudiekursen](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Om du inte redan har en Azure-resursgrupp kan du skapa en ny med kommandot [az group create](/cli/azure/group#create). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *USA, östra*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Ett lagringskonto är en delad lagringspool i vilken du kan distribuera en Azure-filresurs eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal filresurser och en resurs kan lagra ett obegränsat antal filer, upp till lagringskontots kapacitetsgräns.

I det här exemplet skapas ett lagringskonto med namnet `mystorageaccount<random number>` med kommandot [az storage account create](/cli/azure/storage/account#create), varefter lagringskontots namn läggs till i variabeln `$STORAGEACCT`. Lagringskontonamn måste vara unika. Använd `$RANDOM` för att lägga till ett nummer i slutet av lagringskontots namn, så att det blir unikt. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Hämta lagringskontonyckeln
Lagringskontonycklar används för att styra åtkomsten till resurser i ett lagringskonto. De skapas automatiskt när du skapar ett lagringskonto. Du kan hämta lagringskontonycklarna till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys#list). 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Nu kan du skapa din första Azure-filresurs. Du kan skapa filresurser med kommandot [az storage share create](/cli/azure/storage/share#create). I det här exemplet skapar vi en Azure-filresurs som heter `myshare`. 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> Resursnamn får bara innehålla gemena bokstäver, siffror och enskilda bindestreck, men får inte inledas med bindestreck. Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Hantera innehållet i Azure-filresursen
Nu när du har skapat en Azure-filresurs kan du montera filresursen med SMB på [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) eller [macOS](storage-how-to-use-files-mac.md). Du kan också ändra dina Azure-filresursen med Azure CLI. Detta innebär stora fördelar jämfört med att montera filresursen med SMB, eftersom alla förfrågningar som görs med Azure CLI görs med File REST API:et, så att du kan skapa, ändra och ta bort filer och kataloger i filresursen från:

- Bash Cloud Shell (som inte kan montera filresurser över SMB).
- Klienter som inte kan montera SMB-resurser, t.ex. lokala klienter som inte har port 445 avblockerad.
- Serverlösa scenarier, t.ex [Azure Functions](../../azure-functions/functions-overview.md). 

### <a name="create-directory"></a>Skapa katalog
Använd kommandot [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) när du vill skapa en ny katalog med namnet *myDirectory* i vid Azure-filresursens rot.

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Överför en fil
För att kunna visa dig hur du överför en fil med hjälp av kommandot [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) måste vi först skapa en fil på din tillfälliga Azure CLI Cloud Shell-enhet att ladda upp. I följande exempel ska vi skapa och ladda upp filen.

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Om du använder Azure CLI lokalt kan du ersätta `~/clouddrive` med en sökväg som finns på din dator.

När du har överfört filen kan du använda kommandot [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) för att kontrollera att filen har överförts till Azure-filresursen.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Hämta en fil
Du kan hämta en kopia av filen du laddade upp till den tillfälliga Cloud Shell-enheten genom att använda kommandot [`az storage file download`](/cli/azure/storage/file#az_storage_file_download).

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Kopiera filer
En gemensam uppgift är att kopiera filer från en filresurs till en annan eller till/från en Azure Blob Storage-behållare. Om du vill demonstrera den här funktionen kan du skapa en ny resurs och kopiera filen du överförde till denna nya resurs med kommandot [az storage file copy](/cli/azure/storage/file/copy). 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Du bör nu se den kopierade filen om du visa filerna i den nya resursen.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Även om kommandot `az storage file copy start` är praktiskt för ad hoc-filförflyttningar mellan Azure-filresurser och Azure Blob Storage-behållare, så rekommenderar vi AzCopy för större flyttar (sett till antalet filer som flyttas eller hur stora filer som flyttas). Lär dig mer om [AzCopy för Linux](../common/storage-use-azcopy-linux.md) och [AzCopy för Windows](../common/storage-use-azcopy.md). AzCopy måste installeras lokalt – det är inte tillgängligt i Cloud Shell 

## <a name="create-and-modify-share-snapshots"></a>Skapa och ändra resursögonblicksbilder
Ytterligare en användbar uppgift som du kan göra med en Azure-filresurs är att skapa resursögonblicksbilder. En ögonblicksbild bevarar en tidpunkt för en Azure-filresurs. Ögonblicksbilder av resurser liknar de operativsystemtekniker som du kanske redan är bekant med såsom:
- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) ögonblicksbilder för Linux-system
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS. 
- [Tjänsten Volume Shadow Copy(VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) för Windows-filsystem, till exempel NTFS och ReFS

Du kan skapa en ögonblicksbild av en resurs med hjälp av kommandot [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Söka i resursögonblicksbilders innehåll
Du kan söka i en resursögonblicksbilds innehåll genom att skicka resursögonblicksbildens tidsstämpel som vi tog i variabeln `$SNAPSHOT` till kommandot `az storage file list`.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder
Du kan se listan över ögonblicksbilder som du har gjort för din resurs med det här kommandot.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
Du kan återställa en fil med hjälp av kommandot `az storage file copy start` som vi använde tidigare. Först tar vi bort vår `SampleUpload.txt`-fil som vi laddade upp, så att vi kan återställa den från ögonblicksbilden.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Ta bort en resursögonblicksbild
Du kan ta bort en resursögonblicksbild med kommandot [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete), med variabeln som innehåller `$SNAPSHOT`-referensen till `--snapshot`-parametern.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar kan du använda kommandot [`az group delete`](/cli/azure/group#delete) för att ta bort resursgruppen och alla relaterade resurser. 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Du kan också ta bort resurserna en i taget:
- Ta bort de Azure-filresurser som vi skapade för den här snabbstarten.

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- Ta bort själva lagringskontot (vilket medför att de Azure-filresurser som vi skapat liksom andra lagringsresurser som du kan ha skapat, t.ex. en Azure Blob Storage-behållare, också tas bort).

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>Nästa steg
- [Hantera filresurser med Azure Portal](storage-how-to-use-files-portal.md)
- [Hantera filresurser med Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Hantera filresurser med Storage Explorer](storage-how-to-use-files-storage-explorer.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)