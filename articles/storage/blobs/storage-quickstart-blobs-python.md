---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av Python | Microsoft Docs
description: I den här snabbstarten skapar du ett lagringskonto och en container i objektlagring (Blob). Sedan använder du lagringsklientbiblioteket för Python och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en container.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: tamram
ms.openlocfilehash: 94e9d19302c4a51d1401a88248431f237bf59758
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743518"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med Python

I den här snabbstarten får du lära dig att använda Python för att ladda upp, ladda ned och lista blockblobar i en container i Azure Blob Storage. 

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Kontrollera att du har följande ytterligare krav installerade:

* [Python](https://www.python.org/downloads/)
* [Azure Storage SDK för Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet
[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) i den här snabbstarten är ett grundläggande Python-program.  

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Det här kommandot klonar lagringsplatsen *Azure-Samples/storage-blobs-python-quickstart* till din lokala git-mapp. Om du vill köra Python-programmet öppnar du filen *example.py* i lagringsplatsens rot.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
I programmet måste du ange ditt lagringskontonamn och kontonyckel för att skapa ett `BlockBlobService`-objekt. Öppna filen *example.py* från Solution Explorer i IDE. Ersätt värdena `accountname` och `accountkey` med kontonamnet och nyckeln. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Kör exemplet
Det här exemplet skapar en testfil i mappen *Dokument*. Exempelprogrammet laddar upp testfilen till Blob Storage, listar blobarna i containern och laddar ned filen med ett nytt namn. 

Först installerar du beroenden genom att köra `pip install`:

    pip install azure-storage-blob

Sedan kör du exemplet. Meddelandena ser ut ungefär som i följande utdata:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Kontrollera att de två filerna finns i mappen *Dokument* innan du fortsätter. Du kan öppna dem och se att de är samma.

Du kan också använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com) för att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot. 

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen *example.py* och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Nu går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten
Först skapar du referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra, och vart och ett används av nästa i listan.

* Skapa en instans av objektet **BlockBlobService**, som pekar mot Blob Service i lagringskontot. 

* Skapa en instans av objektet **CloudBlobContainer**, som representerar den container du får åtkomst till. Containrar används för att organisera dina blobar på samma sätt som du använder mappar på datorn för att organisera dina filer.

När du har molnblobcontainern kan du instansiera objektet **CloudBlockBlob** som pekar mot den specifika blob du är intresserad av. Du kan sedan ladda upp, hämta och kopiera bloben efter behov.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om containrar och blobnamn finns i [Namngivning och referens av containrar, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

I det här avsnittet skapar du instanser av objekten, skapar en ny container och anger sedan behörigheter för containern så att blobarna är offentliga. Containern heter **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är vanligast och används i denna snabbstart.  

Om du vill överföra en fil till en blob hämtar du den fullständiga sökvägen genom att slå ihop katalognamnet och filnamnet på den lokala enheten. Du kan sedan ladda upp filen till angiven sökväg med hjälp av metoden `create\_blob\_from\_path`. 

Exempelkoden skapar en lokal fil som ska användas för uppladdning och nedladdning, och lagrar filen som ska laddas upp som `file\_path\_to\_file` och namnet på bloben som `local\_file\_name`. I följande exempel överförs filen till containern med namnet **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Det går att använda flera uppladdningsmetoder med Blob Storage. Om du till exempel har en minnesström kan du kan använda metoden `create\_blob\_from\_stream` snarare än `create\_blob\_from\_path`. 

Blockblobar kan vara så stora som 4,7 TB och kan vara allt från Excel-kalkylblad till stora videofiler. Sidblobar används främst för VHD-filer som backar IaaS-datorer. Tilläggsblobar används för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Hämta en lista över filer i containern med hjälp av metoden `list_blobs`. Den här metoden returnerar en generator. Följande kod hämtar listan över blobar&mdash; och går sedan igenom dem och&mdash;visar namnen på de blobar som har påträffats i en container.  

```python
# List the blobs in the container
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Ladda ned blobarna

Ladda ned blobar till din lokala disk med hjälp av metoden `the get\_blob\_to\_path`. Följande kod laddar ned bloben som överfördes i föregående avsnitt. *_DOWNLOADED* läggs till som ett suffix på blobnamnet så att du kan se båda filerna på den lokala disken. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver blobarna som laddades upp i denna snabbstart kan du ta bort hela containern med hjälp av metoden `delete\_container`. Om du vill ta bort enskilda filer använder du i stället metoden `delete\_blob`.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Resurser för att utveckla Python-tillämpningar med blobar

Se dessa ytterligare resurser för att få mer information om Python-utveckling med Blob-lagring:

### <a name="binaries-and-source-code"></a>Binärfiler och källkod

- Visa, ladda ned och installera [Python-klientens bibliotekskällkod](https://github.com/Azure/azure-storage-python) för Azure Storage på GitHub.

### <a name="client-library-reference-and-samples"></a>Referens och exempel för klientbiblioteket

- Mer information om Python-klientbiblioteket finns i [API-referens för Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Utforska [Bloblagringsexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) som skrivits med Python-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg
 
I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Python. Om du vill veta mer om att arbeta med Blob Storage kan du fortsätta till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](./storage-python-how-to-use-blob-storage.md)
 
Mer information om Storage Explorer och blobar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
