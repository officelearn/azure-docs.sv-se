---
title: Reagera på Azure Blob storage-händelser | Microsoft Docs
description: Använd Azure Event Grid för att prenumerera på Blob Storage-händelser.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444686"
---
# <a name="reacting-to-blob-storage-events"></a>Reagera på Blob storage-händelser

Azure Storage-händelser tillåta program att reagera på händelser, t.ex skapandet och borttagningen av blobbar, med hjälp av moderna arkitekturer utan server. Detta sker utan behov av komplicerade kod eller dyrt och ineffektiv avsökningen tjänster.

I stället händelser skickas [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) till prenumeranter som Azure Functions, Azure Logic Apps, eller till och med dina egna anpassade http-lyssnare och du betalar bara för det du använder.

BLOB storage-händelser skickas på ett tillförlitligt sätt till Event Grid-tjänsten som tillhandahåller pålitlig leverans till dina program via omfattande återförsöksprinciper och förlorade leverans.

Vanliga händelse scenarier för Blob storage är bild eller video bearbetning, sökindexering eller något arbetsflöde för filen indatavärdena. Asynkrona filöverföringar är passade bra för händelser. När ändringarna är ovanliga, men din situation kräver omedelbar svarstider, kan händelsebaserad arkitektur vara särskilt effektivt.

Om du vill testa detta nu se några av de här artiklarna i snabbstarten:

|Om du vill använda det här verktyget:    |Se den här artikeln: |
|--|-|
|Azure Portal    |[Snabbstart: Dirigera Blob storage-händelser till webbslutpunkt med Azure portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Snabbstart: Dirigera storage-händelser till webbslutpunkt med PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Snabbstart: Dirigera storage-händelser till webbslutpunkt med Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>Händelsemodellen

Event Grid använder [händelseprenumerationer](../../event-grid/concepts.md#event-subscriptions) händelsen skicka meddelanden till prenumeranter. Den här bilden illustrerar förhållandet mellan händelseutgivare och händelseprenumerationer händelsehanterare.

![Event Grid-modell](./media/storage-blob-event-overview/event-grid-functional-model.png)

Först prenumerera på en slutpunkt på en händelse. Sedan, när en händelse har utlösts Event Grid-tjänsten skickar data om händelsen till slutpunkten.

Se den [schema för Blob storage-händelser](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) artikeln om du vill visa:

> [!div class="checklist"]
> * En fullständig lista över Blob storage-händelser och hur varje händelsen utlöses.
> * Ett exempel på data i Event Grid skickar för var och en av dessa händelser.
> * Syftet med varje nyckel-värde-par som finns i informationen.

## <a name="filtering-events"></a>Filtrera händelser

BLOB-händelseprenumerationer kan filtreras baserat på vilken typ av händelse och av namn på behållare och blobnamnet på det objekt som har skapats eller tagits bort.  Filter kan tillämpas på händelseprenumerationer antingen under den [skapa](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) av händelseprenumerationen eller [vid ett senare tillfälle](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Ämne filter i Event Grid arbete baserat på ”börjar med” och ”slutar med” matchningar, så att händelser med ett matchande ämne levereras till prenumeranten.

Läs mer om hur du använder filter i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

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
> * Om du vill se till att den **Microsoft.Storage.BlobCreated** händelsen utlöses endast när en Blockblob är helt allokerade, filtrera händelsen för den `CopyBlob`, `PutBlob`, `PutBlockList` eller `FlushWithClose` REST API-anrop. Dessa utlösare för API-anrop i **Microsoft.Storage.BlobCreated** händelse efter att data är helt allokerade till en Blockblob. Läs hur du skapar ett filter i [Filtrera händelser för Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Nästa steg

Läs mer om Event Grid och testa Blob storage-händelser:

- [Om Event Grid](../../event-grid/overview.md)
- [Dirigera Blob storage-händelser till en anpassad webbslutpunkt](storage-blob-event-quickstart.md)
