---
title: Så här använder du objektlagring (Blob) från iOS - Azure | Microsoft-dokument
description: Lagra ostrukturerade data i molnet med Azure Blob Storage (objektlagring).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 54085d602246d38adb970ed02f451241ca7ba19d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726407"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Så här använder du Blob-lagring från iOS

Den här artikeln visar hur du utför vanliga scenarier med Microsoft Azure Blob-lagring. Exemplen skrivs i Objective-C och använder [Azure Storage Client Library för iOS](https://github.com/Azure/azure-storage-ios). Scenarierna omfattar uppladdning, listning, nedladdning och borttagning av blobbar. Mer information om blobbar finns i avsnittet [Nästa steg.](#next-steps) Du kan också ladda ned [exempelappen](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) för att snabbt se användningen av Azure Storage i ett iOS-program.

Mer information om Blob-lagring finns i [Introduktion till Azure Blob storage](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importera Azure Storage iOS-biblioteket till ditt program

Du kan importera Azure Storage iOS-biblioteket till ditt program antingen med hjälp av [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) eller genom att importera **Framework-filen.** CocoaPod är det rekommenderade sättet eftersom det gör att integrera biblioteket lättare, men att importera från ramfilen är mindre påträngande för ditt befintliga projekt.

För att kunna använda det här biblioteket behöver du följande:

- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod (cocoaPod)

1. Om du inte redan har gjort det [installerar du CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) på datorn genom att öppna ett terminalfönster och köra följande kommando

    ```shell
    sudo gem install cocoapods
    ```

2. Skapa sedan en ny fil som heter _Podfile_(inget filtillägg) i projektkatalogen (katalogen som innehåller xcodeproj-filen). Lägg till följande i _Podfile_ och spara.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. I terminalfönstret navigerar du till projektkatalogen och kör följande kommando

    ```shell
    pod install
    ```

4. Om xcodeproj är öppen i Xcode stänger du den. Öppna den nyskapade projektfilen i projektkatalogen som har tillägget .xcworkspace. Det här är filen du kommer att arbeta från och med nu.

## <a name="framework"></a>Ramverk

Det andra sättet att använda biblioteket är att bygga ramverket manuellt:

1. Hämta eller klona först [azure-storage-ios repo](https://github.com/azure/azure-storage-ios).
2. Gå till *azure-storage-ios* -> *Lib* -> Azure Storage `AZSClient.xcodeproj` Client*Library*och öppna i Xcode.
3. Längst upp till vänster i Xcode ändrar du det aktiva schemat från "Azure Storage Client Library" till "Framework".
4. Bygg projektet (-+B). Då skapas `AZSClient.framework` en fil på skrivbordet.

Du kan sedan importera ramfilen till ditt program genom att göra följande:

1. Skapa ett nytt projekt eller öppna ditt befintliga projekt i Xcode.
2. Dra och `AZSClient.framework` släpp in i din Xcode-projektnavigator.
3. Välj *Kopiera objekt om det behövs*och klicka på *Slutför*.
4. Klicka på projektet i vänsternavigering och klicka på fliken *Allmänt* högst upp i projektredigeraren.
5. Klicka på knappen Lägg till under avsnittet *Länkade ramverk och bibliotek* (+).
6. Sök efter `libxml2.2.tbd` och lägg till den i projektet i listan över bibliotek som redan finns.

## <a name="import-the-library"></a>Importera biblioteket

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Om du använder Swift måste du skapa ett överbryggande \<huvud och importera AZSClient/AZSClient.h> där:

1. Skapa en `Bridging-Header.h`rubrikfil och lägg till importsatsen ovan.
2. Gå till fliken *Bygginställningar* och sök efter *mål-C-brygghuvud*.
3. Dubbelklicka på fältet *mål-C bridging header* och lägga till sökvägen till din header fil:`ProjectName/Bridging-Header.h`
4. Skapa projektet (-+B) för att kontrollera att brygghuvudet plockades upp av Xcode.
5. Börja använda biblioteket direkt i en Swift-fil, det finns inget behov av importutdrag.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Asynkrona operationer

> [!NOTE]
> Alla metoder som utför en begäran mot tjänsten är asynkrona åtgärder. I kodexemplen, hittar du att dessa metoder har en kompletteringshanterare. Koden i slutförandehanteraren körs **när** begäran har slutförts. Koden efter att slutförandehanteraren körs **medan** begäran görs.

## <a name="create-a-container"></a>Skapa en container

Varje blob i Azure Storage måste finnas i en behållare. I följande exempel visas hur du skapar en behållare, kallad *newcontainer,* i ditt Lagringskonto om den inte redan finns. När du väljer ett namn för din behållare, vara uppmärksam på de namngivningsregler som nämns ovan.

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

Du kan bekräfta att detta fungerar genom att titta på [Microsoft Azure Storage Explorer](https://storageexplorer.com) och verifiera att den nya *behållaren* finns i listan över behållare för ditt lagringskonto.

## <a name="set-container-permissions"></a>Ange behållarbehörigheter

En behållares behörigheter är konfigurerade för **privat** åtkomst som standard. Behållare innehåller dock några olika alternativ för åtkomst till behållare:

- **Privat**: Behållar- och blob-data kan endast läsas av kontoägaren.
- **Blob**: Blob-data i den här behållaren kan läsas via anonym begäran, men behållardata är inte tillgängliga. Klienter kan inte räkna upp blobbar i behållaren via anonym begäran.
- **Behållare:** Container- och blob-data kan läsas via anonym begäran. Klienter kan räkna upp blobbar i behållaren via anonym begäran, men kan inte räkna upp behållare i lagringskontot.

I följande exempel visas hur du skapar en behållare med **behörigheter för behållaråtkomst,** vilket ger offentlig och skrivskyddad åtkomst för alla användare på Internet:

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

Som nämns i avsnittet Blob-tjänstbegrepp erbjuder Blob Storage tre olika typer av blobbar: blockblobar, tilläggsblobbar och sidblobar. Azure Storage iOS-biblioteket stöder alla tre typerna av blobbar. I de flesta fall är blockblob den rekommenderade typen.

I följande exempel visas hur du laddar upp en blockblob från en NSString. Om det redan finns en blob med samma namn i den här behållaren skrivs innehållet i den här blobben över.

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

Du kan bekräfta att detta fungerar genom att titta på [Microsoft Azure Storage Explorer](https://storageexplorer.com) och kontrollera att behållaren, *containerpublic*, innehåller blob, *sampleblob*. I det här exemplet använde vi en offentlig behållare så att du också kan verifiera att det här programmet fungerade genom att gå till blobs URI:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Förutom att ladda upp en blockblob från en NSString finns liknande metoder för NSData, NSInputStream eller en lokal fil.

## <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

I följande exempel visas hur du listar alla blobbar i en behållare. När du utför den här åtgärden bör du tänka på följande parametrar:

- **continuationToken** - Fortsättningstoken representerar var listningsåtgärden ska starta. Om ingen token anges visas blobbar från början. Valfritt antal blobbar kan visas, från noll upp till ett inställt maximum. Även om den här metoden `results.continuationToken` returnerar noll resultat, om den inte är noll, kan det finnas fler blobbar på tjänsten som inte har listats.
- **prefix** - Du kan ange det prefix som ska användas för blob-listning. Endast blobbar som börjar med det här prefixet visas.
- **useFlatBlobListing** - Som nämns i avsnittet [Namngivning och refererande av behållare och blobbar,](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) även om Blob-tjänsten är ett platt lagringsschema, kan du skapa en virtuell hierarki genom att namnge blobbar med sökvägsinformation. En icke-platt notering stöds dock för närvarande inte. Den här funktionen kommer snart. För tillfället bör det här värdet vara **JA**.
- **blobListingDetails** - Du kan ange vilka objekt som ska inkluderas när du listar blobbar
  - _AZSBlobListingDetailsNone_: Lista endast bekräftade blobbar och returnera inte blobmetadata.
  - _AZSBlobListingDetailsSnapshots_: Lista bekräftade blobbar och blob-ögonblicksbilder.
  - _AZSBlobListingDetailsMetadata_: Hämta blobmetadata för varje blob som returneras i listan.
  - _AZSBlobListingDetailsUncommittedBlobs_: Lista engagerade och oengagerade blobbar.
  - _AZSBlobListingDetailsCopy_: Inkludera kopieringsegenskaper i listan.
  - _AZSBlobListingDetailsAll_: Lista alla tillgängliga bekräftade blobbar, oengagerade blobbar och ögonblicksbilder och returnera alla metadata och kopieringsstatus för dessa blobbar.
- **maxResults** - Det maximala antalet resultat som ska returneras för den här åtgärden. Använd -1 för att inte ange en gräns.
- **completionHandler** - Det kodblock som ska köras med resultatet av listningsåtgärden.

I det här exemplet används en hjälpmetod för att rekursivt anropa metoden listblobar varje gång en fortsättningstoken returneras.

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

I följande exempel visas hur du hämtar en blob till ett NSString-objekt.

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

Nu när du har lärt dig hur du använder Blob Storage från iOS följer du dessa länkar för att lära dig mer om iOS-biblioteket och lagringstjänsten.

- [Azure Storage Client Library för iOS](https://github.com/azure/azure-storage-ios)
- [Referensdokumentation för Azure Storage iOS](https://azure.github.io/azure-storage-ios/)
- [REST-API för Azure Storage Services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blogg för Azure Storage Team](https://blogs.msdn.com/b/windowsazurestorage)

Om du har frågor om det här biblioteket är du välkommen att publicera i vårt [MSDN Azure-forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) eller [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Om du har funktionsförslag för Azure Storage kan du publicera i [Azure Storage Feedback](https://feedback.azure.com/forums/217298-storage/).
