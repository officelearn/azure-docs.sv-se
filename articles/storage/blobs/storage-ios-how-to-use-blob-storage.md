---
title: Hur du använder objekt (Blob) lagring från iOS - Azure | Microsoft Docs
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
services: storage
documentationcenter: ios
author: michaelhauss
manager: jeconnoc
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 03/21/2018
ms.author: michaelhauss
ms.openlocfilehash: a15ba7409b4c5f75729b1b40cd2f333c44ae0368
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-blob-storage-from-ios"></a>Hur du använder Blob storage från iOS

Den här artikeln visar hur du utför vanliga scenarier med hjälp av Microsoft Azure Blob storage. Exemplen är skrivna i Objective-C och använda den [Azure Storage-klientbibliotek för iOS](https://github.com/Azure/azure-storage-ios). Scenarier som tas upp inkluderar överföringen, lista, hämtar och tar bort blobbar. Mer information om blobbar finns i [nästa steg](#next-steps) avsnitt. Du kan också hämta de [exempelapp](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) snabbt se användningen av Azure Storage i ett iOS-program.

## <a name="what-is-blob-storage"></a>Vad är Blob storage?

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importera Azure Storage iOS biblioteket i ditt program
Du kan importera iOS-biblioteket för Azure Storage till ditt program genom att använda den [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) eller genom att importera den **Framework** fil. CocoaPod är det rekommenderade sättet som gör det integrera biblioteket enklare, men importera från framework-filen är mindre påverkan för ditt befintliga projekt.

Om du vill använda det här biblioteket, behöver du följande:
- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod
1. Om du inte redan gjort det, [installera CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) på datorn genom att öppna ett terminalfönster och kör följande kommando
    
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

3. Navigera till projektkatalogen i fönstret terminal och kör följande kommando

    ```shell    
    pod install
    ```

4. Stäng om din .xcodeproj är öppen i Xcode. Öppna filen nyligen skapade projekt som har filnamnstillägget .xcworkspace i projektkatalogen. Detta är den fil som du ska arbeta från för nu på.

## <a name="framework"></a>Framework
Ett annat sätt att använda biblioteket är att bygga ramen manuellt:

1. Hämta först eller klona den [lagringsplatsen för azure-lagring – ios](https://github.com/azure/azure-storage-ios).
2. Gå till *azure-lagring – ios* -> *Lib* -> *Azure Storage-klientbibliotek*, och öppna `AZSClient.xcodeproj` i Xcode.
3. I det övre vänstra xcode, ändra schemat från ”Azure Storage-klientbibliotek” till ”Framework”.
4. Skapa projektet (⌘ + B). Detta skapar en `AZSClient.framework` filen på skrivbordet.

Du kan sedan importera filen framework till ditt program genom att göra följande:

1. Skapa ett nytt projekt eller öppna ett befintligt projekt i Xcode.
2. Dra och släpp den `AZSClient.framework` i projektnavigeringen Xcode.
3. Välj *kopiera objekt vid behov*, och klicka på *Slutför*.
4. Klicka på ditt projekt i det vänstra navigeringsfönstret och klicka på den *allmänna* fliken överst i Redigeraren för projektet.
5. Under den *länkade ramverk och bibliotek* klickar du på knappen Lägg till (+).
6. I listan över bibliotek som redan tillhandahålls, söker du efter `libxml2.2.tbd` och lägga till den i projektet.

## <a name="import-the-library"></a>Importera biblioteket 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Om du använder Swift behöver du skapa en datacenterbryggning rubrik och importera < AZSClient/AZSClient.h > det:

1. Skapa en huvudfilen `Bridging-Header.h`, och Lägg till instruktionen ovan import.
2. Gå till den *Versionsinställningar* fliken och Sök efter *Interimshuvudfilen med Objective-C*.
3. Dubbelklicka på fältet i *Interimshuvudfilen med Objective-C* och Lägg till sökvägen till huvudfil: `ProjectName/Bridging-Header.h`
4. Skapa projektet (⌘ + B) om du vill verifiera att rubriken datacenterbryggning hämtades av Xcode.
5. Börja använda biblioteket direkt i Swift-filen, om det behövs ingen importuttryck.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynkrona åtgärder
> [!NOTE]
> Alla metoder som utför en begäran mot tjänsten är asynkrona åtgärder. I kodexemplen hittar du att dessa metoder har en slutförande-hanterare. Kod inuti hanteraren slutförande körs **när** begäran har slutförts. Kod efter slutförande hanteraren kommer att köras **medan** begäran görs.
> 
> 

## <a name="create-a-container"></a>Skapa en behållare
Varje blobb i Azure Storage måste finnas i en behållare. I följande exempel visas hur du skapar en behållare som kallas *newcontainer*, i ditt lagringskonto om den inte redan finns. När du väljer ett namn för din behållare, Tänk också på namngivningsregler som nämns ovan.

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

Du kan bekräfta att det fungerar genom att titta på den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) och verifiera att *newcontainer* finns i listan över behållare för ditt lagringskonto.

## <a name="set-container-permissions"></a>Ange behörigheter för behållaren
En behållare behörigheter har konfigurerats för **privata** åtkomst som standard. Behållare ger dock några olika alternativ för behållaren åtkomst:

* **Privata**: behållare och blob-data kan läsas av endast kontoägaren.
* **BLOB**: Blob-data i den här behållaren kan läsas via anonym begäran, men behållardata är inte tillgänglig. Klienter kan inte räkna upp blobbar i behållaren via anonym begäran.
* **Behållaren**: behållare och blob-data kan läsas via anonym begäran. Klienter kan räkna upp blobbar i behållaren via anonym begäran, men det går inte att räkna upp behållare i lagringskontot.

I följande exempel visas hur du skapar en behållare med **behållare** åtkomstbehörigheter som kommer att tillåta offentlig, skrivskyddad åtkomst för alla användare på Internet:

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

## <a name="upload-a-blob-into-a-container"></a>Ladda upp en blobb till en behållare
Som anges i den [Blob-Tjänstkoncept](#blob-service-concepts) avsnittet Blob Storage erbjuder tre olika typer av blobbar: blockblobbar, tilläggsblobbar och sidblobbar. Azure Storage iOS bibliotek stöder alla tre typer av blobbar. I de flesta fall är blockblob den rekommenderade typen.

I följande exempel visas hur du överför en blockblobb från en NSString. Om det redan finns en blob med samma namn i den här behållaren skrivs innehållet i det här blob över.

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

Du kan bekräfta att det fungerar genom att titta på den [Microsoft Azure Lagringsutforskaren](http://storageexplorer.com) och verifiera att behållaren, *containerpublic*, innehåller blob, *sampleblob*. I det här exemplet använde vi en offentlig behållare, så du kan också kontrollera att programmet fungerar genom att gå till BLOB-URI:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Förutom att överföra en blockblobb från en NSString finns liknande metoder för NSData, NSInputStream eller en lokal fil.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobbarna i en behållare
I följande exempel visas hur du listar alla blobbar i en behållare. När du utför den här åtgärden, Tänk på följande parametrar:     

* **continuationToken** -fortsättning token representerar i var lista åtgärden ska börja. Om ingen token anges, den visar en lista över blobbar från början. Valfritt antal blobbar kan visas från noll upp till en maximal mängd. Även om den här metoden returnerar resultat, om `results.continuationToken` inte är noll, det kan finnas fler blobbar på tjänsten som inte finns.
* **prefixet** -du kan ange prefixet som ska användas för blob-lista. Blobbar som börjar med prefixet visas.
* **useFlatBlobListing** – som anges i den [namnge och referera till behållare och blobbar](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) avsnitt, även om Blob-tjänsten är en platt lagring-schemat, du kan skapa en virtuell hierarki genom att namnge blobbar med sökvägen information. Dock stöds inte platt lista för närvarande inte. Den här funktionen kommer snart. Nu är det här värdet ska vara **Ja**.
* **blobListingDetails** -du kan ange vilka objekt som ska tas med när du visar en lista över blobbar
  * _AZSBlobListingDetailsNone_: Visa endast allokerad blobbar och inte returnerar blobbmetadata.
  * _AZSBlobListingDetailsSnapshots_: Visa spara blobbar och blob ögonblicksbilder.
  * _AZSBlobListingDetailsMetadata_: blob att hämta metadata för varje blob som returneras i listan.
  * _AZSBlobListingDetailsUncommittedBlobs_: Visa bekräftade och ogenomförda blobbar.
  * _AZSBlobListingDetailsCopy_: kopia egenskaper i listan.
  * _AZSBlobListingDetailsAll_: visa en lista över alla tillgängliga allokerat blobbar ogenomförda blobbar och ögonblicksbilder och returnera alla metadata och kopiera status för de blobbar.
* **maxResults** -det maximala antalet resultat som ska returneras för den här åtgärden. Använd -1 om du inte ange en gräns.
* **completionHandler** - kodblocket ska köras med resultatet av åtgärden lista.

I det här exemplet används en hjälpmetod för rekursivt anrop listan blobbar metoden varje gång en fortsättningstoken returneras.

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
I följande exempel visas hur du hämtar en blobb till en NSString-objekt.

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
I följande exempel visas hur du tar bort en behållare.

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
Nu när du har lärt dig hur du använder Blob Storage från iOS följa dessa länkar om du vill veta mer om iOS-bibliotek och Storage-tjänst.

* [Azure Storage-klientbibliotek för iOS](https://github.com/azure/azure-storage-ios)
* [Azure Storage iOS referensdokumentationen](http://azure.github.io/azure-storage-ios/)
* [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage Teamblogg](http://blogs.msdn.com/b/windowsazurestorage)

Om du har frågor om det här biblioteket gärna skicka till vår [MSDN Azure-forumet](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) eller [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Om du har förslag på funktioner för Azure Storage, efter att [Azure Storage Feedback](https://feedback.azure.com/forums/217298-storage/).

