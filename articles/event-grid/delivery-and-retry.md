---
title: "Azure händelse rutnätet leverans och försök igen"
description: "Beskriver hur Azure händelse rutnätet ger händelser och hur den hanterar felande meddelanden."
services: event-grid
author: djrosanova
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/11/2017
ms.author: darosa
ms.openlocfilehash: e0f8afdfd84ea3c0c061459c27da285f6ae8957e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-message-delivery-and-retry"></a>Händelsen rutnätet meddelandeleverans och försök igen 

Den här artikeln beskrivs hur Azure händelse rutnätet hanterar händelser när leverans inte bekräftas.

Händelsen rutnätet innehåller varaktiga leverans. Den ger varje meddelande minst en gång för varje prenumeration. Händelser skickas direkt till registrerade webhooken för varje prenumeration. Om en webhook inte bekräfta mottagandet av en händelse inom 60 sekunder från det första försöket leverans, försöker händelse rutnätet leverans av händelsen.

## <a name="message-delivery-status"></a>Status för leverans av meddelande

Händelsen rutnätet använder HTTP-svarskoder för att bekräfta mottagandet av händelser. 

### <a name="success-codes"></a>Lyckade koder

Följande koder för HTTP-svaret anger att en händelse har har levererats till din webhooken. Händelsen rutnätet anser leverans slutförd.

- 200 OK
- 202 godkända

### <a name="failure-codes"></a>Felkoder

Följande HTTP-svarskoder indikera att en händelse leverans försök misslyckades. Händelsen rutnätet försöker igen för att skicka händelsen. 

- 400 Felaktig förfrågan
- 401 obehörig
- 404 Hittades inte
- 408 begäran-timeout
- 414 URI för lång
- 500 Internt serverfel
- 503 inte tillgänglig
- 504 gateway-Timeout

Andra svarskod eller brist på ett svar anger du ett fel. Händelsen rutnätet återförsök leverans. 

## <a name="retry-intervals"></a>Återförsöksintervall

Händelsen rutnätet använder en exponentiell backoff i principen för leverans av händelsen. Om din webhook svarar inte eller returnerar en felkod, försöker händelse rutnätet leverans enligt följande schema:

1. 10 sekunder
2. 30 sekunder
3. 1 minut
4. 5 minuter
5. 10 minuter
6. 30 minuter
7. 1 timme

Händelsen rutnätet lägger till en liten slumpmässig alla återförsöksintervall.

## <a name="retry-duration"></a>Försök varaktighet

Under förhandsgranskning, Azure händelse rutnätet upphör att gälla alla händelser som inte levereras inom två timmar. Innan du allmän tillgänglighet kommer nu att öka till 24 timmar. 

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [om händelsen rutnätet](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).