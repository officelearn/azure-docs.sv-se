---
title: Snabbstart för hantering av Azure-filresurser med hjälp av Azure CLI
description: I den här snabbstarten lär du dig hur du använder Azure CLI till att hantera Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: ee8dcf1488cfb407793bdb35cdbbee18b2ef15ab
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750978"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Snabbstart: Skapa och hantera Azure-filresurser med hjälp av Azure CLI
Den här guiden vägleder dig igenom grunderna i att arbeta med [Azure-filresurser](storage-files-introduction.md) med hjälp av Azure CLI. Azure-filresurser är precis som andra filresurser men lagras i molnet och backas av Azure-plattformen. Azure-filresurser stöder SMB-protokollet, som är branschstandard och möjliggör fildelning på olika datorer, program och instanser. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du beslutar dig för att installera och använda Azure CLI lokalt måste du köra Azure CLI version 2.0.4 eller senare för att kunna utföra stegen i den här artikeln. Kör **az --version** för att hitta din Azure CLI-version. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 

Som standard returnerar Azure CLI-kommandon JavaScript Object Notation (JSON). JSON är standardmetoden för att skicka och ta emot meddelanden från REST API:er. För att underlätta arbetet med JSON-svaren, så använder vissa av exemplen i den här artikeln *frågeparametern* för Azure CLI-kommandona. Den här parametern använder [JMESPath-frågespråket](http://jmespath.org/) för JSON-parsning. Om du vill lära dig mer om hur man hanterar Azure CLI-kommandonas resultat genom att följa JMESPath-frågespråket kan du ta en titt på [JMESPath-självstudiekursen](http://jmespath.org/tutorial.html).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Om du använder Azure CLI lokalt öppnar du en kommandotolk och loggar in på Azure om du inte redan har gjort det.

```bash 
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Om du inte redan har en Azure-resursgrupp kan du skapa en ny med kommandot [az group create](/cli/azure/group). 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *USA, östra*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
Ett lagringskonto är en delad lagringspool i vilken du kan distribuera en Azure-filresurs eller andra lagringsresurser, t.ex. blobar eller köer. Ett lagringskonto kan innehålla ett obegränsat antal filresurser. En resurs kan lagra ett obegränsat antal filer, upp till kapacitetsbegränsningen för lagringskontot.

I följande exempel skapas ett lagringskonto med namnet *mystorageaccount\< med det slumpmässiga talet\>* med kommandot [az storage account create](/cli/azure/storage/account), varefter lagringskontots namn läggs till i variabeln `$STORAGEACCT`. Lagringskontonamn måste vara unika. Med `$RANDOM` bifogas en siffra till lagringskontonamnet för att göra det unikt. 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>Hämta lagringskontonyckeln
Lagringskontonycklar styr åtkomsten till resurser i ett lagringskonto. Nycklarna skapas automatiskt när du skapar ett lagringskonto. Du kan hämta lagringskontonycklarna till lagringskontot med kommandot [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs
Nu kan du skapa din första Azure-filresurs. Skapa filresurser med kommandot [az storage share create](/cli/azure/storage/share). I det här exemplet skapas en Azure-filresurs som heter *myshare*: 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

Resursnamn får bara innehålla gemener, siffror och enskilda bindestreck (men får inte inledas med bindestreck). Mer information om hur du namnger filresurser och filer finns i [Namnge och referera till resurser, kataloger, filer och metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Använda Azure-filresursen
Azure Files har två metoder för att arbeta med filer och mappar i din Azure-filresurs: branschstandardprotokollen [SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) och [fil-REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Om du vill montera en filresurs med SMB läser du följande dokument baserat på ditt operativsystem:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Använda en Azure-filresurs med fil-REST-protokollet 
Det är möjligt att arbeta direkt med fil-REST-protokollet (dvs. att själv skapa REST HTTP-anrop manuellt) men det vanligaste sättet att använda fil-REST-protokollet är att använda Azure CLI, [Azure PowerShell-modulen](storage-how-to-use-files-powershell.md) eller ett Azure Storage-SDK. Alla ger en bra omslutning runt fil-REST-protokollet i valfritt skript-/programmeringsspråk.  

Vi förväntar att de flesta som använder Azure Files vill arbeta med sin Azure-filresurs via SMB-protokollet, eftersom det gör att de kan använda de befintliga programmen och verktygen som de förväntar sig att kunna använda. Men det finns flera anledningar till varför det är fördelaktigt att använda fil-REST-API:et istället för SMB, till exempel:

- Du bläddrar efter filresursen från Azure Bash Cloud Shell (som inte kan montera filresurser via SMB).
- Du måste köra ett skript eller program från en klient som inte kan montera en SMB-resurs, till exempel lokala klienter, som inte har port 445 avblockerad.
- Du utnyttjar serverlösa resurser såsom [Azure Functions](../../azure-functions/functions-overview.md). 

I följande exempel visas hur du använder Azure CLI till att ändra din Azure-filresurs med fil-REST-protokollet. 

### <a name="create-a-directory"></a>Skapa en katalog
Använd kommandot [`az storage directory create`](/cli/azure/storage/directory) när du vill skapa en ny katalog med namnet *myDirectory* i vid Azure-filresursens rot:

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>Överför en fil
För att visa hur du laddar upp en fil med kommandot [`az storage file upload`](/cli/azure/storage/file) skapar du först en fil som ska laddas upp på den tillfälliga Cloud Shell-enheten. I följande exempel ska du skapa och ladda upp filen:

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

När du har överfört filen kan du använda kommandot [`az storage file list`](/cli/azure/storage/file) för att kontrollera att filen har överförts till Azure-filresursen:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Hämta en fil
Du kan hämta en kopia av filen du laddade upp till Cloud Shell-enheten genom att använda kommandot [`az storage file download`](/cli/azure/storage/file):

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>Kopiera filer
En gemensam uppgift är att kopiera filer från en filresurs till en annan eller till eller från en Azure Blob Storage-container. Om du vill visa den här funktionen skapar du en ny resurs. Kopiera filen du laddade upp till den här nya resursen med kommandot [az storage file copy](/cli/azure/storage/file/copy): 

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

Du bör nu se den kopierade filen om du visar filerna i den nya resursen:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

Även om kommandot `az storage file copy start` är praktiskt för filförflyttningar mellan Azure-filresurser och Azure Blob Storage-containrar, så rekommenderar vi att du använder AzCopy för större flyttar. (Större när det gäller antal eller storleken på filerna som ska flyttas.) Lär dig mer om [AzCopy för Linux](../common/storage-use-azcopy-linux.md) och [AzCopy för Windows](../common/storage-use-azcopy.md). AzCopy måste installeras lokalt. AzCopy är inte tillgängligt i Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Skapa och hantera resursögonblicksbilder
Ytterligare en användbar uppgift som du kan göra med en Azure-filresurs är att skapa resursögonblicksbilder. En ögonblicksbild bevarar en kopia vid en viss tidpunkt av en Azure-filresurs. Ögonblicksbilder av resurser liknar vissa av de operativsystemtekniker som du kanske redan är bekant med:

- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) ögonblicksbilder för Linux-system
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) ögonblicksbilder för macOS
- [Volume Shadow Copy Service (VSS)](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) för Windows-filsystem såsom NTFS och ReFS Du kan skapa en resursögonblicksbild med hjälp av kommandot [`az storage share snapshot`](/cli/azure/storage/share):

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Söka i resursögonblicksbilders innehåll
Du kan söka i en resursögonblicksbilds innehåll genom att skicka resursögonblicksbildens tidsstämpel som vi tog i variabeln `$SNAPSHOT` till kommandot `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder
Du kan se listan över ögonblicksbilder som du har tagit för din resurs med följande kommando:

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>Återställ från en resursögonblicksbild
Du kan återställa en fil med hjälp av kommandot `az storage file copy start` som du använde tidigare. Först tar du bort filen SampleUpload.txt som du laddade upp, så att du kan återställa den från ögonblicksbilden:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"
 # Build the source URI for a snapshot restore
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
Du kan ta bort en ögonblicksbild av en resurs med hjälp av kommandot [`az storage share delete`](/cli/azure/storage/share). Använd variabeln som innehåller referensen `$SNAPSHOT` till parametern `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar kan du använda kommandot [`az group delete`](/cli/azure/group) för att ta bort resursgruppen och alla relaterade resurser: 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

Du kan även ta bort resurser individuellt.
- Så här tar du bort de Azure-filresurser som du skapade för den här artikeln:

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

- Ta bort själva lagringskontot. (Detta tar implicit bort Azure-filresurserna du skapade, och eventuella andra lagringsresurser som du kan ha skapat, som en Azure Blob Storage-container.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group "myResourceGroup" \
        --name $STORAGEACCT \
        --yes
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vad är Azure Files?](storage-files-introduction.md)
