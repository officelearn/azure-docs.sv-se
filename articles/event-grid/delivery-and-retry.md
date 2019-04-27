---
title: Azure Event Grid leverans och försök igen
description: Beskriver hur Azure Event Grid ger händelser och hur den hanterar inte har meddelanden.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: 6dfa84eff8dcc104ae6f9c16262f3b1c697df6c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562006"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid meddelandeleverans och försök igen

Den här artikeln beskriver hur Azure Event Grid hanterar händelser när leveransen inte är bekräftas.

Event Grid förser varaktiga. Du får varje meddelande minst en gång för varje prenumeration. Händelser skickas direkt till den registrerade slutpunkten för varje prenumeration. Om en slutpunkt inte bekräfta mottagandet av en händelse, försöker Event Grid leverans av händelsen.

För närvarande skickar Event Grid varje händelse individuellt till prenumeranter. Prenumeranten tar emot en matris med en enda händelse.

## <a name="retry-schedule-and-duration"></a>Försök testschemat

Event Grid använder en exponentiell backoff återförsöksprincipen för händelseleverans. Om en slutpunkt som inte svarar eller returnerar en felkod, försöker Event Grid leverans enligt följande schema efter bästa förmåga:

1. 10 sekunder
1. 30 sekunder
1. 1 minut
1. 5 minuter
1. 10 minuter
1. 30 minuter
1. 1 timme
1. Per timme för upp till 24 timmar

Event Grid lägger till en liten slumpmässig till alla åtgärder för återförsök och kan autentiseringsuuppsättningarna hoppa över vissa återförsök om en slutpunkt är konsekvent skadade, ned under en lång eller verkar vara överbelastas.

För deterministisk beteende, ställer du in event time to live och max leverans försöker i den [prenumeration återförsöksprinciper](manage-event-delivery.md).

Som standard Event Grid upphör att gälla alla händelser som inte levereras inom 24 timmar. Du kan [anpassa återförsöksprincipen](manage-event-delivery.md) när du skapar en händelseprenumeration. Ange det maximala antalet leveransförsök (standardvärdet är 30) och händelsen time to live (standardvärdet är 1 440 minuter).

## <a name="dead-letter-events"></a>Förlorade händelser

När Event Grid inte kan skicka en händelse, kan den skicka händelsen inte har levererats till ett lagringskonto. Den här processen kallas dead-lettering. Som standard Aktivera inte Event Grid dead-lettering. Du måste ange ett lagringskonto för att lagra felande händelser när du skapar händelseprenumerationen för att aktivera den. Du hämtar händelser från det här lagringskontot för att lösa leveranser.

Event Grid skickar en händelse till förlorade plats när det har försökt alla dess nya försök. Om Event Grid tar emot en 400 (felaktig begäran) eller 413 (begär enhet är stor) svarskod, skickas händelsen direkt till slutpunkten för obeställbara meddelanden. Dessa svarskoder visar leverans av händelsen kommer aldrig att lyckas.

Det finns en fördröjning på fem minuter mellan det senaste försöket att leverera en händelse och när de skickas till platsen för obeställbara meddelanden. Den här fördröjningen är avsedd att minska de antal åtgärderna som Blob storage. Om den förlorade platsen är inte tillgängligt i fyra timmar, har händelsen släppts.

Innan du anger platsen för förlorade måste du ha ett lagringskonto med en behållare. Du kan ange slutpunkten för den här behållaren när du skapar händelseprenumerationen. Slutpunkten är i formatet: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Du kanske vill meddelas när en händelse har skickats till platsen för obeställbara meddelanden. Du använder Event Grid för att svara på händelser som inte har [skapa en händelseprenumeration](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) för förlorade blob-lagringen. Varje gång förlorade blobblagringen tar emot en felande händelse Event Grid meddelar din hanterare. Hanteraren svarar med åtgärder som du vill dra för att stämma av händelser som inte har levererats.

Ett exempel på hur du konfigurerar en plats för obeställbara meddelanden, finns i [död enhetsbokstaven och återförsöksprinciper](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Leveransstatus för meddelande

Event Grid använder HTTP-svarskoder för att bekräfta mottagandet av händelser. 

### <a name="success-codes"></a>Lyckade koder

Följande HTTP-svarskoder indikerar att en händelse har har levererats till din webhook. Event Grid anser att leveransen är klar.

- 200 OK
- 202-accepterad

### <a name="failure-codes"></a>Felkoder

Följande HTTP-svarskoder tyda på att en händelse leveransförsök misslyckades.

- 400 Felaktig förfrågan
- 401 Ej behörig
- 404 Hittades inte
- 408 timeout för begäran
- 413 begäran entiteten är för stor
- 414 URI för lång
- 429 för många begäranden
- 500 Internt serverfel
- 503 Tjänsten är inte tillgänglig
- 504 Gateway-timeout

## <a name="next-steps"></a>Nästa steg

* Om du vill visa status för händelsen leveranser, se [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Om du vill anpassa Leveransalternativ för händelsen, se [död enhetsbokstaven och återförsöksprinciper](manage-event-delivery.md).