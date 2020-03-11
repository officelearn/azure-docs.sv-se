---
title: Reagerar på Azure Blob Storage-händelser | Microsoft Docs
description: Använd Azure Event Grid för att prenumerera på Blob Storage-händelser.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082616"
---
# <a name="reacting-to-blob-storage-events"></a>Reagerar på Blob Storage-händelser

Azure Storage händelser gör att program kan reagera på händelser, t. ex. skapande och borttagning av blobbar. Det gör det utan att det behövs komplicerade kod eller dyra och ineffektiva avsöknings tjänster.

Händelser skickas med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som Azure Functions, Azure Logic Apps eller till och med din egen http-lyssnare. Den bästa delen är att du bara betalar för det du använder.

Blob Storage skickar händelser till Event Grid som tillhandahåller tillförlitlig händelse leverans till dina program via omfattande principer för återförsök och obeställbara meddelanden.

Vanliga händelse scenarier för blob-lagring omfattar bild-eller video bearbetning, Sök indexering eller valfritt filorienterade arbets flöde. Asynkrona fil överföringar är en bra plats för händelser. När ändringar är ovanliga, men scenariot kräver omedelbar svars tid, kan den händelsebaserade arkitekturen vara särskilt effektiv.

Om du vill testa detta nu kan du läsa följande artiklar om snabb start:

|Om du vill använda det här verktyget:    |Se den här artikeln: |
|--|-|
|Azure Portal    |[Snabb start: dirigera Blob Storage-händelser till webb slut punkt med Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Snabb start: dirigera lagrings händelser till webb slut punkt med PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Snabb start: dirigera lagrings händelser till webb slut punkt med Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Om du vill visa djupgående exempel för att agera på Blob Storage-händelser med hjälp av Azure Functions, se följande artiklar:

- [Självstudie: använd Azure Data Lake Storage Gen2 händelser om du vill uppdatera en Databricks delta tabell](data-lake-storage-events.md).
- [Självstudie: automatisera storleks ändring av överförda bilder med hjälp av Event Grid](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Endast lagrings konton av typen **StorageV2 (generell användning v2)** och **BlobStorage** -stöd för händelse integrering. **Lagring (Genral Purpose v1)** stöder *inte* integrering med event Grid.

## <a name="the-event-model"></a>Händelse modellen

Event Grid använder [händelse prenumerationer](../../event-grid/concepts.md#event-subscriptions) för att dirigera händelse meddelanden till prenumeranter. Den här bilden illustrerar relationen mellan händelse utgivare, händelse prenumerationer och händelse hanterare.

![Event Grid modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Börja med att prenumerera på en slut punkt till en händelse. När en händelse utlöses skickar Event Grid-tjänsten data om händelsen till slut punkten.

Se artikeln [Blob Storage Events schema](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa:

> [!div class="checklist"]
> * En fullständig lista över Blob Storage-händelser och hur varje händelse utlöses.
> * Ett exempel på de data som Event Grid skulle skicka för var och en av dessa händelser.
> * Syftet med varje nyckel värdes par som visas i data.

## <a name="filtering-events"></a>Filtrera händelser

BLOB- [händelser kan filtreras](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) efter händelse typ, container namn eller namn på det objekt som skapades/togs bort. Filter i Event Grid matcha början eller slutet av ämnet så att händelser med ett matchande ämne går till prenumeranten.

Mer information om hur du använder filter finns i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Ämnet för Blob Storage-händelser använder formatet:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Om du vill matcha alla händelser för ett lagrings konto kan du lämna ämnes filtren tomma.

Om du vill matcha händelser från blobbar som skapats i en uppsättning behållare som delar ett prefix använder du ett `subjectBeginsWith`-filter som:

```
/blobServices/default/containers/containerprefix
```

Om du vill matcha händelser från blobbar som skapats i en speciell behållare använder du ett `subjectBeginsWith`-filter som:

```
/blobServices/default/containers/containername/
```

Om du vill matcha händelser från blobbar som skapats i en speciell behållare som delar ett BLOB Name-prefix använder du ett `subjectBeginsWith`-filter som:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Använd ett `subjectEndsWith` filter som ". log" eller ". jpg" om du vill matcha händelser från blobbar som skapats i en speciell behållare som delar ett BLOB-suffix. Mer information finns i [Event Grid begrepp](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Blob Storage-händelser bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, är det viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att säkerställa att det kommer från det lagrings konto som du förväntar dig.
> * På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
> * När meddelanden kan komma efter en viss fördröjning använder du etag-fälten för att ta reda på om din information om objekt fortfarande är uppdaterad. Information om hur du använder etag-fältet finns i [Hantera samtidighet i Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * När meddelanden kan komma in i turordning använder du fälten sekvenser för att förstå händelse ordningen för ett visst objekt. Fältet Sequencer är ett sträng värde som representerar den logiska sekvensen av händelser för ett visst BLOB-namn. Du kan använda standard sträng jämförelse för att förstå den relativa sekvensen av två händelser på samma BLOB-namn.
> * Använd fältet blobType för att förstå vilken typ av åtgärder som tillåts i blobben och vilka klient biblioteks typer som du ska använda för att få åtkomst till bloben. Giltiga värden är antingen `BlockBlob` eller `PageBlob`. 
> * Använd URL-fältet med `CloudBlockBlob` och `CloudAppendBlob` konstruktörer för att komma åt blobben.
> * Ignorera fält som du inte förstår. Den här övningen hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
> * Om du vill se till att händelsen **Microsoft. Storage. BlobCreated** endast utlöses när en block-BLOB är fullständigt allokerad filtrerar du händelsen för `CopyBlob`, `PutBlob``PutBlockList` eller `FlushWithClose` REST API-anrop. Dessa API-anrop utlöser händelsen **Microsoft. Storage. BlobCreated** endast efter att data har allokerats till en Block-Blob fullständigt. Information om hur du skapar ett filter finns i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Blob Storage-händelser ett försök:

- [Om Event Grid](../../event-grid/overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webb slut punkt](storage-blob-event-quickstart.md)
