---
title: Azure snabbstart – Skapa en blob i objektlagring med hjälp av Ruby | Microsoft Docs
description: I den här snabbstarten skapar du ett lagringskonto och en container i objektlagring (Blob). Sedan använder du lagringsklientbiblioteket för Ruby och laddar upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 0bde1b7be15d49d82818f26d07c2ec633dc4526c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523271"
---
# <a name="quickstart-upload-download-and-list-blobs-using-ruby"></a>Snabbstart: Ladda upp, ladda ned och lista blobar med Ruby

I den här snabbstarten får du lära dig att använda Ruby för att ladda upp, hämta och lista blockblobar i en container i Azure Blob-lagring. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Kontrollera att du har följande ytterligare krav installerade:

* [Ruby](https://www.ruby-lang.org/en/downloads/)
* [Azure Storage-biblioteket för Ruby]() med hjälp av rubygem-paketet: 

    ```
    gem install azure-storage-blob
    ```
    
## <a name="download-the-sample-application"></a>Hämta exempelprogrammet
[Exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) som används i den här snabbstarten är ett enkelt Ruby-program.  

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git 
```

Det här kommandot klonar lagret till den lokala git-mappen. Om du vill öppna Ruby-exempelprogrammet ska du leta efter mappen storage-blobs-ruby-quickstart och öppna filen example.rb.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
I programmet måste du ange ditt lagringskontonamn och kontonyckeln för att skapa `BlobService`-instansen för programmet. Öppna filen `example.rb` från Solution Explorer i IDE. Ersätt värdena **accountname** och **accountkey** med ditt kontonamn och din nyckel. 

```ruby 
blob_client = Azure::Storage::Blob::BlobService.create(
            storage_account_name: account_name,
            storage_access_key: account_key
          )
```

## <a name="run-the-sample"></a>Kör exemplet
Det här exemplet skapar en testfil i mappen ”Dokument”. Exempelprogrammet laddar upp testfilen till Blob Storage, listar blobarna i containern och laddar ned filen med ett nytt namn. 

Kör exemplet. Följande utdata är ett exempel på utdata som returneras när programmet körs:
  
```
Creating a container: quickstartblobs7b278be3-a0dd-438b-b9cc-473401f0c0e8

Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
När du trycker på valfri tangent för att fortsätta tar exempelprogrammet bort containern och filerna. Kontrollera att de två filerna finns i mappen ”Dokument” innan du fortsätter. Du kan öppna dem och se att de är identiska.

Du kan också använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com) för att visa filerna i blobblagringen. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot. 

När du har kontrollerat filerna trycker du på valfri tangent för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen example.rb och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten
Det första du ska göra är att skapa referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra, och vart och ett används av nästa i listan.

* Skapa en instans av **BlobService** av Azure Storage för att konfigurera autentiseringsuppgifter för anslutning. 
* Skapa objektet **Container**, som representerar den behållare du får åtkomst till. Containrar används för att organisera dina blobar på samma sätt som du använder mappar på datorn för att organisera dina filer.

När du har Cloud Blob-containern kan du skapa blobobjektet **Block** som pekar mot den specifika blob du är intresserad av, och utföra åtgärder som att ladda upp, ladda ned och kopiera.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om containrar och blobnamn finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

I det här avsnittet skapar du en instans av Azure Storage-klienten samt av blobtjänstobjektet, skapar en ny container och anger sedan behörigheter för containern så att blobarna är offentliga. Containern heter **quickstartblobs**. 

```ruby 
# Create a BlobService object
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )

# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs' + SecureRandom.uuid
container = blob_client.create_container(container_name)   

# Set the permission so the blobs are public.
blob_client.set_container_acl(container_name, "container")
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Blob Storage stöder blockblobar, tilläggsblobar och sidblobar. Blockblobar är vanligast och används i denna snabbstart.  

Om du vill överföra en fil till en blob hämtar du filens fullständiga sökväg genom att slå ihop katalognamnet och filnamnet på den lokala enheten. Du kan sedan överföra filen till angiven sökväg med hjälp av metoden **create \_ block \_ BLOB ()** . 

Exempelkoden skapar en lokal fil som ska användas för uppladdning och nedladdning, och lagrar filen som ska laddas upp som **file\_path\_to\_file** och namnet på bloben som **local\_file\_name**. I följande exempel överförs filen till containern med namnet **quickstartblobs**.

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
blob_client.create_block_blob(container.name, local_file_name, full_path_to_file)
```

Om du vill utföra en del uppdatering av innehållet i en Block-Blob använder du metoden **create \_ block \_ List ()** . Blockblobar kan vara så stora som 4,7 TB och kan vara allt från Excel-kalkylblad till stora videofiler. Sidblobar används främst för VHD-filer som används för att säkerhetskopiera virtuella IaaS-datorer. Tilläggsblobar används för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. Tilläggsblobar ska användas i en enskild skrivmodell. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa blobar i en container

Du kan hämta en lista över filer i behållaren med hjälp av **list \_ -blobar ()** -metoden. Följande kod hämtar listan över blobar och går sedan igenom dem och visar namnen på de blobar som har påträffats i en container.  

```ruby
# List the blobs in the container
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-the-blobs"></a>Ladda ned blobarna

Ladda ned blobar till din lokala disk med hjälp av metoden **get\_blob()**. Följande kod laddar ned bloben som överfördes i föregående avsnitt. ”_DOWNLOADED” läggs till som ett suffix på blobnamnet så att du kan se båda filerna på den lokala disken. 

```ruby
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = File.join(local_path, local_file_name.gsub('.txt', '_DOWNLOADED.txt'))

puts "\n Downloading blob to " + full_path_to_file2
blob, content = blob_client.get_blob(container_name,local_file_name)
File.open(full_path_to_file2,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Rensa resurser
Om du inte längre behöver blobarna som laddades upp i den här snabb starten kan du ta bort hela behållaren med hjälp av metoden **Delete \_ container ()** . Om filerna som skapats inte längre behövs använder du metoden **Delete \_ BLOB ()** för att ta bort filerna.

```ruby
# Clean up resources. This includes the container and the temp files
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
File.delete(full_path_to_file2)    
```
## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Resurser för utveckling av Ruby-program med blobbar

Se följande ytterligare resurser för Ruby-utveckling med blobblagring:

- Visa och ladda ned [Ruby-klientbibliotekets källkod](https://github.com/Azure/azure-storage-ruby) för Azure Storage på GitHub.
- Utforska [Blobblagringsexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=ruby&term=blob) som skrivits med Ruby-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg
 
I den här snabbstarten har du lärt dig hur du överför filer mellan en lokal disk och Azure Blob Storage med Ruby. Om du vill veta mer om att arbeta med Blob Storage kan du gå vidare till anvisningarna om Blob Storage.

> [!div class="nextstepaction"]
> [Anvisningar för Blob Storage-åtgärder]()


Mer information om Storage Explorer och blobar finns i [Hantera Azure Blob Storage-resurser med Storage Explorer ](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).