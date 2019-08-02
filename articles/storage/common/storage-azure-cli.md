---
title: Använda Azure CLI med Azure Storage | Microsoft Docs
description: Lär dig hur du använder Azures kommando rads gränssnitt (Azure CLI) med Azure Storage för att skapa och hantera lagrings konton och arbeta med Azure-blobbar och-filer.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 3338bed8cd8067d58eb2600854de6c0d8e34d1a3
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668463"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Använda Azure CLI med Azure Storage

Azure CLI med öppen källkod innehåller en uppsättning kommandon för att arbeta med Azure-plattformen. Den innehåller många av de funktioner som finns i [Azure Portal](https://portal.azure.com), inklusive omfattande data åtkomst.

I den här guiden visar vi hur du använder [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) för att utföra flera aktiviteter som arbetar med resurser i ditt Azure Storage-konto. Vi rekommenderar att du hämtar och installerar eller uppgraderar till den senaste versionen av CLI innan du använder den här guiden.

I exemplen i guiden förutsätts användningen av bash-gränssnittet på Ubuntu, men andra plattformar bör utföras på samma sätt. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här guiden förutsätter att du förstår de grundläggande begreppen i Azure Storage. Det förutsätter också att du kan uppfylla de krav för att skapa konton som anges nedan för Azure och lagrings tjänsten.

### <a name="accounts"></a>Konton
* **Azure-konto**: Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free/).
* **Lagringskonto**: Se [skapa ett lagrings konto](storage-quickstart-create-account.md) i [om Azure Storage-konton](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Installera Azure CLI

Hämta och installera Azure CLI genom att följa instruktionerna som beskrivs i [Installera Azure CLI](/cli/azure/install-az-cli2).

> [!TIP]
> Om du har problem med installationen kan du läsa avsnittet Installera [fel sökning](/cli/azure/install-az-cli2) i artikeln och [installera fel söknings](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) guiden på GitHub.
>

## <a name="working-with-the-cli"></a>Arbeta med CLI

När du har installerat CLI kan du använda `az` kommandot i ditt kommando rads gränssnitt (bash, Terminal, kommando tolk) för att få åtkomst till Azure CLI-kommandona. `az` Skriv kommandot om du vill se en fullständig lista över bas kommandona (följande exempel-utdata har trunkerats):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

I kommando rads gränssnittet kör du kommandot `az storage --help` för att visa en `storage` lista över kommando under grupper. Beskrivningarna av under grupperna ger en översikt över de funktioner som Azure CLI tillhandahåller för att arbeta med dina lagrings resurser.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ansluta CLI till din Azure-prenumeration

Om du vill arbeta med resurserna i din Azure-prenumeration måste du först logga in på ditt Azure- `az login`konto med. Du kan logga in på flera sätt:

* **Interaktiv inloggning**:`az login`
* **Logga in med användar namn och lösen ord**:`az login -u johndoe@contoso.com -p VerySecret`
  * Detta fungerar inte med Microsoft-konton eller konton som använder Multi-Factor Authentication.
* **Logga in med ett huvud namn för tjänsten**:`az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Exempel skript för Azure CLI

Nu ska vi arbeta med ett litet skal skript som innehåller några grundläggande Azure CLI-kommandon för att interagera med Azure Storage-resurser. Skriptet skapar först en ny container på ditt lagringskonto och överför sedan en befintlig fil (som en blob) till containern. Sedan visas alla blobar i behållaren och filen laddas ned till ett mål på din lokala dator som du anger.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Konfigurera och kör skriptet**

1. Öppna din favorit text redigerare och kopiera och klistra in föregående skript i redigeraren.

2. Uppdatera sedan skriptets variabler så att de motsvarar dina konfigurations inställningar. Ersätt följande värden enligt vad som anges:

   * storage_account_name namnet på ditt lagrings konto. **\<\>**
   * storage_account_key den primära eller sekundära åtkomst nyckeln för ditt lagrings konto. **\<\>**
   * container_name ett namn på den nya behållaren som ska skapas, till exempel "Azure-CLI-Sample-container". **\<\>**
   * blob_name ett namn för mål-bloben i behållaren. **\<\>**
   * file_to_upload sökvägen till en liten fil på din lokala dator, till exempel "~/images/HelloWorld.png". **\<\>**
   * destination_file är sökvägen till mål filen, till exempel "~/downloadedImage.png". **\<\>**

3. När du har uppdaterat de nödvändiga variablerna sparar du skriptet och avslutar redigeraren. Nästa steg förutsätter att du har namngett skriptet **my_storage_sample. sh**.

4. Markera skriptet som körbar fil, om det behövs:`chmod +x my_storage_sample.sh`

5. Kör skriptet. Till exempel i bash:`./my_storage_sample.sh`

Du bör se utdata som liknar följande och den **\<destination_file\>** som du angav i skriptet bör visas på den lokala datorn.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> Föregående utdata är i **tabell** format. Du kan ange vilket utdataformat som ska användas genom att ange `--output` argumentet i CLI-kommandona eller ange det globalt med `az configure`.
>

## <a name="manage-storage-accounts"></a>Hantera lagringskonton

### <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto
Om du vill använda Azure Storage behöver du ett lagringskonto. Du kan skapa ett nytt Azure Storage-konto när du har konfigurerat datorn för att ansluta till din prenumeration.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location`[Krävs]: Sökvägen. Till exempel "västra USA".
* `--name`[Krävs]: Namnet på lagringskontot. Namnet måste vara mellan 3 och 24 tecken långt och endast använda gemena alfanumeriska tecken.
* `--resource-group`[Krävs]: Resurs gruppens namn.
* `--sku`[Krävs]: Lagrings kontots SKU. Tillåtna värden:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Ange standardvariabler för Azure Storage-kontots miljö

Du kan ha flera lagrings konton i din Azure-prenumeration. För att välja en av dem som ska användas för alla efterföljande lagrings kommandon, kan du ange följande miljövariabler:

Visa först dina lagringskontonycklar med kommandot [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Nu när du har nyckeln kan du definiera den och konto namnet som miljövariabler:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Ett annat sätt att ange ett standard lagrings konto är att använda en anslutnings sträng. Börja med att hämta anslutnings strängen med `show-connection-string` kommandot:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Kopiera sedan anslutnings strängen för utdata och ange `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln (du kan behöva ange anslutnings strängen i citat tecken):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Alla exempel i följande avsnitt i den här artikeln förutsätter att du har ställt `AZURE_STORAGE_ACCOUNT` in `AZURE_STORAGE_KEY` miljövariablerna och.

## <a name="create-and-manage-blobs"></a>Skapa och hantera blobbar
Azure Blob Storage är en tjänst för att lagra stora mängder ostrukturerade data, till exempel text eller binära data, som kan nås från var som helst i världen via HTTP eller HTTPS. Det här avsnittet förutsätter att du redan är bekant med Azure Blob Storage-koncept. Detaljerad information finns i [Kom igång med Azure Blob Storage med hjälp av .net](../blobs/storage-dotnet-how-to-use-blobs.md) och [BLOB service-koncept](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Skapa en container
Varje BLOB i Azure Storage måste finnas i en behållare. Du kan skapa en behållare med hjälp `az storage container create` av kommandot:

```azurecli
az storage container create --name <container_name>
```

Du kan ange en av tre nivåer av Läs behörighet för en ny behållare genom att ange det `--public-access` valfria argumentet:

* `off`(standard): Behållar data är privata för konto ägaren.
* `blob`: Offentlig Läs behörighet för blobbar.
* `container`: Offentlig Läs-och list åtkomst till hela behållaren.

Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Ladda upp en blob till en container
Azure Blob Storage stöder block-, tilläggs-och sid-blobar. Ladda upp blobbar till en behållare med hjälp `blob upload` av kommandot:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Ersätt `--name <blob_name>` med`--name <folder/blob_name>`om du vill överföra direkt till en mapp inuti behållaren i ditt lagrings konto.

 Som standard `blob upload` laddar kommandot upp *. VHD-filer till Page blobbar eller block-blobar på annat sätt. Om du vill ange en annan typ när du laddar upp en BLOB kan `--type` du använda argument--tillåtna `append`värden `block`, och `page`.

 Mer information om olika BLOB-typer finns i [förstå block-blobar, lägga till blobbar och sid](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)-blobar.


### <a name="download-a-blob-from-a-container"></a>Hämta en blob från en container
Det här exemplet visar hur du laddar ned en BLOB från en behållare:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Visa en lista med blobar i en behållare med kommandot [AZ Storage BLOB List](/cli/azure/storage/blob) .

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Kopiera blobbar
Du kan kopiera blobar i eller mellan lagringskonton och regioner asynkront.

Följande exempel visar hur du kopierar blobar från ett lagringskonto till ett annat. Vi skapar först en container i källagringskontot där vi anger offentlig läsbehörighet för dess blobar. Efter det laddar vi upp en fil till containern och slutligen kopierar vi bloben från den containern till en container i mållagringskontot.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

I ovanstående exempel måste mål containern redan finnas på mål lagrings kontot för att kopierings åtgärden ska lyckas. Dessutom måste källbloben som anges i `--source-uri`-argumentet antingen inkludera en signatur för delad åtkomst (SAS) eller vara offentligt åtkomlig, som i det här exemplet.

### <a name="delete-a-blob"></a>Ta bort en blob
Om du vill ta bort en BLOB `blob delete` använder du kommandot:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

### <a name="set-the-content-type"></a>Ange innehållstyp

Innehållstypen, som även kallas MIME-typ, identifierar formatet för data i bloben. Webbläsare och andra program kan du använda innehållstypen för att avgöra hur data ska bearbetas. Innehålls typen för PNG-bilder är `image/png`till exempel. Om du vill ange innehålls typen använder du `blob update` kommandot:

```azurecli
az storage blob update
    --container-name <container_name> 
    --name <blob_name>
    --content-type <content_type>
```

## <a name="create-and-manage-file-shares"></a>Skapa och hantera fil resurser
Azure Files erbjuder delad lagring för program som använder SMB-protokollet (Server Message Block). Microsoft Azure virtuella datorer och moln tjänster, samt lokala program, kan dela fildata via monterade resurser. Du kan hantera fil resurser och fildata via Azure CLI. Mer information om Azure Files finns i [Introduktion till Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Skapa en filresurs
En Azure-filresurs är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en fil resurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagrings kontots kapacitets gränser. I följande exempel skapas en fil resurs mednamnet Unshare.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Skapa en katalog
En katalog innehåller en hierarkisk struktur i en Azure-filresurs. I följande exempel skapas en katalog med namnet **myDir** i fil resursen.

```azurecli
az storage directory create --name myDir --share-name myshare
```

En katalog Sök väg kan innehålla flera nivåer, till exempel **dir1/dir2**. Du måste dock se till att alla överordnade kataloger finns innan du skapar en under katalog. För sökvägen **dir1/dir2**måste du till exempel först skapa katalog **dir1**och sedan skapa katalog **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Ladda upp en lokal fil till en resurs
I följande exempel överförs en fil från **~/Temp/samplefile.txt** till roten för fil resursen för **delning** . `--source` Argumentet anger den befintliga lokala fil som ska överföras.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

När du skapar katalogen kan du ange en katalog Sök väg i resursen för att överföra filen till en befintlig katalog i resursen:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

En fil i resursen kan vara upp till 1 TB.

### <a name="list-the-files-in-a-share"></a>Lista filerna i en resurs
Du kan visa en lista över filer och kataloger i en resurs `az storage file list` med hjälp av kommandot:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopiera filer      
Du kan kopiera en fil till en annan fil, en fil till en BLOB eller en blob till en fil. Om du till exempel vill kopiera en fil till en katalog i en annan resurs:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Skapa resurs ögonblicks bild
Du kan skapa en resurs ögonblicks bild med `az storage share snapshot` hjälp av kommandot:

```cli
az storage share snapshot -n <share name>
```

Exempel på utdata
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Lista resursögonblicksbilder

Du kan visa en lista över resurs ögonblicks bilder av en viss resurs med hjälp av`az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Exempel på utdata**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Bläddra resursögonblicksbilder
Du kan också bläddra till en viss resurs ögonblicks bild för att visa `az storage file list`dess innehåll med hjälp av. En måste ange resurs namnet `--share-name <snare name>` och tidsstämpeln`--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Exempel på utdata**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Återställ från resurs ögonblicks bilder

Du kan återställa en fil genom att kopiera eller ladda ned en fil från en resurs `az storage file download` ögonblicks bild med kommandot

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Exempel på utdata**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Ta bort resurs ögonblicks bild
Du kan ta bort en resurs ögonblicks bild `az storage share delete` genom att använda `--snapshot` kommandot genom att ange en parameter med tidsstämpel för resurs ögonblicks bild

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Exempel på utdata
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Nästa steg
Här följer några ytterligare resurser för att lära dig mer om att arbeta med Azure CLI. 

* [Kom igång med Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Kommando referens för Azure CLI](/cli/azure)
* [Azure CLI på GitHub](https://github.com/Azure/azure-cli)
