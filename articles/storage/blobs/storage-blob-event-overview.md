---
title: Reagera på Azure Blob Storage-händelser | Microsoft Docs
description: Använda Azure händelse rutnätet för att prenumerera på Blob Storage-händelser.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: ea2ec712c8d8b5f85f020535ab0544986f0da53a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="reacting-to-blob-storage-events"></a>Reagera på Blob Storage-händelser

Azure Storage-händelser kan reagera på Skapa och ta bort blobbar med moderna serverlösa arkitekturer program. Detta sker utan att behöva komplicerad kod eller dyrt och ineffektiv avsökning tjänster.  I stället händelser skickas [Azure händelse rutnätet](https://azure.microsoft.com/services/event-grid/) till prenumeranter som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logikappar](https://azure.microsoft.com/services/logic-apps/), eller till och med din egen anpassade http-lyssnare och du bara betala för det du använder. 

Vanliga scenarier för Blob Storage-händelse innehåller bild- eller bearbetning, Sökindexeringen eller något filen indatavärdena arbetsflöde.  Asynkrona filöverföringar är ett bra val för händelser.  När ändringarna är ovanligt, men din situation kräver omedelbara svarstider, kan händelse-baserad arkitektur vara särskilt effektivt.

Tillgänglighet för Storage-händelser som är knutna till händelse rutnätet [tillgänglighet](../../event-grid/overview.md) och ska vara tillgänglig i andra regioner som händelsen rutnätet. Ta en titt på [väg Blob storage-händelser till en egen webbserver endpoint - CLI](storage-blob-event-quickstart.md) eller [väg Blob storage-händelser till en egen webbserver endpoint - PowerShell](storage-blob-event-quickstart-powershell.md) för ett enkelt exempel. 

![Händelsen rutnätet modellen](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob Storage-konton
BLOB storage-händelser finns i [Blob storage-konton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) och i [allmänna lagringskonton för v2](../common/storage-account-options.md#general-purpose-v2). **Allmänna syften v2 (GPv2)** är lagringskonton som stöder alla funktioner för alla lagringstjänster, inklusive Blobbar, filer, köer och tabeller. En **Blob-lagringskonto** är ett specialiserat lagringskonto för att lagra Ostrukturerade data som blobbar (objekt) i Azure Storage. BLOB storage-konton fungerar som allmänna lagringskonton och dela alla bra hållbarhet, tillgänglighet, skalbarhet och prestanda funktioner du använder idag, inklusive 100 procent konsekvent API för blockblobar och tilläggsblobar. För program som bara behöver lagring av block- eller tilläggsblobbar, rekommenderar vi att du använder Blob-lagringskonton. 

## <a name="available-blob-storage-events"></a>Tillgängliga Blob storage-händelser
Används av rutnätet händelse [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) händelse meddelanden till prenumeranter.  BLOB storage händelseprenumerationer kan innehålla två typer av händelser:  

> |Händelsenamnet|Beskrivning|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Utlöses när en blob skapas eller ersättas via den `PutBlob`, `PutBlockList`, eller `CopyBlob` åtgärder|
> |`Microsoft.Storage.BlobDeleted`|Utlöses när en blob tas bort via en `DeleteBlob` åtgärden|

## <a name="event-schema"></a>Händelseschema
BLOB storage-händelser innehåller den information som du måste svara för ändringar i dina data.  Du kan identifiera en händelse för Blob storage eftersom egenskapen eventType börjar med ”Microsoft.Storage”.  
Mer information om användningen av händelse rutnätet händelseegenskaper dokumenteras i [händelse rutnätet Händelseschema](../../event-grid/event-schema.md).  

> |Egenskap|Typ|Beskrivning|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |Avsnittet|sträng|Fullständig Azure Resource Manager-id för det lagringskonto som genererar händelsen.|
> |Ämne|sträng|Resurssökvägen till objektet som är föremål för händelsen, i formatet samma utökade Azure Resource Manager som vi använder för att beskriva storage-konton, tjänster och behållare för Azure RBAC relativa.  Det här formatet innehåller ett bevara blob-namn.|
> |EventTime|sträng|Datum/tid som händelsen skapades i ISO 8601-format|
> |Händelsetyp|sträng|”Microsoft.Storage.BlobCreated” eller ”Microsoft.Storage.BlobDeleted”|
> |Id|sträng|Unik identifierare om den här händelsen|
> |dataVersion|sträng|Schemaversion av dataobjektets primärnycklar.|
> |metadataVersion|sträng|Schemaversionen översta egenskaper.|
> |data|objekt|Insamling av data för blob storage-händelse|
> |data.contentType|sträng|Innehållstypen för blob, som returneras i Content-Type-huvudet från blob|
> |data.contentLength|nummer|Storleken på blobben som heltal som representerar ett antal byte som returneras i Content-Length-huvudet från blob.  Skickas med BlobCreated händelse, men inte med BlobDeleted.|
> |data.url|sträng|URL-adressen för det objekt som omfattas av händelsen|
> |data.eTag|sträng|Etag för objektet när den här händelsen utlöses.  Inte tillgängligt för händelsen BlobDeleted.|
> |data.api|sträng|Namnet på api-åtgärden som utlöste händelsen.  Det här värdet är ”PutBlob”, ”PutBlockList” eller ”CopyBlob” för BlobCreated händelser.  Det här värdet är ”DeleteBlob” för BlobDeleted händelser.  Dessa värden är samma api-namn som finns i Azure Storage diagnostikloggar.  Se [loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|sträng|En täckande värde som representerar logiska händelsesekvens för namn på en viss blob.  Användarna kan använda standard strängjämförelse för att förstå relativa sekvensen av två händelser i samma blob-namn.|
> |data.requestId|sträng|Tjänst som skapade begäran-id för Lagringsåtgärden API.  Kan användas för att korrelera till Azure Storage diagnostik loggar med hjälp av fältet ”-id-huvudet i begäran” i loggarna och returneras från initierar API-anrop i 'x-ms-begäran-id-huvudet. Se [loggformat](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|sträng|Om klienten begärande-id för lagring av API-åtgärd.  Kan användas för att korrelera till Azure Storage diagnostikloggar fältet ”client-request-id” i loggarna och kan anges i klientbegäranden med rubriken ”x-ms-client-request-id”. Se [loggformat](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.storageDiagnostics|objekt|Diagnostikdata ibland ingår som Azure Storage-tjänsten.  När ska ignoreras av händelsekonsumenter.|
|data.blobType|sträng|Typ av blobben. Giltiga värden är ”BlockBlob” eller ”PageBlob”.| 

Här är ett exempel på en BlobCreated händelse:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Mer information finns i [Blob storage-händelser schemat](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtrera händelser
BLOB händelseprenumerationer kan filtreras baserat på vilken typ av händelse och behållarnamn och blobbnamnet på det objekt som har skapats eller tagits bort.  Filter kan tillämpas på händelseprenumerationer antingen under den [skapa](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) av händelseprenumerationen eller [vid ett senare tillfälle](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Ämne filter i händelsen rutnätet arbete baserat på ”börjar med” och ”slutar med” matchar, så att händelser med ett matchande ämne levereras till prenumeranten. 

Ämnet för Blob storage-händelser använder formatet:

```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Om du vill matcha alla händelser för ett lagringskonto kan du lämna ämne filter tomt.

Om du vill matcha händelser från BLOB som skapats i en behållare som delar ett prefix, använda en `subjectBeginsWith` filtrera som:

```json
/blobServices/default/containers/containerprefix
```

Om du vill matcha händelser från BLOB som skapats i en specifik behållare använder en `subjectBeginsWith` filtrera som:

```json
/blobServices/default/containers/containername/
```

Om du vill matcha händelser från BLOB som skapats i en specifik behållare som delar ett blob-namnprefix använder en `subjectBeginsWith` filtrera som:

```json
/blobServices/default/containers/containername/blobs/blobprefix
```

Om du vill matcha händelser från BLOB som skapats i en specifik behållare som delar ett blob-suffix, använda en `subjectEndsWith` filter som ”.log” eller ”.jpg”

Mer information finns i [händelse rutnätet begrepp](../../event-grid/concepts.md#filters).

## <a name="practices-for-consuming-events"></a>Metoder för förbrukning av händelser
Program som hanterar Blob storage-händelser bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * Flera prenumerationer kan konfigureras för vägen händelser till samma händelsehanterare, är det viktigt inte kan ta över händelser som kommer från en viss källa, men för att kontrollera avsnittet i meddelandet för att se till att det kommer från lagringskontot som du förväntar dig.
> * Kontrollera dessutom att eventType är som du är beredd på att processen och inte förutsätter att alla händelser som visas är de typer som du förväntar dig.
> * När meddelanden kan tas emot i rätt ordning och efter ett tag kan du använda etag-fält för att förstå om din information om objekt är fortfarande aktuell.  Använd fälten sequencer för att förstå ordningen för händelser till ett visst objekt.
> * Använd blobType-fältet för att förstå vilken typ av åtgärder tillåts på blob och vilka klientbiblioteket skriver du ska använda för att få åtkomst till blob. Giltiga värden är antingen `BlockBlob` eller `PageBlob`. 
> * Använda url-fältet med det `CloudBlockBlob` och `CloudAppendBlob` konstruktorer åtkomst till blob.
> * Ignorera fält som du inte förstår.  Den här övningen kommer att skydda dig flexibel till nya funktioner som kan läggas till i framtiden.


## <a name="next-steps"></a>Nästa steg

Mer information om händelsen rutnätet och pröva Blob storage-händelser:

- [Om Event Grid](../../event-grid/overview.md)
- [Vidarebefordra Blob storage händelser till en anpassad webbplats-slutpunkt](storage-blob-event-quickstart.md)
