---
title: 'Snabbstart: Azure Blob storage library v12 - Python'
description: I den här snabbstarten får du lära dig hur du använder Azure Blob storage client library version 12 för Python för att skapa en behållare och en blob i Blob (objekt) lagring. Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8daf7380e859cd2f9b5890c716f7b7d95e6c3fe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061336"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Snabbstart: Hantera blobbar med Python v12 SDK

I den här snabbstarten lär du dig hantera blobbar med hjälp av Python. Blobbar är objekt som kan innehålla stora mängder text eller binära data, inklusive bilder, dokument, strömmande media och arkivdata. Du överför, hämtar och listblobar och skapar och tar bort behållare.

[API-referensdokumentation](/python/api/azure-storage-blob) | [Biblioteksnamnkodpaket](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [(Python Package Index)](https://pypi.org/project/azure-storage-blob/) | [Exempel](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Ett Azure Storage-konto. [Skapa ett lagringskonto](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5 eller högre.

> [!NOTE]
> Mer om du vill komma igång med den tidigare SDK-versionen finns i [Snabbstart: Hantera blobbar med Python v2.1 SDK](storage-quickstart-blobs-python-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Inrätta

I det här avsnittet får du hjälp med att förbereda ett projekt för att arbeta med Azure Blob storage-klientbiblioteket v12 för Python.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett Python-program med namnet *blob-quickstart-v12*.

1. Skapa en ny katalog för projektet i ett konsolfönster (till exempel cmd, PowerShell eller Bash).

    ```console
    mkdir blob-quickstart-v12
    ```

1. Växla till den nyskapade *blob-quickstart-v12-katalogen.*

    ```console
    cd blob-quickstart-v12
    ```

1. Skapa en *blob-quickstart-v12* annan katalog som kallas *data*. Det är här blob-datafilerna kommer att skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

Installera Azure Blob storage-klientbiblioteket för Python-paketet medan `pip install` du fortfarande är i programkatalogen med kommandot.

```console
pip install azure-storage-blob
```

Det här kommandot installerar Azure Blob storage-klientbiblioteket för Python-paketet och alla bibliotek som det är beroende av. I det här fallet är det bara Azure-kärnbiblioteket för Python.

### <a name="set-up-the-app-framework"></a>Konfigurera ramverket för appen

Från projektkatalogen:

1. Öppna en ny textfil i kodredigeraren
1. Lägga `import` till utdrag
1. Skapa strukturen för programmet, inklusive grundläggande undantagshantering

    Här är koden:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Spara den nya filen som *blob-quickstart-v12.py* i katalogen *blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob-lagring är optimerad för lagring av stora mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagringskontot
* En container på lagringskontot
* En blob i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blobs-introduction/blob1.png)

Använd följande Python-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` Med klassen kan du manipulera Azure Storage-resurser och blob-behållare.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` Klassen låter dig manipulera Azure Storage-behållare och deras blobbar.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` Klassen låter dig manipulera Azure Storage blobbar.

## <a name="code-examples"></a>Kodexempel

I det här exemplet visar kodavsnitt hur du utför följande med Azure Blob storage-klientbiblioteket för Python:

* [Hämta anslutningssträngen](#get-the-connection-string)
* [Skapa en behållare](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa en lista över blobarna i en container](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutningssträngen för lagringskontot från den miljövariabel som skapas i avsnittet [Konfigurera lagringsanslutningssträngen.](#configure-your-storage-connection-string)

Lägg till den `try` här koden i blocket:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Skapa en container

Bestäm ett namn för den nya behållaren. Koden nedan lägger till ett UUID-värde i behållarnamnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) genom att anropa [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) metoden. Anropa sedan [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) metoden för att faktiskt skapa behållaren i ditt lagringskonto.

Lägg till den här `try` koden i slutet av blocket:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodavsnitt:

1. Skapar en textfil i den lokala katalogen.
1. Hämtar en referens till ett [BlobClient-objekt](/python/api/azure-storage-blob/azure.storage.blob.blobclient) genom att anropa [get_blob_client-metoden](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) på [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) från avsnittet [Skapa en behållare.](#create-a-container)
1. Överför den lokala textfilen till blobben genom att anropa [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) metoden.

Lägg till den här `try` koden i slutet av blocket:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Lista blobbar i behållaren genom att anropa [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) metoden. I det här fallet har bara en blob lagts till i behållaren, så listningsåtgärden returnerar bara den bloben.

Lägg till den här `try` koden i slutet av blocket:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Ladda ned blobbar

Hämta den tidigare skapade blobben genom att anropa [download_blob-metoden.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) Exempelkoden lägger till ett suffix av "DOWNLOAD" i filnamnet så att du kan se båda filerna i det lokala filsystemet.

Lägg till den här `try` koden i slutet av blocket:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar de resurser som appen har skapat genom att ta bort hela behållaren med [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) metoden. Du kan också ta bort de lokala filerna om du vill.

Appen pausar för användarindata genom att anropa `input()` innan blobben, behållaren och de lokala filerna tas bort. Detta är en bra chans att kontrollera att resurserna skapades korrekt, innan de tas bort.

Lägg till den här `try` koden i slutet av blocket:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en testfil i den lokala mappen och överför den till Blob-lagring. I exemplet visas sedan blobbar i behållaren och hämtar filen med ett nytt namn så att du kan jämföra gamla och nya filer.

Navigera till katalogen som innehåller *blob-quickstart-v12.py* filen och `python` kör sedan följande kommando för att köra appen.

```console
python blob-quickstart-v12.py
```

Utdata för appen liknar följande exempel:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Innan du påbörjar rensningsprocessen *data* kontrollerar du datamappen efter de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **Retur för** att ta bort testfilerna och slutför demon.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du laddar upp, laddar ned och listar blobbar med Python.

Om du vill visa exempelappar för Blob-lagring fortsätter du att:

> [!div class="nextstepaction"]
> [Azure Blob storage SDK v12 Python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Mer information finns i [Azure SDK för Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Om du vill ha självstudier, exempel, snabbstarter och annan dokumentation besöker du [Azure for Python Developers](/azure/python/).
