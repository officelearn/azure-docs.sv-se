---
title: Händelse schema för Azure Event Grid Blob Storage
description: Beskriver de egenskaper som har angetts för Blob Storage-händelser med Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: fa638a00e0d35e1d48bc3205ce2a77e7faf5d22e
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718388"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid händelse schema för Blob Storage

Den här artikeln innehåller egenskaper och schema för Blob Storage-händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md).

En lista över exempel skript och självstudier finns i [lagrings händelse källa](event-sources.md#storage).

## <a name="list-of-events-for-blob-rest-apis"></a>Lista över händelser för BLOB REST-API: er

Dessa händelser utlöses när en klient skapar, ersätter eller tar bort en BLOB genom att anropa BLOB REST-API: er.

 |Händelsenamn |Beskrivning|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |Utlöses när en BLOB skapas eller ersätts. <br>Mer specifikt utlöses den här händelsen när klienter använder `PutBlob`-, `PutBlockList`-eller `CopyBlob`-åtgärder som är tillgängliga i BLOB-REST API.   |
 |**Microsoft.Storage.BlobDeleted** |Utlöses när en BLOB tas bort. <br>Mer specifikt utlöses den här händelsen när klienter anropar åtgärden `DeleteBlob` som är tillgänglig i BLOB-REST API. |

> [!NOTE]
> Om du vill se till att händelsen **Microsoft. Storage. BlobCreated** endast utlöses när en block-BLOB är fullständigt allokerad, filtrerar du händelsen för `CopyBlob`-, `PutBlob`-och `PutBlockList` REST API anrop. Dessa API-anrop utlöser händelsen **Microsoft. Storage. BlobCreated** endast efter att data har allokerats till en Block-Blob fullständigt. Information om hur du skapar ett filter finns i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Lista över händelser för Azure Data Lake Storage gen 2 REST-API: er

Dessa händelser utlöses om du aktiverar ett hierarkiskt namn område på lagrings kontot och klienterna anropar Azure Data Lake Storage Gen2 REST-API: er.

> [!NOTE]
> Dessa händelser finns i en offentlig för hands version och de är bara tillgängliga i regionerna USA, västra **2** och **västra centrala USA** .

 |Händelsenamn|Beskrivning|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | Utlöses när en BLOB skapas eller ersätts. <br>Mer specifikt utlöses den här händelsen när klienter använder åtgärderna `CreateFile` och `FlushWithClose` som är tillgängliga i Azure Data Lake Storage Gen2 REST API. |
 |**Microsoft.Storage.BlobDeleted** |Utlöses när en BLOB tas bort. <br>Mer specifikt utlöses den här händelsen när klienter anropar åtgärden `DeleteFile` som är tillgänglig i Azure Data Lake Storage Gen2 REST API. |
 |**Microsoft.Storage.BlobRenamed**|Utlöses när en BLOB får ett nytt namn. <br>Mer specifikt utlöses den här händelsen när klienter använder den `RenameFile`-åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft.Storage.DirectoryCreated**|Utlöses när en katalog skapas. <br>Mer specifikt utlöses den här händelsen när klienter använder den `CreateDirectory`-åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft.Storage.DirectoryRenamed**|Utlöses när en katalog får ett nytt namn. <br>Mer specifikt utlöses den här händelsen när klienter använder den `RenameDirectory`-åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|
 |**Microsoft.Storage.DirectoryDeleted**|Utlöses när en katalog tas bort. <br>Mer specifikt utlöses den här händelsen när klienter använder den `DeleteDirectory`-åtgärd som är tillgänglig i Azure Data Lake Storage Gen2 REST API.|

> [!NOTE]
> Om du vill se till att händelsen **Microsoft. Storage. BlobCreated** endast utlöses när en block-BLOB är fullständigt allokerad filtrerar du händelsen för anropet `FlushWithClose` REST API. Detta API-anrop utlöser händelsen **Microsoft. Storage. BlobCreated** endast efter att data har allokerats till en Block-Blob fullständigt. Information om hur du skapar ett filter finns i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>Innehållet i ett händelse svar

När en händelse utlöses skickar Event Grid-tjänsten data om händelsen för att prenumerera på slut punkten.

Det här avsnittet innehåller ett exempel på hur data ska se ut för varje Blob Storage-händelse.

### <a name="microsoftstorageblobcreated-event"></a>Microsoft. Storage. BlobCreated-händelse

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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobCreated-händelse (Data Lake Storage Gen2)

Om Blob Storage-kontot har ett hierarkiskt namn område, ser data ut ungefär som i föregående exempel med undantag för de här ändringarna:

* Värdet för nyckeln `dataVersion` har angetts till `2`.

* Nyckeln `data.api` anges till strängen `CreateFile` eller `FlushWithClose`.

* Nyckeln `contentOffset` ingår i data uppsättningen.

> [!NOTE]
> Om program använder `PutBlockList`-åtgärden för att överföra en ny blob till kontot, innehåller data inte dessa ändringar.

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

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft. Storage. BlobDeleted-händelse

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft. Storage. BlobDeleted-händelse (Data Lake Storage Gen2)

Om Blob Storage-kontot har ett hierarkiskt namn område, ser data ut ungefär som i föregående exempel med undantag för de här ändringarna:

* Värdet för nyckeln `dataVersion` har angetts till `2`.

* @No__t-0-nyckeln anges till strängen `DeleteFile`.

* @No__t-0-nyckeln innehåller sökvägen `dfs.core.windows.net`.

> [!NOTE]
> Om program använder `DeleteBlob`-åtgärden för att ta bort en BLOB från kontot kommer data inte att innehålla dessa ändringar.

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

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft. Storage. BlobRenamed-händelse

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

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft. Storage. DirectoryCreated-händelse

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft. Storage. DirectoryRenamed-händelse

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

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft. Storage. DirectoryDeleted-händelse

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

## <a name="event-properties"></a>Händelse egenskaper

En händelse har följande data på översta nivån:

| Egenskap | Type | Beskrivning |
| -------- | ---- | ----------- |
| topic | string | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| topic | string | Publisher-definierad sökväg till händelsens ämne. |
| eventType | string | En av de registrerade händelse typerna för den här händelse källan. |
| EventTime | string | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | string | Unikt ID för händelsen. |
| data | object | Händelse data för Blob Storage. |
| dataVersion | string | Data objektets schema version. Utgivaren definierar schema versionen. |
| metadataVersion | string | Schema versionen för händelsens metadata. Event Grid definierar schemat för de högsta nivå egenskaperna. Event Grid ger det här värdet. |

Data-objektet har följande egenskaper:

| Egenskap | Type | Beskrivning |
| -------- | ---- | ----------- |
| api | sträng | Den åtgärd som utlöste händelsen. |
| clientRequestId | sträng | ett ID för begäran som tillhandahållits för Storage API-åtgärden. Detta ID kan användas för att korrelera Azure Storage diagnostikloggar med hjälp av fältet "client-Request-ID" i loggarna och kan tillhandahållas i klient begär Anden med hjälp av huvudet "x-MS-client-Request-ID". Se [logg format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| requestId | sträng | Service-genererat förfrågnings-ID för Storage API-åtgärden. Kan användas för att korrelera Azure Storage diagnostikloggar som använder fältet "Request-ID-huvud" i loggarna och returneras från initiering av API-anrop i huvudet "x-MS-Request-ID". Se [logg format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
| eTag | sträng | Det värde som du kan använda för att utföra åtgärder villkorligt. |
| Innehålls | sträng | Den innehålls typ som angetts för blobben. |
| contentLength | integer | Storleken på blobben i byte. |
| BlobType | sträng | Typ av BLOB. Giltiga värden är antingen "BlockBlob" eller "PageBlob". |
| contentOffset | nummer | Offset i byte för en Skriv åtgärd som tas vid den tidpunkt då programmet för händelse aktivering slutförde skrivning till filen. <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område.|
| destinationUrl |sträng | URL till den fil som ska finnas när åtgärden har slutförts. Om en fil till exempel byter namn, innehåller egenskapen `destinationUrl` URL: en för det nya fil namnet. <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område.|
| sourceUrl |sträng | URL till den fil som finns före åtgärden. Om en fil till exempel får ett nytt namn, innehåller `sourceUrl` URL: en för det ursprungliga fil namnet innan namnbytes åtgärden. <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område. |
| url | sträng | Sökvägen till blobben. <br>Om klienten använder en BLOB-REST API, har URL: en följande struktur: *@no__t -1storage-Account-name\>.blob.core.windows.net/\<container-Name @ no__t-4 @ no__t-5 @ no__t-6file-Name @ no__t-7*. <br>Om klienten använder en Data Lake Storage REST API har URL: en följande struktur: *@no__t -1storage-Account-name\>.dfs.core.windows.net/\<file-system namn @ no__t-4 @ no__t-5 @ no__t-6file-Name @ no__t-7*. |
| recursive | sträng | `True` om du vill utföra åtgärden på alla underordnade kataloger. annars `False`. <br>Visas endast för händelser som utlöses på Blob Storage-konton som har ett hierarkiskt namn område. |
| sequencer | sträng | Ett ogenomskinligt sträng värde som representerar den logiska sekvensen av händelser för ett visst BLOB-namn.  Användare kan använda standard sträng jämförelse för att förstå den relativa sekvensen av två händelser på samma BLOB-namn. |
| storageDiagnostics | object | Diagnostikdata ingår ibland i Azure Storages tjänsten. I förekommande fall ska ignoreras av händelse konsumenter. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Mer information om hur du skapar en Azure Event Grid-prenumeration finns i [Event Grid prenumerations schema](subscription-creation-schema.md).
* En introduktion till att arbeta med Blob Storage-händelser finns i [dirigera Blob Storage-händelser – Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 
