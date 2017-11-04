---
title: "Azure händelse rutnätet Händelseschema"
description: "Beskriver de egenskaper som har angetts för händelser med Azure händelse rutnätet"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 10/20/2017
ms.author: babanisa
ms.openlocfilehash: e251cbfe7c4d8dfbd492817a8fa7af48e6b379df
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2017
---
# <a name="azure-event-grid-event-schema"></a>Azure händelse rutnätet Händelseschema

Den här artikeln innehåller egenskaperna och schemat för händelser. Händelser som består av en uppsättning med fem krävs strängegenskaper och ett objekt för data som krävs. Egenskaper är gemensamma för alla händelser från alla utgivare. Dataobjekt som innehåller egenskaper som är specifika för varje utgivare. De här egenskaperna är specifika för resursleverantör, till exempel Azure Storage eller Azure Event Hubs för system-avsnitt.

Händelser skickas till Azure händelse rutnät i en matris som kan innehålla flera händelseobjekt. Om det finns endast en enskild händelse, har en längd på 1 i matrisen. Matrisen kan ha en total storlek på upp till 1 MB. Varje händelse i matrisen är begränsat till 64 KB.
 
## <a name="event-properties"></a>Egenskaper för händelse

Alla händelser som innehåller samma följande översta data:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Avsnittet | Sträng | Fullständigt labbresurs sökvägen till händelsekällan. Det här fältet är skrivskyddat. |
| Ämne | Sträng | Publisher-definierade sökvägen till ämnet för händelsen. |
| Händelsetyp | Sträng | En av de registrerade händelsetyperna för den här händelsekälla. |
| EventTime | Sträng | Den tid som händelsen genereras baserat på leverantörens UTC-tid. |
| id | Sträng | Unik identifierare för händelsen. |
| Data | Objektet | Händelsedata är specifika för resursprovidern. |

## <a name="available-event-sources"></a>Tillgängliga händelsekällor

Följande händelsekällor publicera händelser för förbrukning via händelsen rutnätet:

* Resursgrupper (hanteringsåtgärder)
* Azure-prenumerationer (hanteringsåtgärder)
* Händelsehubbar
* Anpassade avsnitt

## <a name="azure-subscriptions"></a>Azure-prenumerationer

Azure-prenumerationer kan nu genererar management händelser från Azure Resource Manager, t.ex. när en virtuell dator skapas eller en lagringskontot tas bort.

### <a name="available-event-types"></a>Tillgängliga händelsetyper

- **Microsoft.Resources.ResourceWriteSuccess**: aktiveras när en resurs skapa eller uppdatera åtgärden lyckas.  
- **Microsoft.Resources.ResourceWriteFailure**: aktiveras när en resurs skapa eller uppdateringsåtgärden misslyckas.  
- **Microsoft.Resources.ResourceWriteCancel**: aktiveras när en resurs skapa eller uppdatera åtgärden har avbrutits.  
- **Microsoft.Resources.ResourceDeleteSuccess**: aktiveras när en resurs borttagningsåtgärd lyckas.  
- **Microsoft.Resources.ResourceDeleteFailure**: aktiveras när en resurs borttagningsåtgärd misslyckas.  
- **Microsoft.Resources.ResourceDeleteCancel**: aktiveras när en resurs delete-åtgärden har avbrutits. Detta händer när en för malldistribution har avbrutits.

### <a name="example-event-schema"></a>Exempel Händelseschema

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="resource-groups"></a>Resursgrupper

Resursgrupper kan nu genererar management händelser från Azure Resource Manager, t.ex. när en virtuell dator skapas eller en lagringskontot tas bort.

### <a name="available-event-types"></a>Tillgängliga händelsetyper

- **Microsoft.Resources.ResourceWriteSuccess**: aktiveras när en resurs skapa eller uppdatera åtgärden lyckas.  
- **Microsoft.Resources.ResourceWriteFailure**: aktiveras när en resurs skapa eller uppdateringsåtgärden misslyckas.  
- **Microsoft.Resources.ResourceWriteCancel**: aktiveras när en resurs skapa eller uppdatera åtgärden har avbrutits.  
- **Microsoft.Resources.ResourceDeleteSuccess**: aktiveras när en resurs borttagningsåtgärd lyckas.  
- **Microsoft.Resources.ResourceDeleteFailure**: aktiveras när en resurs borttagningsåtgärd misslyckas.  
- **Microsoft.Resources.ResourceDeleteCancel**: aktiveras när en resurs delete-åtgärden har avbrutits. Detta händer när en för malldistribution har avbrutits.

### <a name="example-event"></a>Exempel-händelse

```json
[
    {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```



## <a name="event-hubs"></a>Händelsehubbar

Event Hubs händelser är för närvarande orsakat endast när en fil skickas automatiskt till lagring via funktionen avbildning.

### <a name="available-event-types"></a>Tillgängliga händelsetyper

- **Microsoft.EventHub.CaptureFileCreated**: aktiveras när en avbildning fil skapas.

### <a name="example-event"></a>Exempel-händelse

Den här exempelhändelse visar schemat för en Händelsehubbar händelse som aktiveras när en fil lagras i funktionen avbildning: 

```json
[
    {
        "topic": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{event-hubs-ns}",
        "subject": "eventhubs/eh1",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-07-11T00:55:55.0120485Z",
        "id": "bd440490-a65e-4c97-8298-ef1eb325673c",
        "data": {
            "fileUrl": "https://gridtest1.blob.core.windows.net/acontainer/eventgridtest1/eh1/1/2017/07/11/00/54/54.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 0,
            "eventCount": 0,
            "firstSequenceNumber": -1,
            "lastSequenceNumber": -1,
            "firstEnqueueTime": "0001-01-01T00:00:00",
            "lastEnqueueTime": "0001-01-01T00:00:00"
        },
    }
]

```


## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="available-event-types"></a>Tillgängliga händelsetyper

- **Microsoft.Storage.BlobCreated**: aktiveras när en blob har skapats.
- **Microsoft.Storage.BlobDeleted**: aktiveras när en blob tas bort.

### <a name="example-event"></a>Exempel-händelse

Den här händelsen i exemplet visar schemat för en lagring händelse som aktiveras när en blob skapas: 

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```




## <a name="custom-topics"></a>Anpassade avsnitt

Datanyttolasten av din anpassade händelser som du definierar och kan vara en formaterad JSON-objekt. Informationen på den översta nivån ska innehålla samma fält som standard resurs användardefinierade händelser. När du publicerar händelser till anpassade avsnitt bör du modellera ämnet händelserna för att underlätta Routning och filtrering.

### <a name="example-event"></a>Exempel-händelse

I följande exempel visas en händelse för en anpassad avsnittet:
````json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.EventGrid/topics/myeventgridtopic",
    "subject": "/myapp/vehicles/motorcycles",    
    "id": "b68529f3-68cd-4744-baa4-3c0498ec19e2",
    "eventType": "recordInserted",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "data":{
      "make": "Ducati",
      "model": "Monster"
    }
  }
]

````

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure händelse rutnätet finns [vad är händelsen rutnätet?](overview.md).
* Mer information om hur du skapar en prenumeration på Azure händelse rutnätet finns [händelse rutnätet prenumeration schemat](subscription-creation-schema.md).
