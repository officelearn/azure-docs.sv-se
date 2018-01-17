---
title: "Snabbstart för Azure – Överföra objekt till och från Azure Blob Storage med hjälp av Ruby | Microsoft Docs"
description: "Lär dig snabbt hur du överför objekt till och från Azure Blob Storage med hjälp av Ruby"
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: quickstart
ms.date: 12/7/2017
ms.author: v-ruogun
ms.openlocfilehash: 3b0bc01047b9aa7459cf6cc33f004cf7506e5826
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-ruby"></a>Överföra objekt till och från Azure Blob Storage med hjälp av Ruby
I den här snabbstarten får du lära dig att använda Ruby för att ladda upp, hämta och lista blockblobar i Azure Blob-lagring. 

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du: 
* Installera [Ruby](https://www.ruby-lang.org/en/downloads/)
* Installera [Azure Storage-biblioteket för Ruby](https://docs.microsoft.com/azure/storage/blobs/storage-ruby-how-to-use-blob-storage#configure-your-application-to-access-storage) med hjälp av rubygem-paketet. 

```
gem install azure-storage
```

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet
[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) som används i den här snabbstarten är ett enkelt Ruby-program.  

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Det här kommandot klonar lagret till den lokala git-mappen. Om du vill öppna Ruby-exempelprogrammet ska du leta efter mappen storage-blobs-ruby-quickstart och öppna filen example.rb.  

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
I programmet måste du ange ditt lagringskontonamn och kontonyckeln för att skapa `Client`-instansen för programmet. Öppna filen `example.rb` från Solution Explorer i IDE. Ersätt värdena **accountname** och **accountkey** med ditt kontonamn och din nyckel. 

```ruby 
client = Azure::Storage.client(storage_account_name: account_name, storage_access_key: account_key)
```

## <a name="run-the-sample"></a>Kör exemplet
Det här exemplet skapar en testfil i mappen ”Dokument”. Exempelprogrammet laddar upp testfilen till Blob Storage, listar blobarna i behållaren och laddar ned filen med ett nytt namn. 

Kör exemplet. Följande utdata är ett exempel på utdata som returneras när programmet körs:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
När du trycker på valfri tangent för att fortsätta tar exempelprogrammet bort behållaren och filerna. Kontrollera att de två filerna finns i mappen ”Dokument” innan du fortsätter. Du kan öppna dem och se att de är identiska.

Du kan också använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com) för att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot. 

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen example.rb och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten
Det första du ska göra är att skapa referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra, och vart och ett används av nästa i listan.

* Skapa en instans av **klientobjektet** av Azure Storage för att konfigurera autentiseringsuppgifter för anslutning. 
* Skapa objektet **BlobService**, som pekar mot Blob Service i lagringskontot. 
* Skapa objektet **Container**, som representerar den behållare du får åtkomst till. Behållare används för att organisera dina blobar på samma sätt som du använder mappar på datorn för att organisera dina filer.

När du har Cloud Blob-behållaren kan du skapa blobobjektet **Block** som pekar mot den specifika blob du är intresserad av, och utföra åtgärder som att ladda upp, ladda ned och kopiera.

> [!IMPORTANT]
> Behållarnamn måste använda gemener. Mer information om behållare och blobnamn finns i [Namngivning och referens av behållare, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

I det här avsnittet skapar du en instans av Azure Storage-klienten samt av blobtjänstobjektet, skapar en ny behållare och anger sedan behörigheter för behållaren så att blobarna är offentliga. Behållaren heter **quickstartblobs**. 

```ruby 
# Setup a specific instance of an Azure::Storage::Client
client = Azure::Storage.client(storage_account_name: account_name, storage_access_key: account_key)

# Create the BlobService that represents the Blob service for the storage account
blob_service = client.blob_client

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
container = blob_service.create_container(container_name)   

# Set the permission so the blobs are public.
blob_service.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i behållaren

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är vanligast och används i denna snabbstart.  

Om du vill överföra en fil till en blob hämtar du filens fullständiga sökväg genom att slå ihop katalognamnet och filnamnet på den lokala enheten. Du kan sedan överföra filen till angiven sökväg med hjälp av metoden **create\_block\_blob()**. 

Exempelkoden skapar en lokal fil som ska användas för uppladdning och nedladdning, och lagrar filen som ska laddas upp som **file\_path\_to\_file** och namnet på bloben som **local\_file\_name**. I följande exempel överförs filen till behållaren med namnet **quickstartblobs**.

```ruby
# Create a file in Documents to test the upload and download.
local_path=File.expand_path("~/Documents")
local_file_name ="QuickStart_" + SecureRandom.uuid + ".txt"
full_path_to_file =File.join(local_path, local_file_name)

# Write text to the file.
file = File.open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

puts "Temp file = " + full_path_to_file
puts "\nUploading to Blob storage as blob" + local_file_name

# Upload the created file, using local_file_name for the blob name
blob_service.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Om du vill utföra en deluppdatering av innehållet i en blockblob ska du använda metoden **create\_block\_list()**. Blockblobar kan vara så stora som 4,7 TB och kan vara allt från Excel-kalkylblad till stora videofiler. Sidblobar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Tilläggsblobar används för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. Tilläggsblobar ska användas i en enskild skrivmodell. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare

Du kan hämta en lista över filer i behållaren med hjälp av metoden **list\_blobs()**. Följande kod hämtar listan över blobar och går sedan igenom dem och visar namnen på de blobar som har påträffats i en behållare.  

```ruby
# List the blobs in the container
puts "\n List blobs in the container"
blobs = blob_service.list_blobs(container_name)
blobs.each do |blob|
    puts "\t Blob name #{blob.name}"   
end  
```

### <a name="download-the-blobs"></a>Ladda ned blobarna

Ladda ned blobar till din lokala disk med hjälp av metoden **get\_blob()**. Följande kod laddar ned bloben som överfördes i föregående avsnitt. ”_DOWNLOADED” läggs till som ett suffix på blobnamnet så att du kan se båda filerna på den lokala disken. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_service.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver blobarna som laddades upp i denna snabbstart kan du ta bort hela behållaren med hjälp av metoden **delete\_container()**. Om du inte längre behöver filerna som skapades använder du metoden **delete\_blob()** för att ta bort filerna.

```ruby
# Clean up resources. This includes the container and the temp files
blob_service.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```

## <a name="next-steps"></a>Nästa steg
 
I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Ruby. Om du vill veta mer om att arbeta med Blob Storage kan du gå vidare till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder](./storage-ruby-how-to-use-blob-storage.md)


Mer information om Storage Explorer och blobar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
