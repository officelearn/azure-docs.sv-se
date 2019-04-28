---
title: Hur du använder objektlagring (Blob) från iOS – Azure | Microsoft Docs
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
author: michaelhauss
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 11/20/2018
ms.author: michaelhauss
ms.subservice: blobs
ms.openlocfilehash: 1ab799ef7eb9d7c591e76ab9180d4e3f4ba6ba59
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122753"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Använda Blob storage från iOS

Den här artikeln visar hur du utför vanliga scenarier med Microsoft Azure Blob storage. Exemplen är skrivna i Objective-C och Använd den [Azure Storage-klientbibliotek för iOS](https://github.com/Azure/azure-storage-ios). Scenarier som omfattas är ladda upp, lista, hämta och tar bort blobbar. Mer information om BLOB-objekt finns i den [nästa steg](#next-steps) avsnittet. Du kan också hämta den [exempelapp](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) att snabbt se användningen av Azure Storage i ett iOS-program.

Mer information om Blob storage finns [introduktion till Azure Blob storage](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importera iOS Azure Storage-biblioteket till ditt program
Du kan importera iOS Azure Storage-biblioteket i ditt program antingen med hjälp av den [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) eller genom att importera den **Framework** fil. CocoaPod är det rekommenderade sättet eftersom det gör att integrera biblioteket enklare, men importera från filen framework är mindre störande för ditt befintliga projekt.

Om du vill använda det här biblioteket, behöver du följande:
- iOS 8+
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Om du inte redan gjort det, [installera CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) på datorn genom att öppna ett terminalfönster och köra följande kommando
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Skapa sedan en ny fil med namnet i projektkatalogen (den katalog som innehåller filen .xcodeproj) _Podfile_(utan filtillägget). Lägg till följande till _Podfile_ och spara.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. I terminalfönstret, navigera till projektkatalogen och kör följande kommando

    ```shell    
    pod install
    ```

4. Om din .xcodeproj är öppen i Xcode kan du stänga den. Öppna filen nyskapade projektet som har tillägget .xcworkspace i projektkatalogen. Detta är den fil som du kommer att arbeta från för nu på.

## <a name="framework"></a>Framework
Det andra sättet att använda biblioteket är att bygga ramverket manuellt:

1. Först ladda ned eller klona den [lagringsplatsen för azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Gå till *azure-storage-ios* -> *Lib* -> *Azure Storage-klientbiblioteket*, och öppna `AZSClient.xcodeproj` i Xcode.
3. Ändra schemat från ”Azure Storage-klientbiblioteket” till ”Framework” i det övre vänstra xcode.
4. Bygga projektet (⌘ + B). Detta skapar en `AZSClient.framework` filen på skrivbordet.

Du kan sedan importera filen framework till ditt program genom att göra följande:

1. Skapa ett nytt projekt eller öppna ett befintligt projekt i Xcode.
2. Dra och släpp den `AZSClient.framework` i projektnavigeringen Xcode.
3. Välj *kopiera objekt vid behov*, och klicka på *Slutför*.
4. Klicka på ditt projekt i det vänstra navigeringsfältet och klicka på den *Allmänt* fliken högst upp i Redigeraren för projektet.
5. Under den *länkade ramverk och bibliotek* klickar du på knappen Lägg till (+).
6. I listan över bibliotek som redan ges, söker du efter `libxml2.2.tbd` och lägga till den i projektet.

## <a name="import-the-library"></a>Importera biblioteket 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Om du använder Swift, behöver du skapa en datacenterbryggning rubrik och importera < AZSClient/AZSClient.h > det:

1. Skapa en rubrikfil `Bridging-Header.h`, och Lägg till ovanstående import.
2. Gå till den *Versionsinställningar* fliken och Sök efter *Interimshuvudfilen med Objective-C*.
3. Dubbelklicka på fältet i *Interimshuvudfilen med Objective-C* och lägga till sökvägen till filen med din rubrik: `ProjectName/Bridging-Header.h`
4. Bygga projektet (⌘ + B) för att verifiera att rubriken datacenterbryggning hämtades av Xcode.
5. Börja använda biblioteket direkt i Swift filer finns det inget behov av importuttryck.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynkrona åtgärder
> [!NOTE]
> Alla metoder som utför en begäran mot tjänsten är asynkrona åtgärder. I kodexemplen hittar du att dessa metoder har en hanterare för slutförande. Koden i hanteraren för slutförande körs **när** begäran har slutförts. Code när hanteraren för slutförande körs **medan** begäran görs.
> 
> 

## <a name="create-a-container"></a>Skapa en container
Varje blobb i Azure Storage måste finnas i en behållare. I följande exempel visas hur du skapar en behållare som kallas *newcontainer*, i ditt Storage-konto om det inte redan finns. När du väljer ett namn för din behållare, Tänk också på namngivningsregler som nämns ovan.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Du kan bekräfta att det fungerar genom att titta på den [Microsoft Azure Lagringsutforskaren](https://storageexplorer.com) och verifiera att *newcontainer* finns i listan över behållare för ditt lagringskonto.

## <a name="set-container-permissions"></a>Ange behörigheter för behållare
Behörigheter för en behållare som är konfigurerade för **privata** åtkomst som standard. Behållare ger dock några olika alternativ för åtkomst till behållare:

* **Privata**: Behållare och blob-data kan läsas av ägare endast.
* **Blob**: BLOB-data i den här behållaren kan läsas via anonym begäran, men behållardata är inte tillgänglig. Klienter kan inte räkna upp blobbar i behållaren via anonyma begäran.
* **behållaren**: Behållare och blob-data kan läsas via anonyma begäran. Klienter kan räkna upp blobbar i behållaren via anonym begäran, men det går inte att räkna upp behållare i lagringskontot.

I följande exempel visas hur du skapar en behållare med **behållare** åtkomstbehörigheter, som gör att offentlig, skrivskyddad åtkomst för alla användare på Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blob till en container
Som vi redan nämnt i begreppsavsnittet för Blob service, Blob Storage erbjuder tre olika typer av blobbar: blockblobbar, tilläggsblobbar och sidblobbar. Azure Storage-biblioteket för iOS har stöd för alla tre typer av blobbar. I de flesta fall är blockblob den rekommenderade typen.

I följande exempel visas hur du överför en blockblob från en NSString. Om en blob med samma namn finns redan i den här behållaren, skrivs innehållet i den här bloben.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Du kan bekräfta att det fungerar genom att titta på den [Microsoft Azure Lagringsutforskaren](https://storageexplorer.com) och kontrollera att behållaren, *containerpublic*, innehåller blob, *sampleblob*. I det här exemplet använde vi en offentlig behållare, så att du kan också kontrollera att det här programmet fungerade genom att gå till BLOB-URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Förutom att ladda upp en blockblob från en NSString finns liknande metoder för NSData, NSInputStream eller en lokal fil.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container
I följande exempel visar hur du lista alla blobar i en behållare. När du utför den här åtgärden, Tänk också på följande parametrar:     

* **continuationToken** -fortsättning token representerar som var liståtgärden ska börja. Om ingen token har angetts och det visas en lista över blobbar från början. Valfritt antal BLOB-objekt kan visas från noll till en maximal mängd. Även om den här metoden returnerar noll resultat om `results.continuationToken` inte är noll, det kan finnas flera blobar på tjänsten som inte visas.
* **prefixet** – du kan ange prefixet som ska användas för blob-lista. Endast de blobbar som börjar med prefixet visas.
* **useFlatBlobListing** – som anges i den [namngivning och referens av behållare och blobbar](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) avsnittet, även om Blob-tjänsten är ett Lagringsschema för platt du kan skapa en virtuell hierarki genom att namnge blobar med sökväg information. Dock stöds inte är fast lista för närvarande inte. Den här funktionen kommer snart. För tillfället detta värde bör vara **Ja**.
* **blobListingDetails** – du kan ange vilka objekt som ska innehålla när lista blobbar
  * _AZSBlobListingDetailsNone_: Lista bara allokerade blobar och inte returnerar blob-metadata.
  * _AZSBlobListingDetailsSnapshots_: Lista allokerade blobar och blob-ögonblicksbilder.
  * _AZSBlobListingDetailsMetadata_: Hämta blob-metadata för varje blob returneras i listan.
  * _AZSBlobListingDetailsUncommittedBlobs_: Lista allokerade och ogenomförda blobar.
  * _AZSBlobListingDetailsCopy_: Inkludera kopiera egenskaper i listan.
  * _AZSBlobListingDetailsAll_: Lista alla tillgängliga allokerade blobs, ogenomförda blobar och ögonblicksbilder och returnerar alla metadata och kopiera status för dessa blobar.
* **maxResults** – det maximala antalet resultat som ska returneras för den här åtgärden. Använda -1 om du inte ange en gräns.
* **completionHandler** – blockeringen av kod att köra med resultaten från samma åtgärd.

I det här exemplet är en hjälpkomponentmetod används för att rekursivt anrop listan över blobar metoden varje gång en fortsättningstoken som returneras.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Ladda ned en blob
I följande exempel visar hur du laddar ned en blob till ett NSString-objekt.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Ta bort en blob
I följande exempel visas hur du tar bort en blob.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Ta bort en blob-behållare
I följande exempel visar hur du tar bort en behållare.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du använder Blob Storage från iOS, kan du följa dessa länkar om du vill veta mer om iOS-klientbibliotek och Storage-tjänsten.

* [Azure Storage-klientbibliotek för iOS](https://github.com/azure/azure-storage-ios)
* [Azure Storage iOS referensdokumentation](https://azure.github.io/azure-storage-ios/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](https://blogs.msdn.com/b/windowsazurestorage)

Om du har frågor om det här biblioteket passa på att publicera till vår [MSDN Azure-forumet](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Om du har förslag på funktioner för Azure Storage kan du publicera till [Azure Storage-Feedback](https://feedback.azure.com/forums/217298-storage/).

