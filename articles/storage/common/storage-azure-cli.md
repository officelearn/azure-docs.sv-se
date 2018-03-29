---
title: Med hjälp av Azure CLI 2.0 med Azure Storage | Microsoft Docs
description: Lär dig hur du använder Azure-kommandoradsgränssnittet (Azure CLI) 2.0 med Azure Storage för att skapa och hantera storage-konton och arbeta med Azure-blobbar och filer. Azure CLI 2.0 är ett verktyg för flera plattformar som skrivits i Python.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: rogarana
ms.openlocfilehash: 99e67b9c8469f08f1cbfc980568eec35694d1ae2
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Med hjälp av Azure CLI 2.0 med Azure Storage

Med öppen källkod, plattformsoberoende Azure CLI 2.0 innehåller en uppsättning kommandon för att arbeta med Azure-plattformen. Det ger mycket av samma funktioner som finns i den [Azure-portalen](https://portal.azure.com), inklusive omfattande dataåtkomst.

I den här handboken vi hur du använder den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) för flera uppgifter ska arbeta med resurserna i Azure Storage-konto. Vi rekommenderar att du hämtar och installerar eller uppgraderar till den senaste versionen av CLI 2.0 innan du använder den här guiden.

Exemplen i handboken förutsätter att Bash-gränssnitt på Ubuntu, men andra plattformar ska utföra på samma sätt. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Förutsättningar
Den här handboken förutsätts att du förstår de grundläggande principerna för Azure Storage. Det förutsätts även att du är kan uppfylla kraven för kontot skapas som anges nedan för Azure och Storage-tjänst.

### <a name="accounts"></a>Konton
* **Azure-konto**: Om du inte redan har en Azure-prenumeration [skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).
* **Lagringskonto**: Se [skapar ett lagringskonto](storage-create-storage-account.md#create-a-storage-account) i [Om Azure-lagringskonton](storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Installera Azure CLI 2.0

Hämta och installera Azure CLI 2.0 genom att följa instruktionerna i [installera Azure CLI 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Om du har problem med installationen kan ta en titt på [felsökning för installationen](/cli/azure/install-az-cli2#installation-troubleshooting) i artikeln, och [installera felsökning](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) guide på GitHub.
>

## <a name="working-with-the-cli"></a>Arbeta med CLI

När du har installerat CLI, kan du använda den `az` i din kommandoradsgränssnittet (Bash, Terminal, Kommandotolken) för att komma åt Azure CLI-kommandona. Typ av `az` kommandot för att se en fullständig lista över grundläggande kommandon (följande exempel utdata har trunkerats):

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

Kör kommandot i din kommandoradsgränssnittet `az storage --help` listan den `storage` kommandot undergrupper. Beskrivningar av undergrupperna ger en översikt över funktionen Azure CLI tillhandahåller för att arbeta med dina lagringsresurser.

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

## <a name="connect-the-cli-to-your-azure-subscription"></a>Ansluta CLI på Azure-prenumerationen

Om du vill arbeta med resurserna i din Azure-prenumeration, du måste först logga in på ditt Azure-konto med `az login`. Det finns flera sätt som du kan logga in:

* **Interaktiv inloggning**: `az login`
* **Logga in med användarnamn och lösenord**: `az login -u johndoe@contoso.com -p VerySecret`
  * Detta fungerar inte med Microsoft-konton eller konton som använder multi-Factor authentication.
* **Logga in med ett huvudnamn för tjänsten**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Azure CLI 2.0 exempelskript

Nu ska arbetar vi med ett litet kommandoskript som utfärdar några grundläggande Azure CLI 2.0-kommandon för att interagera med Azure Storage-resurser. Skriptet först skapar en ny behållare på ditt lagringskonto och överför en befintlig fil (som en blob) till behållaren. Den visar en lista över alla blobbar i behållaren och slutligen hämtas filen till en plats på den lokala datorn som du anger.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

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

**Konfigurera och köra skriptet**

1. Öppna valfri textredigerare, och sedan kopiera och klistra in det här skriptet i redigeraren.

2. Därefter uppdatera skriptvariabler för att återspegla konfigurationsinställningarna. Ersätt följande värden som anges:

   * **\<storage_account_name\>**  namnet på ditt lagringskonto.
   * **\<storage_account_key\>**  primära eller sekundära åtkomstnyckeln för ditt lagringskonto.
   * **\<container_name\>**  A namnge den nya behållaren att skapa, till exempel ”azure-cli-exemplet-container”.
   * **\<blob_name\>**  ett namn för mål-blob-behållare.
   * **\<file_to_upload\>**  sökvägen till en liten fil på den lokala datorn som ”~ / images/HelloWorld.png”.
   * **\<destination_file\>**  mål-sökvägen, exempelvis ”~ / downloadedImage.png”.

3. När du har uppdaterat de nödvändiga variablerna, spara skriptet och avsluta redigeringsprogram. Nästa steg förutsätter att du har namngett skriptet **my_storage_sample.sh**.

4. Markera skriptet som körbara, om det behövs: `chmod +x my_storage_sample.sh`

5. Kör skriptet. Till exempel i Bash: `./my_storage_sample.sh`

Du bör se utdata som liknar följande, och **\<destination_file\>** du angav i skriptet ska visas på den lokala datorn.

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
> Föregående utdata är i **tabell** format. Du kan ange vilka utdata format du ska använda genom att ange den `--output` argument i CLI-kommandon eller Ställ in den globalt med `az configure`.
>

## <a name="manage-storage-accounts"></a>Hantera lagringskonton

### <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto
Om du vill använda Azure Storage behöver du ett lagringskonto. Du kan skapa ett nytt Azure Storage-konto när du har konfigurerat datorn [ansluta till din prenumeration](#connect-to-your-azure-subscription).

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [Krävs]: plats. Till exempel ”USA, västra”.
* `--name` [Krävs]: lagringskontonamn. Namnet måste bestå av 3 till 24 tecken och Använd endast gemener alfanumeriska tecken.
* `--resource-group` [Krävs]: namnet på resursgruppen.
* `--sku` [Krävs]: lagringskontot SKU. Tillåtna värden:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`


### <a name="set-default-azure-storage-account-environment-variables"></a>Ange standardkontot för Azure storage miljövariabler
Du kan ha flera lagringskonton i Azure-prenumerationen. Du kan ange dessa miljövariabler för att välja en av dem ska användas för alla efterföljande lagring-kommandon:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Det är ett annat sätt att ange standardkontot för lagring med hjälp av en anslutningssträng. Först hämta anslutningssträngen med den `show-connection-string` kommando:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Kopiera anslutningssträngen utdata och ange den `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln (du kan behöva anslutningssträngen omges av citattecken):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Alla exempel i följande avsnitt i den här artikeln förutsätter att du har ställt in den `AZURE_STORAGE_ACCOUNT` och `AZURE_STORAGE_ACCESS_KEY` miljövariabler.
>

## <a name="create-and-manage-blobs"></a>Skapa och hantera blobbar
Azure Blob storage är en tjänst för att lagra stora mängder Ostrukturerade data, till exempel text eller binära data som kan nås från var som helst i världen via HTTP eller HTTPS. Det här avsnittet förutsätter att du redan är bekant med Azure Blob storage-koncept. Detaljerad information finns i [komma igång med Azure Blob storage med hjälp av .NET](../blobs/storage-dotnet-how-to-use-blobs.md) och [Blob-Tjänstkoncept](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Skapa en behållare
Varje blobb i Azure-lagring måste vara i en behållare. Du kan skapa en behållare med hjälp av den `az storage container create` kommando:

```azurecli
az storage container create --name <container_name>
```

Du kan ange en av tre nivåer för läsbehörighet för en ny behållare genom att ange den valfria `--public-access` argument:

* `off` (standard): behållardata är privat för ägare.
* `blob`: Offentlig läsbehörighet för blobbar.
* `container`: Läs- och offentlig åtkomst till hela behållaren.

Mer information finns i [Hantera anonym läsbehörighet till behållare och blobbar](../blobs/storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Ladda upp en blob till en behållare
Azure Blob storage stöder block, Lägg till och sidblobbar. Överför blobbar till en behållare med hjälp av den `blob upload` kommando:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

 Som standard den `blob upload` kommandot överför *.vhd filer till sidblobbar eller blockblobbar på annat sätt. Om du vill ange en annan typ när du överför en blob, kan du använda den `--type` argumentet--tillåtna värden är `append`, `block`, och `page`.

 Mer information om de olika blob-typerna finns [förstå Blockblobbar, Tilläggsblobbar och Sidblobbar](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Hämta en blob från en behållare
Det här exemplet visar hur du hämtar en blob från en behållare:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Visa blobbar i en behållare med den [az storage blob listan](/cli/azure/storage/blob#az_storage_blob_list) kommando.

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Kopiera BLOB
Du kan kopiera blobar i eller mellan lagringskonton och regioner asynkront.

Följande exempel visar hur du kopierar blobar från ett lagringskonto till ett annat. Vi skapar först en behållare i källagringskontot där vi anger offentlig läsbehörighet för dess blobar. Efter det laddar vi upp en fil till behållaren och slutligen kopierar vi bloben från den behållaren till en behållare i mållagringskontot.

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

I exemplet ovan finnas målbehållare redan i mål-lagringskontot för kopieringen ska lyckas. Dessutom måste källbloben som anges i `--source-uri`-argumentet antingen inkludera en signatur för delad åtkomst (SAS) eller vara offentligt åtkomlig, som i det här exemplet.

### <a name="delete-a-blob"></a>Ta bort en blob
Om du vill ta bort en blobb, använder den `blob delete` kommando:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Skapa och hantera filresurser
Azure Files erbjuder delad lagring för program som använder Server Message Block (SMB)-protokollet. Microsoft Azure-datorer och molntjänster, samt lokala program kan dela fildata via monterade resurser. Du kan hantera filresurser och fildata via Azure CLI. Mer information om Azure-filer finns [introduktion till Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Skapa en filresurs
En filresurs i Azure är en SMB-filresurs i Azure. Alla kataloger och filer måste skapas i en filresurs. Ett konto kan innehålla ett obegränsat antal resurser och en resurs kan lagra ett obegränsat antal filer, upp till lagringskontot kapacitetsgränser. Följande exempel skapar en filresurs med namnet **minresurs**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Skapa en katalog
En katalog innehåller en hierarkisk struktur i ett Azure-filresursen. I följande exempel skapas en katalog med namnet **MinMapp** i filresursen.

```azurecli
az storage directory create --name myDir --share-name myshare
```

En katalogsökväg kan innehålla flera nivåer, till exempel **dir1/dir2**. Dock måste du kontrollera att alla överordnade kataloger finns innan du skapar en underkatalog. Till exempel för sökvägen **dir1/dir2**, måste du först skapa directory **dir1**, skapa katalog **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Ladda upp en lokal fil till en resurs
I följande exempel överför en fil från **~/temp/samplefile.txt** roten för den **minresurs** filresurs. Den `--source` argumentet anger den befintliga lokala fil att överföra.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Med directory skapas ange som en sökväg på resursen att överföra filen till en befintlig katalog på resursen:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

En fil i resursen kan vara upp till 1 TB i storlek.

### <a name="list-the-files-in-a-share"></a>Visa filer i en resurs
Du kan visa en lista över filer och kataloger på en resurs med hjälp av den `az storage file list` kommando:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Kopiera filer      
Du kan kopiera en fil till en annan fil, en fil till en blobb eller en blobb till en fil. Till exempel att kopiera en fil till en katalog på en annan resurs:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Skapa resursen ögonblicksbild
Du kan skapa en ögonblicksbild av en resurs med hjälp av den `az storage share snapshot` kommando:

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

### <a name="list-share-snapshots"></a>Lista resursen ögonblicksbilder

Du kan visa resursen ögonblicksbilder av en viss filresurs med hjälp av `az storage share list --include-snapshots`

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

### <a name="browse-share-snapshots"></a>Bläddra resursen ögonblicksbilder
Du kan även bläddra i en viss resurs ögonblicksbild för att visa dess innehåll med hjälp av `az storage file list`. Behöver ange resursnamnet `--share-name <snare name>` och tidsstämpeln `--snapshot '2017-10-04T19:45:18.0000000Z'`

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
### <a name="restore-from-share-snapshots"></a>Återställa från resursen ögonblicksbilder

Du kan återställa en fil genom att kopiera eller ladda ned en fil från en resurs med verktyget `az storage file download` kommando

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
## <a name="delete-share-snapshot"></a>Ta bort resursen ögonblicksbild
Du kan ta bort en ögonblicksbild av en resurs med hjälp av den `az storage share delete` kommandot genom att tillhandahålla `--snapshot` parameter med dela ögonblicksbild tidsstämpel:

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
Här följer några ytterligare resurser för att lära dig mer om hur du arbetar med Azure CLI 2.0.

* [Kom igång med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure CLI 2.0-kommandoreferens](/cli/azure)
* [Azure CLI 2.0 på GitHub](https://github.com/Azure/azure-cli)
