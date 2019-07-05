---
title: Azure Event Grid blob storage Händelseschema
description: Beskriver de egenskaper som har angetts för blob storage-händelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445770"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid-Händelseschema för Blob storage

Den här artikeln innehåller egenskaperna och schemat för blob storage-händelser. En introduktion till Händelsescheman i [Azure Event Grid Händelseschema](event-schema.md).

En lista över exempel på skript och självstudier finns i [Storage händelsekälla](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Lista över händelser för Blob REST API: er

Dessa händelser utlöses när en klient skapar, ersätter eller tar bort en blob genom att anropa Blob REST API: er.

 |Händelsenamn |Beskrivning|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Utlöses när en blob skapas eller ersatts. <br>Mer specifikt den här händelsen utlöses när klienter använder den `PutBlob`, `PutBlockList`, eller `CopyBlob` åtgärder som är tillgängliga i Blob REST API.   |
 |**Microsoft.Storage.BlobDeleted** |Utlöses när en blob tas bort. <br>Mer specifikt den här händelsen utlöses när klienter anropar den `DeleteBlob` åtgärd som är tillgängliga i Blob REST API. |

> [!NOTE]
> Om du vill se till att den **Microsoft.Storage.BlobCreated** händelsen utlöses endast när en Blockblob är helt allokerade, filtrera händelsen för den `CopyBlob`, `PutBlob`, och `PutBlockList` REST API-anrop. Dessa utlösare för API-anrop i **Microsoft.Storage.BlobCreated** händelse efter att data är helt allokerade till en Blockblob. Läs hur du skapar ett filter i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista över händelser för Azure Data Lake Storage Gen 2 REST API: er

Dessa händelser utlöses om du aktiverar ett hierarkiskt namnområde för lagringskontot och klienter anropa Azure Data Lake Storage Gen2 REST API: er.

> [!NOTE]
> Dessa händelser finns i offentlig förhandsversion och de är tillgängliga bara den **USA, västra 2** och **USA, västra centrala** regioner.

 |Händelsenamn|Beskrivning|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Utlöses när en blob skapas eller ersatts. <br>Mer specifikt den här händelsen utlöses när klienter använder den `CreateFile` och `FlushWithClose` åtgärder som är tillgängliga i Azure Data Lake Storage Gen2 REST API. |
 |**Microsoft.Storage.BlobDeleted** |Utlöses när en blob tas bort. <br>Mer specifikt den här händelsen utlöses också när klienter anropar den `DeleteFile` åtgärd som är tillgängliga i Azure Data Lake Storage Gen2 REST API. |
 |**Microsoft.Storage.BlobRenamed**|Utlöses när en blob har bytt namn. <br>Mer specifikt den här händelsen utlöses när klienter använder den `RenameFile` åtgärd som är tillgängliga i Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft.Storage.DirectoryCreated**|Utlöses när en katalog skapas. <br>Mer specifikt den här händelsen utlöses när klienter använder den `CreateDirectory` åtgärd som är tillgängliga i Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft.Storage.DirectoryRenamed**|Utlöses när en katalog har bytt namn. <br>Mer specifikt den här händelsen utlöses när klienter använder den `RenameDirectory` åtgärd som är tillgängliga i Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft.Storage.DirectoryDeleted**|Utlöses när en katalog tas bort. <br>Mer specifikt den här händelsen utlöses när klienter använder den `DeleteDirectory` åtgärd som är tillgängliga i Azure Data Lake Storage Gen2 REST API.|

> [!NOTE]
> Om du vill se till att den **Microsoft.Storage.BlobCreated** händelsen utlöses endast när en Blockblob är helt allokerade, filtrera händelsen för den `FlushWithClose` REST API-anrop. Den här API-anrop utlösare den **Microsoft.Storage.BlobCreated** händelse efter att data är helt allokerade till en Blockblob. Läs hur du skapar ett filter i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Innehållet i ett svar på händelsen

När en händelse utlöses, skickar tjänsten Event Grid data om händelsen till prenumererande slutpunkt.

Det här avsnittet innehåller ett exempel på hur dessa data skulle se ut för varje blob storage-händelse.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated event

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated event (Data Lake Storage Gen2)

Om blob storage-konto har ett hierarkiskt namnområde kan data liknar det föregående exemplet med undantag för dessa ändringar:

* Den `dataVersion` nyckel har angetts till ett värde av `2`.

* Den `data.api` nyckeln har angetts till strängen `CreateFile` eller `FlushWithClose`.

* Den `contentOffset` nyckeln ingår i datauppsättningen.

> [!NOTE]
> Om program använder den `PutBlockList` åtgärd för att ladda upp en ny blob till kontot data inte innehåller de här ändringarna.

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
    "eTag": "0x8D4BCC2E4835CD0",
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

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted event

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted event (Data Lake Storage Gen2)

Om blob storage-konto har ett hierarkiskt namnområde kan data liknar det föregående exemplet med undantag för dessa ändringar:

* Den `dataVersion` nyckel har angetts till ett värde av `2`.

* Den `data.api` nyckeln har angetts till strängen `DeleteFile`.

* Den `url` nyckeln innehåller sökvägen `dfs.core.windows.net`.

> [!NOTE]
> Om program använder den `DeleteBlob` åtgärden att ta bort en blob från kontot data inte innehåller de här ändringarna.

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

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed event

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

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated händelse

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed händelse

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

## <a name="event-properties"></a>Egenskaper för händelse

En händelse har följande översta data:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| subject | string | Fullständig resurssökväg till händelsekällan. Det här fältet är skrivskyddat. Event Grid ger det här värdet. |
| subject | string | Publisher-definierade sökvägen till ämne för händelsen. |
| eventType | string | En av typerna som registrerade händelsen för den här händelsekällan. |
| eventTime | string | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | string | Unik identifierare för händelsen. |
| data | objekt | Händelsedata för BLOB storage. |
| dataVersion | string | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| metadataVersion | string | Schemaversion för händelsemetadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Dataobjektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| api | string | Åtgärden som utlöste händelsen. |
| clientRequestId | string | en klient-angivna begäran-id för storage API-åtgärden. Detta id kan användas för att korrelera till Azure Storage med hjälp av ”client-request-id”-fältet i loggarna för diagnostikloggar och kan anges i klientbegäranden med rubriken ”x-ms-client-request-id”. Se [logga Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | string | Tjänstgenererade begäran-id för storage API-åtgärden. Kan användas för att korrelera till Azure Storage diagnostik loggar med ”rubrik-begäran-id”-fältet i loggarna och returneras initierar API-anrop i rubriken ”x-ms-request-id”. Se [logga Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | string | Det värde som du kan använda för att utföra åtgärder villkorligt. |
| contentType | string | Innehållstyp som angetts för blobben. |
| contentLength | heltal | Storleken på blobben i byte. |
| blobType | string | Typ av blob. Giltiga värden är ”BlockBlob” eller ”PageBlob”. |
| contentOffset | nummer | Förskjutningen i byte för skrivning vidtas vid den punkt där programmet händelse utlöser slutfört skrivning till filen. <br>Visas bara för händelser som utlösts på blob storage-konton som har ett hierarkiskt namnområde.|
| destinationUrl |string | Url till den fil som kommer att finnas när åtgärden har slutförts. Om en fil har bytt namn, till exempel den `destinationUrl` egenskapen innehåller URL: en för det nya filnamnet. <br>Visas bara för händelser som utlösts på blob storage-konton som har ett hierarkiskt namnområde.|
| sourceUrl |string | Url till den fil som finns innan åtgärden. Om en fil har bytt namn, till exempel den `sourceUrl` innehåller URL: en för det ursprungliga filnamnet innan namnbytet. <br>Visas bara för händelser som utlösts på blob storage-konton som har ett hierarkiskt namnområde. |
| url | string | Sökvägen till blobben. <br>Om klienten använder en Blob REST-API och URL: en har den här strukturen:  *\<lagringskontonamn-\>.blob.core.windows.net/\<container-name\>/\<filnamn \>* . <br>Om klienten använder en Data Lake Storage REST API och URL: en har den här strukturen:  *\<lagringskontonamn-\>.dfs.core.windows.net/\<system filnamn\> / \<filnamn\>* .
|
| recursive| string| `True` att utföra åtgärden på alla underordnade kataloger. Annars `False`. <br>Visas bara för händelser som utlösts på blob storage-konton som har ett hierarkiskt namnområde. |
| sequencer | string | En täckande strängvärde som representerar den logiska ordningsföljden händelser för ett visst blobnamn.  Användare kan använda vanlig strängjämförelse för att förstå den relativa sekvensen av två händelser på samma blobnamn. |
| storageDiagnostics | object | Diagnostikdata som ibland inkluderas med Azure Storage-tjänsten. När det finns, ska ignoreras av händelsekonsumenter. |

|Egenskap|Typ|Beskrivning|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [vad är Event Grid?](overview.md)
* Läs mer om hur du skapar en Azure Event Grid-prenumeration, [Event Grid prenumerationsschema](subscription-creation-schema.md).
* En introduktion till arbeta med blob storage-händelser finns i [dirigera Blob storage-händelser – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
