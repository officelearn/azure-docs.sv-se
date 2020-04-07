---
title: Azure Event Grid blob storage händelseschema
description: Beskriver de egenskaper som tillhandahålls för blob-lagringshändelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 71aa937536f35c9af44adb5822ce7a2bb8f3a9eb
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756000"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid-händelseschema för Blob-lagring

Den här artikeln innehåller egenskaper och schema för blob-lagringshändelser.En introduktion till händelsescheman finns i [Azure Event Grid-händelseschema](event-schema.md).

En lista över exempelskript och självstudier finns i [Lagringshändelsekälla](event-sources.md#storage).

>[!NOTE]
> Endast lagringskonton av typen **StorageV2 (allmänt ändamål v2),** **BlockBlobStorage**och **BlobStorage** stöder händelseintegrering. **Lagring (genral syfte v1)** stöder *inte* integrering med Event Grid.

## <a name="list-of-events-for-blob-rest-apis"></a>Lista över händelser för Blob REST API:er

Dessa händelser utlöses när en klient skapar, ersätter eller tar bort en blob genom att anropa Blob REST API:er.

 |Händelsenamn |Beskrivning|
 |----------|-----------|
 |**Microsoft.Storage.BlobSkapad** |Utlöses när en blob skapas eller ersätts. <br>Den här händelsen utlöses när klienter `PutBlob` `PutBlockList`använder `CopyBlob` , eller åtgärder som är tillgängliga i Blob REST API.   |
 |**Microsoft.Storage.BlobDeleted** |Utlöses när en blob tas bort. <br>Den här händelsen utlöses när klienter `DeleteBlob` anropar åtgärden som är tillgänglig i Blob REST API. |

> [!NOTE]
> Om du vill vara säker på att händelsen **Microsoft.Storage.BlobCreated** endast utlöses när en `CopyBlob`Block `PutBlob`Blob är helt genomförd filtrerar du händelsen för API-anropen , och `PutBlockList` REST. Dessa API-anrop utlöser händelsen **Microsoft.Storage.BlobSkaperad** först när data har gjorts helt åtada till en Block Blob. Mer information om hur du skapar ett filter finns i [Filtrera händelser för händelserutnät](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista över händelser för Azure Data Lake Storage Gen 2 REST API:er

Dessa händelser utlöses om du aktiverar ett hierarkiskt namnområde på lagringskontot och klienter anropar Azure Data Lake Storage Gen2 REST API:er. Mer information om Azure Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

|Händelsenamn|Beskrivning|
|----------|-----------|
|**Microsoft.Storage.BlobSkapad** | Utlöses när en blob skapas eller ersätts. <br>Den här händelsen utlöses när klienter `CreateFile` `FlushWithClose` använder och åtgärder som är tillgängliga i AZURE Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobDeleted** |Utlöses när en blob tas bort. <br>Den här händelsen utlöses också när `DeleteFile` klienter anropar åtgärden som är tillgänglig i AZURE Data Lake Storage Gen2 REST API. |
|**Microsoft.Storage.BlobRenamed**|Utlöses när en blob har bytt namn. <br>Den här händelsen utlöses när klienter `RenameFile` använder åtgärden som är tillgänglig i AZURE Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectorySkapad**|Utlöses när en katalog skapas. <br>Den här händelsen utlöses när klienter `CreateDirectory` använder åtgärden som är tillgänglig i AZURE Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryRenamed**|Utlöses när en katalog har bytt namn. <br>Den här händelsen utlöses när klienter `RenameDirectory` använder åtgärden som är tillgänglig i AZURE Data Lake Storage Gen2 REST API.|
|**Microsoft.Storage.DirectoryDeleted**|Utlöses när en katalog tas bort. <br>Den här händelsen utlöses när klienter `DeleteDirectory` använder åtgärden som är tillgänglig i AZURE Data Lake Storage Gen2 REST API.|

> [!NOTE]
> Om du vill vara säker på att händelsen **Microsoft.Storage.BlobCreated** utlöses först när en `FlushWithClose` Block Blob är helt genomförd filtrerar du händelsen för REST API-anropet. Det här API-anropet utlöser händelsen **Microsoft.Storage.BlobSkaperad** först när data har gjorts helt åtada till en Block Blob. Mer information om hur du skapar ett filter finns i [Filtrera händelser för händelserutnät](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Innehållet i ett händelsesvar

När en händelse utlöses skickar tjänsten Event Grid data om händelsen för att prenumerera på slutpunkten.

Det här avsnittet innehåller ett exempel på hur dessa data skulle se ut för varje blob storage-händelse.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobSkapad händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobSkapad händelse (Gen2 för lagring av datasjöer2)

Om blob-lagringskontot har ett hierarkiskt namnområde ser data ut ungefär som i föregående exempel med undantag för dessa ändringar:

* Nyckeln `dataVersion` är inställd på `2`värdet .

* Nyckeln `data.api` är inställd på `CreateFile` `FlushWithClose`strängen eller .

* Nyckeln `contentOffset` ingår i datauppsättningen.

> [!NOTE]
> Om program `PutBlockList` använder åtgärden för att överföra en ny blob till kontot innehåller data inte dessa ändringar.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted-händelse (Gen2 för lagring av datasjö)

Om blob-lagringskontot har ett hierarkiskt namnområde ser data ut ungefär som i föregående exempel med undantag för dessa ändringar:

* Nyckeln `dataVersion` är inställd på `2`värdet .

* Nyckeln `data.api` är inställd på `DeleteFile`strängen .

* Nyckeln `url` innehåller sökvägen `dfs.core.windows.net`.

> [!NOTE]
> Om program `DeleteBlob` använder åtgärden för att ta bort en blob från kontot innehåller data inte dessa ändringar.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectorySkapad händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed-händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted händelse

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Händelseegenskaper

En händelse har följande data på den högsta nivån:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurssökväg till händelsekällan. Det här fältet kan inte skrivas. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | sträng | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för bloblagring. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| api | sträng | Åtgärden som utlöste händelsen. |
| klientRequestId | sträng | ett klienttillförd begärande id för lagrings-API-åtgärden. Det här id:t kan användas för att korrelera till Azure Storage-diagnostikloggar med hjälp av fältet "client-request-id" i loggarna och kan tillhandahållas i klientbegäranden med huvudet "x-ms-client-request-id". Se [Loggformat](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Id | sträng | Tjänstgenererad begärande-ID för lagrings-API-åtgärden. Kan användas för att korrelera till Azure Storage-diagnostikloggar med hjälp av fältet "request-id-header" i loggarna och returneras från att initiera API-anrop i huvudet "x-ms-request-id". Se [Loggformat](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| Etag | sträng | Det värde som du kan använda för att utföra åtgärder villkorligt. |
| Contenttype | sträng | Den innehållstyp som angetts för blobben. |
| innehållLängd | heltal | Storleken på blobben i byte. |
| blobType (blobType) | sträng | Typ av blob. Giltiga värden är antingen "BlockBlob" eller "PageBlob". |
| innehållOffset | nummer | Förskjutningen i byte av en skrivåtgärd som togs vid den punkt där det händelseutlösande programmet slutförde skrivandet till filen. <br>Visas endast för händelser som utlöses på blob-lagringskonton som har ett hierarkiskt namnområde.|
| destinationUrl |sträng | Url:en för filen som finns när åtgärden är klar. Om en fil till exempel byter `destinationUrl` namn innehåller egenskapen url:en till det nya filnamnet. <br>Visas endast för händelser som utlöses på blob-lagringskonton som har ett hierarkiskt namnområde.|
| källaUrl |sträng | Url:en för filen som finns före operationen. Om en fil till exempel har `sourceUrl` bytt namn innehåller den ursprungliga filnamnets url före namnbytet. <br>Visas endast för händelser som utlöses på blob-lagringskonton som har ett hierarkiskt namnområde. |
| url | sträng | Sökvägen till blobben. <br>Om klienten använder ett Blob REST API har url:en den här strukturen: * \<\>storage-account-name\<.blob.core.windows.net/-filnamn för behållarnamn\>/\<\>*. <br>Om klienten använder ett REST-API för datasjölagring har url:en den här strukturen: * \<\>storage-account-name\<.dfs.core.windows.net/ filnamnsfilnamn för filnamnet\>/\<\>*. |
| Rekursiv | sträng | `True`att utföra operationen på alla underordnade kataloger; annars `False`. <br>Visas endast för händelser som utlöses på blob-lagringskonton som har ett hierarkiskt namnområde. |
| Sequencer | sträng | Ett ogenomskinligt strängvärde som representerar den logiska händelsesekvensen för ett visst blob-namn.  Användare kan använda standardsträngjämförelse för att förstå den relativa sekvensen av två händelser på samma blobnamn. |
| lagringDiagnostik | objekt | Diagnostikdata som ibland ingår i Azure Storage-tjänsten. När närvarande, bör ignoreras av händelse konsumenter. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är Event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Prenumerationsschema för Event Grid](subscription-creation-schema.md).
* En introduktion till hur du arbetar med blob-lagringshändelser finns i [Route Blob storage events - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
