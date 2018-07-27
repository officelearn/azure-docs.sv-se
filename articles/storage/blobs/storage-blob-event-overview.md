---
title: Reagera på Azure Blob storage-händelser | Microsoft Docs
description: Använd Azure Event Grid för att prenumerera på Blob Storage-händelser.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.component: blobs
ms.openlocfilehash: 6f3afa22a50728070c42cd6e2eff0cc148815fbc
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262692"
---
# <a name="reacting-to-blob-storage-events"></a>Reagera på Blob storage-händelser

Azure Storage-händelser tillåta program att reagera på skapandet och borttagningen av blobar med moderna arkitekturer utan server. Detta sker utan behov av komplicerade kod eller dyrt och ineffektiv avsökningen tjänster.  I stället händelser skickas [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), eller till och med dina egna anpassade http-lyssnare och du bara betala för det du använder. 

Vanliga händelse scenarier för Blob storage är bild eller video bearbetning, sökindexering eller något arbetsflöde för filen indatavärdena.  Asynkrona filöverföringar är passade bra för händelser.  När ändringarna är ovanliga, men din situation kräver omedelbar svarstider, kan händelsebaserad arkitektur vara särskilt effektivt.

Lagringshändelsernas tillgänglighet är kopplad till Event Grid-tjänstens [tillgänglighet](../../event-grid/overview.md) och blir tillgängliga i andra regioner i takt med att Event Grid blir det. Ta en titt på [dirigera Blob storage-händelser till en anpassad webb-endpoint - CLI](storage-blob-event-quickstart.md) eller [dirigera Blob storage-händelser till en anpassad webb-endpoint - PowerShell](storage-blob-event-quickstart-powershell.md) för ett enkelt exempel. 

![Event Grid-modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Blob Storage-konton
BLOB storage-händelser är tillgängliga i [Blob storage-konton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) och i [lagringskonton för generell användning v2](../common/storage-account-options.md#general-purpose-v2-accounts). **GPv2-konton (General Purpose v2)** är lagringskonton som stöder alla funktionerna för alla lagringstjänster, som blobbar, filer, köer och tabeller. Ett **Blob Storage-konto** är ett specialiserat lagringskonto för lagring av ostrukturerade data som blobbar (objekt) i Azure Storage. Blob Storage-konton liknar allmänna lagringskonton och har samma höga hållbarhet, tillgänglighet, skalbarhet och prestanda som du använder idag, inklusive 100 % API-konsekvens för blockblobbar och tilläggsblobbar. För program som bara behöver lagring av block- eller tilläggsblobbar, rekommenderar vi att du använder Blob-lagringskonton. 

## <a name="available-blob-storage-events"></a>Tillgängliga Blob storage-händelser
Händelserutnät använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) händelsen skicka meddelanden till prenumeranter.  Händelseprenumerationer för BLOB storage kan innehålla två typer av händelser:  

> |Händelsenamn|Beskrivning|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|När en blob skapas eller ersatts via den `PutBlob`, `PutBlockList`, eller `CopyBlob` åtgärder|
> |`Microsoft.Storage.BlobDeleted`|När en blob tas bort via en `DeleteBlob` åtgärden|

## <a name="event-schema"></a>Händelseschema
BLOB storage-händelser innehåller all information du behöver för att svara på ändringar i dina data.  Du kan identifiera en händelse för Blob-lagring eftersom egenskapen händelsetyp börjar med ”Microsoft.Storage”. Mer information om användningen av egenskaper för Event Grid-händelse dokumenteras i [Event Grid Händelseschema](../../event-grid/event-schema.md).  

> |Egenskap |Typ|Beskrivning|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |ämne|sträng|Fullständig Azure Resource Manager-id för det lagringskonto som genererar händelsen.|
> |Ämne|sträng|Relativ resurssökväg till objektet som omfattas av händelsen, med samma utökade Azure Resource Manager-format som vi använder för att beskriva storage-konton, tjänster och behållare för Azure RBAC.  Det här formatet innehåller ett bevara blobnamn.|
> |eventTime|sträng|Datum/tid som händelsen har genererats i ISO 8601-format|
> |Händelsetyp|sträng|”Microsoft.Storage.BlobCreated” eller ”Microsoft.Storage.BlobDeleted”|
> |Id|sträng|Unik identifierare om den här händelsen|
> |dataVersion|sträng|Dataobjektets schemaversion.|
> |metadataVersion|sträng|Schemaversion för översta egenskaper.|
> |data|objekt|Insamling av data för blob storage-händelse|
> |data.contentType|sträng|Innehållstypen för bloben som ska returneras i Content-Type-rubriken från blob|
> |data.contentLength|nummer|Storleken på blobben som heltal som motsvarar ett antal byte som ska returneras i Content-Length-huvudet från blob.  Skickas med BlobCreated händelse, men inte med BlobDeleted.|
> |data.URL|sträng|URL: en för det objekt som omfattas av händelsen|
> |data.eTag|sträng|Etag för objektet när den här händelsen utlöses.  Inte tillgängligt för händelsen BlobDeleted.|
> |data.api|sträng|Namnet på api-åtgärden som utlöste händelsen. Det här värdet är ”PutBlob”, ”PutBlockList” eller ”CopyBlob” för BlobCreated händelser. Det här värdet är ”DeleteBlob” för BlobDeleted händelser. Dessa värden är samma api-namn som finns i diagnostikloggar för Azure Storage. Se [loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|sträng|En täckande strängvärde som representerar den logiska ordningsföljden händelser för ett visst blobnamn.  Användare kan använda vanlig strängjämförelse för att förstå den relativa sekvensen av två händelser på samma blobnamn.|
> |data.requestId|sträng|Tjänstgenererade begäran-id för storage API-åtgärden. Kan användas för att korrelera till Azure Storage diagnostik loggar med ”rubrik-begäran-id”-fältet i loggarna och returneras initierar API-anrop i rubriken ”x-ms-request-id”. Se [logga Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|sträng|Klient-angivna begäran-id för storage API-åtgärden. Kan användas för att korrelera till Azure Storage med hjälp av ”client-request-id”-fältet i loggarna för diagnostikloggar och kan anges i klientbegäranden med rubriken ”x-ms-client-request-id”. Se [logga Format](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
> |data.storageDiagnostics|objekt|Diagnostikdata som ibland inkluderas med Azure Storage-tjänsten. När det finns, ska ignoreras av händelsekonsumenter.|
|data.blobType|sträng|Typ av blob. Giltiga värden är ”BlockBlob” eller ”PageBlob”.| 

Här är ett exempel på en händelse som BlobCreated:
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

Mer information finns i [schema för Blob storage-händelser](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtrera händelser
BLOB-händelseprenumerationer kan filtreras baserat på vilken typ av händelse och av namn på behållare och blobnamnet på det objekt som har skapats eller tagits bort.  Filter kan tillämpas på händelseprenumerationer antingen under den [skapa](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) av händelseprenumerationen eller [vid ett senare tillfälle](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Ämne filter i Event Grid arbete baserat på ”börjar med” och ”slutar med” matchningar, så att händelser med ett matchande ämne levereras till prenumeranten. 

Ämnet för Blob storage-händelser använder formatet:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Om du vill matcha alla händelser för ett lagringskonto kan du lämna ämne filtren tomt.

Om du vill matcha händelser från blobbar som skapats i en uppsättning behållare som delar ett prefix, använda en `subjectBeginsWith` filtrera som:

```
/blobServices/default/containers/containerprefix
```

Om du vill matcha händelser från blobbar som skapats i en specifik behållare använder en `subjectBeginsWith` filtrera som:

```
/blobServices/default/containers/containername/
```

Om du vill matcha händelser från blobbar som skapats i en specifik behållare som delar ett prefix för blobbnamn, använda en `subjectBeginsWith` filtrera som:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Om du vill matcha händelser från blobbar som skapats i en specifik behållare som delar ett blob-suffix, använda en `subjectEndsWith` filter som ”.log” eller ”jpg”. Mer information finns i [Event Grid-begrepp](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Metoder för att använda händelser
Program som hanterar Blob storage-händelser bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * När flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare, är det viktigt inte att anta händelser är från en viss källa, utan att kontrollera ämne för meddelandet att se till att det kommer från storage-konto som du förväntade dig.
> * Kontrollera dessutom att händelsetyp är något du är beredd på att processen och förutsätter inte att alla händelser som visas är de typer som du förväntar dig.
> * När meddelanden kan tas emot i fel ordning och efter ett tag, använder du fälten etag för att förstå om din information om objekt är fortfarande aktuell.  Använd dessutom fälten sequencer för att förstå händelseordningen till ett visst objekt.
> * Använd blobType-fältet för att förstå vilken typ av åtgärder tillåts på blobben och vilka klientbiblioteket skriver du ska använda för att få åtkomst till bloben. Giltiga värden är antingen `BlockBlob` eller `PageBlob`. 
> * Använd url-fält med den `CloudBlockBlob` och `CloudAppendBlob` konstruktorer att få åtkomst till bloben.
> * Ignorera fält som du inte förstår. Den här metoden hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och testa Blob storage-händelser:

- [Om Event Grid](../../event-grid/overview.md)
- [Dirigera Blob storage-händelser till en anpassad webbslutpunkt](storage-blob-event-quickstart.md)
