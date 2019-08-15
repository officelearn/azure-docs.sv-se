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
ms.openlocfilehash: 13eef9beb6c86683c56efc744dc42b4614b84fe9
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946485"
---
# <a name="reacting-to-blob-storage-events"></a>Reagerar på Blob Storage-händelser

Azure Storage händelser gör att program kan reagera på händelser, t. ex. skapande och borttagning av blobar, genom att använda moderna serverbaserade arkitekturer. Det gör det utan att det behövs komplicerade kod eller dyra och ineffektiva avsöknings tjänster.

I stället flyttas händelser via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som Azure Functions, Azure Logic Apps eller till och med din egen anpassade http-lyssnare och du betalar bara för det du använder.

Blob Storage-händelser skickas på ett tillförlitligt sätt till tjänsten Event Grid som tillhandahåller pålitliga leverans tjänster till dina program genom omfattande principer för återförsök och leverans av obeställbara meddelanden.

Vanliga händelse scenarier för blob-lagring omfattar bild-eller video bearbetning, Sök indexering eller valfritt filorienterade arbets flöde. Asynkrona fil överföringar är en bra plats för händelser. När ändringar är ovanliga, men scenariot kräver omedelbar svars tid, kan den händelsebaserade arkitekturen vara särskilt effektiv.

Om du vill testa detta nu kan du läsa följande artiklar om snabb start:

|Om du vill använda det här verktyget:    |Se den här artikeln: |
|--|-|
|Azure Portal    |[Snabbstart: Dirigera Blob Storage-händelser till webb slut punkt med Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Snabbstart: Dirigera lagrings händelser till webb slut punkt med PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Snabbstart: Dirigera lagrings händelser till webb slut punkter med Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

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

BLOB Event-prenumerationer kan filtreras baserat på händelse typ och efter behållar namn och blob-namn för objektet som har skapats eller tagits bort.  Filter kan tillämpas på händelse prenumerationer antingen under [skapandet](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) av händelse prenumerationen eller [vid ett senare tillfälle](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Ämnes filter i Event Grid arbete baserat på "börjar med" och "slutar med"-matchningar, så att händelser med ett matchande ämne levereras till prenumeranten.

Mer information om hur du använder filter finns i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Ämnet för Blob Storage-händelser använder formatet:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Om du vill matcha alla händelser för ett lagrings konto kan du lämna ämnes filtren tomma.

Om du vill matcha händelser från blobbar som skapats i en uppsättning behållare som delar ett prefix använder `subjectBeginsWith` du ett filter som:

```
/blobServices/default/containers/containerprefix
```

Om du vill matcha händelser från blobbar som skapats i en speciell behållare `subjectBeginsWith` använder du ett filter som:

```
/blobServices/default/containers/containername/
```

Om du vill matcha händelser från blobbar som skapats i en speciell behållare som delar ett BLOB- `subjectBeginsWith` namn använder du ett filter som:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Använd ett `subjectEndsWith` filter som ". log" eller ". jpg" om du vill matcha händelser från blobbar som skapats i en speciell behållare som delar ett BLOB-suffix. Mer information finns i [Event Grid begrepp](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Metoder för att konsumera händelser

Program som hanterar Blob Storage-händelser bör följa några rekommenderade metoder:
> [!div class="checklist"]
> * Eftersom flera prenumerationer kan konfigureras för att dirigera händelser till samma händelse hanterare, är det viktigt att inte anta att händelser kommer från en viss källa, men för att kontrol lera ämnet i meddelandet för att säkerställa att det kommer från det lagrings konto som du förväntar dig.
> * På samma sätt kan du kontrol lera att eventType är att du är för beredd att bearbeta och inte förutsätter att alla händelser som du tar emot är de typer som du förväntar dig.
> * När meddelanden kan komma in i rätt ordning och efter en viss fördröjning använder du etag-fälten för att förstå om din information om objekt fortfarande är uppdaterad.  Använd också sekvenserare-fälten för att förstå ordningen på händelser för ett visst objekt.
> * Använd fältet blobType för att förstå vilken typ av åtgärder som tillåts i blobben och vilka klient biblioteks typer som du ska använda för att få åtkomst till bloben. Giltiga värden är antingen `BlockBlob` eller `PageBlob`. 
> * Använd fältet URL med `CloudBlockBlob` -och `CloudAppendBlob` -konstruktörer för att komma åt blobben.
> * Ignorera fält som du inte förstår. Den här övningen hjälper dig att hålla dig flexibel till nya funktioner som kan läggas till i framtiden.
> * Om du vill se till att händelsen **Microsoft. Storage. BlobCreated** endast utlöses när en block-BLOB är fullständigt allokerad, filtrerar du händelsen för `CopyBlob`- `PutBlob`, `PutBlockList` - `FlushWithClose` eller REST API-anrop. Dessa API-anrop utlöser händelsen **Microsoft. Storage. BlobCreated** endast efter att data har allokerats till en Block-Blob fullständigt. Information om hur du skapar ett filter finns i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och ge Blob Storage-händelser ett försök:

- [Om Event Grid](../../event-grid/overview.md)
- [Dirigera Blob Storage-händelser till en anpassad webb slut punkt](storage-blob-event-quickstart.md)
