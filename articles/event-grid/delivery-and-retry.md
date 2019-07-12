---
title: Azure Event Grid leverans och försök igen
description: Beskriver hur Azure Event Grid ger händelser och hur den hanterar inte har meddelanden.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 0945b06f78ac34500f0b16a4a419cff12d1a4734
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812911"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid meddelandeleverans och försök igen

Den här artikeln beskriver hur Azure Event Grid hanterar händelser när leveransen inte är bekräftas.

Event Grid förser varaktiga. Du får varje meddelande minst en gång för varje prenumeration. Händelser skickas direkt till den registrerade slutpunkten för varje prenumeration. Om en slutpunkt inte bekräfta mottagandet av en händelse, försöker Event Grid leverans av händelsen.

För närvarande skickar Event Grid varje händelse individuellt till prenumeranter. Prenumeranten tar emot en matris med en enda händelse.

## <a name="retry-schedule-and-duration"></a>Försök testschemat

Event Grid väntar 30 sekunder för ett svar efter att leverera meddelandet. Efter 30 sekunder om slutpunkten inte har svarat meddelandet är i kö för återförsök. Event Grid använder en exponentiell backoff återförsöksprincipen för händelseleverans. Event Grid försöker leverans enligt följande schema efter bästa förmåga:

- 10 sekunder
- 30 sekunder
- 1 minut
- 5 minuter
- 10 minuter
- 30 minuter
- 1 timme
- Per timme för upp till 24 timmar

Om slutpunkten som svarar inom 3 minuter, Event Grid försöker att ta bort händelsen från kön försök igen efter bästa förmåga men kan fortfarande att ta emot dubbletter.

Event Grid lägger till en liten slumpmässig till alla åtgärder för återförsök och kan autentiseringsuuppsättningarna hoppa över vissa återförsök om en slutpunkt är konsekvent skadade, ned under en lång eller verkar vara överbelastas.

För deterministisk beteende, ställer du in event time to live och max leverans försöker i den [prenumeration återförsöksprinciper](manage-event-delivery.md).

Som standard Event Grid upphör att gälla alla händelser som inte levereras inom 24 timmar. Du kan [anpassa återförsöksprincipen](manage-event-delivery.md) när du skapar en händelseprenumeration. Ange det maximala antalet leveransförsök (standardvärdet är 30) och händelsen time to live (standardvärdet är 1 440 minuter).

## <a name="delayed-delivery"></a>Fördröjd leverans

Som en slutpunkt får levereras kan börjar Event Grid att fördröja leverans och nytt försök till händelser till denna slutpunkt. Till exempel om de första tio händelser som publicerats till en slutpunkt misslyckas kan Event Grid kommer förutsätter att slutpunkten har problem med att och fördröjs alla efterföljande försök *och nya* leveranser under en tid – i vissa fall upp till flera timmar .

Funktionella syftet med fördröjd leverans är att skydda feltillstånd slutpunkter samt Event Grid-system. Utan backoff och fördröjning på leverans till feltillstånd slutpunkter återförsöksprincipen för Event Grid och funktioner för volymen kan lätt bli överväldigande ett system.

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

Event Grid tar hänsyn till **endast** följande HTTP-svarskoder som lyckade leveranser. Alla andra koder anses misslyckade leveranser och kommer att göras eller deadlettered efter behov. När tas emot statuskoden lyckas, anser Event Grid leverans som är klar.

- 200 OK
- 201 Skapad
- 202-accepterad
- 203 icke-auktoritär Information
- 204 inget innehåll

### <a name="failure-codes"></a>Felkoder

Alla andra koder inte i uppsättningen ovan (200 204) betraktas fel och kommer att göras. Vissa har specifika återförsöksprinciper som är knutna till dem som beskrivs nedan, alla andra följer standard exponentiell backoff-modellen. Det är viktigt att tänka på att funktionssätt för återförsök på grund av den parallelliserad natur Event Grid-arkitekturen, är icke-deterministisk. 

| Statuskod | Funktionssätt för återförsök |
| ------------|----------------|
| 400 Felaktig förfrågan | Försök igen efter 5 minuter eller mer (obeställbara omedelbart om obeställbara installationen) |
| 401 Ej behörig | Försök igen efter 5 minuter eller mer |
| 403 Åtkomst nekas | Försök igen efter 5 minuter eller mer |
| 404 Hittades inte | Försök igen efter 5 minuter eller mer |
| 408 Timeout för begäran | Försök igen efter 2 minuter eller mer |
| 413 begäran entiteten är för stor | Försök igen efter 10 sekunder eller mer (obeställbara omedelbart om obeställbara installationen) |
| 503 Tjänsten är inte tillgänglig | Försök igen efter 30 sekunder eller mer |
| Alla andra | Försök igen efter 10 sekunder eller mer |


## <a name="next-steps"></a>Nästa steg

* Om du vill visa status för händelsen leveranser, se [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Om du vill anpassa Leveransalternativ för händelsen, se [död enhetsbokstaven och återförsöksprinciper](manage-event-delivery.md).
