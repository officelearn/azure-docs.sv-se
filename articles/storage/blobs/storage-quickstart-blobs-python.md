---
title: "Azure Quickstart - objekt för överföring till/från Azure Blob storage med hjälp av Python | Microsoft Docs"
description: "Lär dig snabbt att överföra objekt till och från Azure Blob storage med hjälp av Python"
services: storage
documentationcenter: storage
author: ruthogunnnaike
manager: cwatson
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/12/2017
ms.author: v-ruogun
ms.openlocfilehash: 44ec416a814ff6a5fef79ef21e2f54ce4ce4da17
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-python"></a>Överför objekt till/från Azure Blob storage med hjälp av Python
I den här snabbstarten att lära dig hur du använder Python att överföra, hämta och visa en lista med blockblobbar i en behållare i Azure Blob storage. 

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du: 
* Installera [Python](https://www.python.org/downloads/)
* Hämta och installera [Azure Storage SDK för Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Skapa ett lagringskonto med hjälp av Azure portal

Först skapa Allmänt lagringskonto ska användas för denna Snabbstart. 

1. Gå till den [Azure-portalen](https://portal.azure.com) och logga in med ditt Azure-konto. 
2. På navmenyn väljer **ny** > **lagring** > **lagringskonto - blob, fil, tabell, kö**. 
3. Ange ett namn för lagringskontot. Namnet måste vara mellan 3 och 24 tecken långt och får innehålla siffror och gemener. Det måste också vara unikt.
4. Ange `Deployment model` till **Resource manager**.
5. Ange `Account kind` till **generella**.
6. Ange `Performance` till **Standard**. 
7. Ange `Replication` till **lokalt Redundant lagring (LRS)**.
8. Ange `Storage service encryption` till **inaktiverade**.
9. Ange `Secure transfer required` till **inaktiverade**.
10. Välj din prenumeration. 
11. För `resource group`, skapa en ny och ge den ett unikt namn. 
12. Välj den `Location` ska användas för ditt lagringskonto.
13. Kontrollera **fäst på instrumentpanelen** och på **skapa** att skapa ditt lagringskonto. 

När ditt lagringskonto har skapats kan är den fäst på instrumentpanelen. Klicka på den för att öppna den. Under **inställningar**, klickar du på **åtkomstnycklar**. Välj en nyckel och kopiera lagringskontonamnet till Urklipp och klistrar in den i anteckningar för senare användning.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet
Den [exempelprogram](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) används i den här snabbstarten är ett grundläggande Python-program.  

Använd [git](https://git-scm.com/) att hämta en kopia av programmet till din utvecklingsmiljö. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Det här kommandot klonar databasen till din lokala git-mapp. Om du vill öppna programmet Python, leta efter storage-BLOB-python-Snabbstart mappen och example.py filen.  

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
I programmet, du måste ange ditt namn och lagringskontonyckel att skapa en `BlockBlobService` objekt. Öppna den `example.py` filen från Solution Explorer i din IDE. Ersätt den **accountname** och **accountkey** värden med kontonamnet och nyckeln. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Köra exemplet
Det här exemplet skapar en testfil i mappen ”dokument”. Det här exempelprogrammet överför Testfilen till Blob storage, visar en lista över blobbar i behållaren och laddar ned filen med ett nytt namn. 

Kör exemplet. Följande utdata är ett exempel på utdata som returneras när du kör programmet:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
När du trycker på valfri tangent för att fortsätta tas exempel lagringsbehållaren och filer. Innan du fortsätter, kontrollera mappen ”dokument” för de två filerna. Du kan öppna dem och de är identiska.

Du kan också använda ett verktyg som den [Azure Lagringsutforskaren](http://storageexplorer.com) att visa filer i Blob storage. Azure Lagringsutforskaren är ett kostnadsfritt plattformsoberoende verktyg som hjälper dig att komma åt kontoinformationen lagring. 

När du har kontrollerat filerna, trycka på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör du öppna filen example.py titta på koden. 

## <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekt
Det första du gör är att skapa referenser till objekt som används för att komma åt och hantera Blob storage. De här objekten som bygger på varandra och varje används av den nästa i listan.

* Skapa en instans av den **BlockBlobService** -objektet, vilket leder till Blob-tjänsten på ditt lagringskonto. 

* Skapa en instans av den **CloudBlobContainer** objekt som representerar den behållare som du ansluter till. Behållare för att organisera dina blobbar som du använder mappar på din dator för att ordna dina filer.

När du har molnet Blob-behållare kan du initiera den **CloudBlockBlob** objekt som hänvisar till specifika blob som du är intresserad av och utföra åtgärder som att ladda upp, hämta och kopiera.

I det här avsnittet instansiera objekt, skapa en ny behållare och ange behörigheter för behållaren så att blobbarna är offentliga. Behållaren kallas **quickstartblobs**. 


```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
## <a name="upload-blobs-to-the-container"></a>Ladda upp blobbar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobbar är den vanligaste och som används i denna Snabbstart.  

Om du vill överföra en fil till en blob, hämta den fullständiga sökvägen till filen genom att koppla katalognamnet och filnamnet på en lokal hårddisk. Du kan sedan överföra filen till den angivna sökvägen med hjälp av den **skapa\_blob\_från\_sökväg** metod. 

Exempelkoden skapar en lokal fil som ska användas för överföring och hämtning, spara filen ska överföras som **filen\_sökväg\_till\_filen** och namnet på blob som **lokala\_filen\_namn**. I följande exempel överför filen till den behållare som kallas **quickstartblobs**.

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

Det finns flera överför metoder som du kan använda med Blob storage. Till exempel om du har en dataström med minne, du kan använda den **skapa\_blob\_från\_dataströmmen** metoden snarare än den **skapa\_blob\_från\_sökvägen**. 

Blockblobbar kan vara så stor som 4,7 TB och kan vara allt från Excel-kalkylblad till stora videofiler. Sidblobbar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Lägg till blobbar som används för inloggning, till exempel när du vill skriva till en fil och sedan hålla att lägga till mer information. De flesta objekt som lagras i Blob storage är blockblobbar.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Hämta en lista över filer i en behållare med hjälp av den **list_blobs** metod. Den här metoden returnerar en generator. Följande kod hämtar listan över blobbar och loop genom dem, visar namnen på de blobbar som påträffats i en behållare.  

```python
# List the blobs in the container
print("\nList blobs in the container")
    generator = block_blob_service.list_blobs(container_name)
    for blob in generator:
        print("\t Blob name: " + blob.name)
```

## <a name="download-the-blobs"></a>Hämta Blobbarna

Ladda ned blobbar till din lokala disk med hjälp av den **hämta\_blob\_till\_sökväg** metod. Följande kod hämtar blob som överförts i föregående avsnitt. ”_DOWNLOADED” läggs till som ett suffix till blob-namn så att du kan se båda filerna på lokal disk. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

## <a name="clean-up-resources"></a>Rensa resurser
Om du behöver inte längre blobbar på den här snabbstarten, kan du ta bort hela behållaren med hjälp av den **ta bort\_behållare**. Om de filer som skapas inte längre behövs, använder du den **ta bort\_blob** metod för att ta bort filer.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="next-steps"></a>Nästa steg
 
I Snabbstart, du har lärt dig hur du överför filer mellan en lokal disk och Azure blob storage med hjälp av Python. Mer information om hur du arbetar med blob storage fortsätta till Blob storage anvisningar.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](./storage-python-how-to-use-blob-storage.md)
 

Mer information om Lagringsutforskaren och Blobbar finns [hantera Azure-blobblagringsresurser med Lagringsutforskaren](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
