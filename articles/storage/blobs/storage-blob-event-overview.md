---
title: Reagera på Azure Blob-lagringshändelser | Microsoft-dokument
description: Använd Azure Event Grid för att prenumerera på Blob Storage-händelser.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268501"
---
# <a name="reacting-to-blob-storage-events"></a>Reagera på Blob Storage-händelser

Azure Storage-händelser tillåter program att reagera på händelser, till exempel skapande och borttagning av blobbar. Det gör den utan behov av komplicerad kod eller dyra och ineffektiva valtjänster.

Händelser trycks med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som Azure Functions, Azure Logic Apps eller till och med till din egen http-lyssnare. Det bästa är att du bara betalar för det du använder.

Blob storage skickar händelser till Event Grid som ger tillförlitlig händelseleverans till dina program genom avancerade principer för återförsök och obeställbara bokstäver.

Vanliga Blob-lagringshändelsescenarier inkluderar bild- eller videobearbetning, sökindexering eller filorienterade arbetsflöden. Asynkrona filuppladdningar är en bra passform för händelser. När ändringar är ovanliga, men ditt scenario kräver omedelbar svarstid, kan händelsebaserad arkitektur vara särskilt effektiv.

Om du vill prova det här nu kan du se någon av dessa snabbstartsartiklar:

|Om du vill använda det här verktyget:    |Se den här artikeln: |
|--|-|
|Azure Portal    |[Snabbstart: Route Blob storage events to web endpoint with the Azure portal Snabbstart: Route Blob storage events to web endpoint with the Azure portal Snabbstart: Route Blob storage events to web endpoint with the Azure portal Snabbstart](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Snabbstart: Dirigera lagringshändelser till webbslutpunkt med PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Snabbstart: Dirigera lagringshändelser till webbslutpunkt med Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Mer information om hur du reagerar på Blob-lagringshändelser med hjälp av Azure-funktioner finns i följande artiklar:

- [Självstudiekurs: Använd Azure Data Lake Storage Gen2-händelser för att uppdatera en Databricks Delta-tabell](data-lake-storage-events.md).
- [Självstudiekurs: Automatisera storleksändring av uppladdade bilder med Event Grid](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Endast lagringskonton av typen **StorageV2 (general purpose v2)** och **BlobStorage** stöder händelseintegrering. **Lagring (genral syfte v1)** stöder *inte* integrering med Event Grid.

## <a name="the-event-model"></a>Händelsemodellen

Event Grid använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) för att dirigera händelsemeddelanden till prenumeranter. Den här bilden illustrerar förhållandet mellan händelseutgivare, händelseprenumerationer och händelsehanterare.

![Modell för händelserutnät](./media/storage-blob-event-overview/event-grid-functional-model.png)

Prenumerera först på en slutpunkt till en händelse. När en händelse utlöses skickar tjänsten Event Grid data om händelsen till slutpunkten.

Se [schemaartikeln för Blob storage events](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa:

> [!div class="checklist"]
> * En fullständig lista över Blob-lagringshändelser och hur varje händelse utlöses.
> * Ett exempel på de data som händelserutnätet skickar för var och en av dessa händelser.
> * Syftet med varje nyckelvärdepar som visas i data.

## <a name="filtering-events"></a>Filtrera händelser

[Blob-händelser kan filtreras efter](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) händelsetyp, behållarnamn eller namn på objektet som skapades/togs bort. Filter i händelserutnät matchar början eller slutet av ämnet så att händelser med ett matchande ämne går till prenumeranten.

Mer information om hur du använder filter finns i [Filtrera händelser för händelserutnät](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Ämnet Blob-lagringshändelser använder formatet:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Om du vill matcha alla händelser för ett lagringskonto kan du lämna ämnesfiltren tomma.

Om du vill matcha händelser från blobbar som skapats `subjectBeginsWith` i en uppsättning behållare som delar ett prefix använder du ett filter som:

```
/blobServices/default/containers/containerprefix
```

Om du vill matcha händelser från blobbar som skapats i en viss behållare använder du ett `subjectBeginsWith` filter som:

```
/blobServices/default/containers/containername/
```

Om du vill matcha händelser från blobbar som skapats `subjectBeginsWith` i en viss behållare som delar ett blobnamnprefix använder du ett filter som:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Om du vill matcha händelser från blobbar som skapats `subjectEndsWith` i en specifik behållare som delar ett blob-suffix använder du ett filter som ".log" eller ".jpg". Mer information finns i [Begrepp för händelserutnät](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Blob-lagringshändelser bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelsehanterare är det viktigt att inte anta att händelser kommer från en viss källa, utan för att kontrollera ämnet för meddelandet för att säkerställa att det kommer från det lagringskonto du förväntar dig.
> * På samma sätt kontrollerar du att eventType är en som du är beredd att bearbeta och förutsätter inte att alla händelser du får kommer att vara de typer du förväntar dig.
> * När meddelanden kan komma efter en viss fördröjning kan du använda etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad. Mer information om hur du använder etag-fältet finns [i Hantera samtidighet i Blob-lagring](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * När meddelanden kan komma i oordning använder du sequencerfälten för att förstå ordningen på händelser på ett visst objekt. Sequencer-fältet är ett strängvärde som representerar den logiska händelsesekvensen för ett visst blobnamn. Du kan använda standardsträngjämförelse för att förstå den relativa sekvensen av två händelser på samma blobnamn.
> * Använd fältet blobType för att förstå vilken typ av åtgärder som tillåts på blobben och vilka klientbibliotekstyper som du bör använda för att komma åt blobben. Giltiga värden `BlockBlob` är `PageBlob`antingen eller . 
> * Använd url-fältet `CloudBlockBlob` med `CloudAppendBlob` och konstruktorer för att komma åt blobben.
> * Ignorera fält som du inte förstår. Den här metoden hjälper dig att hålla dig motståndskraftig mot nya funktioner som kan läggas till i framtiden.
> * Om du vill vara säker på att händelsen **Microsoft.Storage.BlobCreated** endast utlöses när en `CopyBlob`Block `PutBlob` `PutBlockList` Blob är helt genomförd filtrerar du händelsen för , eller `FlushWithClose` REST API-anrop. Dessa API-anrop utlöser händelsen **Microsoft.Storage.BlobSkaperad** först när data har gjorts helt åtada till en Block Blob. Mer information om hur du skapar ett filter finns i [Filtrera händelser för händelserutnät](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Blob-lagringshändelser ett försök:

- [Om Event Grid](../../event-grid/overview.md)
- [Dirigera Blob-lagringshändelser till en anpassad webbslutpunkt](storage-blob-event-quickstart.md)
