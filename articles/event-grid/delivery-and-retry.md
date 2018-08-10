---
title: Azure Event Grid leverans och försök igen
description: Beskriver hur Azure Event Grid ger händelser och hur den hanterar inte har meddelanden.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: b34386a7b416d6f7d8b008a9cb5ef142948a370f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005403"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid meddelandeleverans och försök igen 

Den här artikeln beskriver hur Azure Event Grid hanterar händelser när leveransen inte är bekräftas.

Event Grid förser varaktiga. Du får varje meddelande minst en gång för varje prenumeration. Händelser skickas direkt till registrerade webhooken till varje prenumeration. Om en webhook inte bekräfta mottagandet av en händelse inom 60 sekunder det första försökets leverans, försöker Event Grid leverans av händelsen. 

För närvarande skickar Event Grid varje händelse individuellt till prenumeranter. Prenumeranten tar emot en matris med en enda händelse.

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
- 414 URI för lång
- 429 för många begäranden
- 500 Internt serverfel
- 503 Tjänsten är inte tillgänglig
- 504 Gateway-timeout

Om Event Grid tar emot ett fel som anger slutpunkten är tillfälligt otillgänglig eller en begäran om framtida kan lyckas, försök igen att skicka händelsen. Om Event Grid tar emot ett fel som anger leveransen aldrig lyckas och [förlorade slutpunkten har konfigurerats](manage-event-delivery.md), skickas händelsen till slutpunkten för obeställbara meddelanden. 

## <a name="retry-intervals-and-duration"></a>Intervall för återförsök och varaktighet

Event Grid använder en exponentiell backoff återförsöksprincipen för händelseleverans. Om din webhook inte svarar eller returnerar en felkod, försöker Event Grid leverans enligt följande schema:

1. 10 sekunder
2. 30 sekunder
3. 1 minut
4. 5 minuter
5. 10 minuter
6. 30 minuter
7. 1 timme

Event Grid lägger till en liten slumpmässig i alla återförsöksinterval. Efter en timme görs händelseleverans en gång i timmen.

Som standard Event Grid upphör att gälla alla händelser som inte levereras inom 24 timmar. Du kan [anpassa återförsöksprincipen](manage-event-delivery.md) när du skapar en händelseprenumeration. Ange det maximala antalet leveransförsök (standardvärdet är 30) och händelsen time to live (standardvärdet är 1 440 minuter).

## <a name="dead-letter-events"></a>Förlorade händelser

När Event Grid inte kan skicka en händelse, kan den skicka händelsen inte har levererats till ett lagringskonto. Den här processen kallas dead-lettering. Om du vill se händelser som inte har kan du hämta dem från förlorade plats. Mer information finns i [död enhetsbokstaven och återförsöksprinciper](manage-event-delivery.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill visa status för händelsen leveranser, se [övervaka Event Grid meddelandeleverans](monitor-event-delivery.md).
* Om du vill anpassa Leveransalternativ för händelsen, se [hantera Event Grid leveransinställningar](manage-event-delivery.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Kom igång snabbt med Event Grid, se [skapa och dirigera anpassade händelser med Azure Event Grid](custom-event-quickstart.md).