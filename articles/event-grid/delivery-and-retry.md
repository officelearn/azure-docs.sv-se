---
title: Azure händelse rutnätet leverans och försök igen
description: Beskriver hur Azure händelse rutnätet ger händelser och hur den hanterar felande meddelanden.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: tomfitz
ms.openlocfilehash: 83852917909d13555e7a0a339d2ecc805eeead42
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625805"
---
# <a name="event-grid-message-delivery-and-retry"></a>Händelsen rutnätet meddelandeleverans och försök igen 

Den här artikeln beskrivs hur Azure händelse rutnätet hanterar händelser när leverans inte bekräftas.

Händelsen rutnätet innehåller varaktiga leverans. Den ger varje meddelande minst en gång för varje prenumeration. Händelser skickas direkt till registrerade webhooken för varje prenumeration. Om en webhook inte bekräfta mottagandet av en händelse inom 60 sekunder från det första försöket leverans, försöker händelse rutnätet leverans av händelsen. 

För närvarande skickar händelse rutnätet varje händelse individuellt till prenumeranter. Prenumeranten tar emot en matris med en enskild händelse.

## <a name="message-delivery-status"></a>Status för leverans av meddelande

Händelsen rutnätet använder HTTP-svarskoder för att bekräfta mottagandet av händelser. 

### <a name="success-codes"></a>Lyckade koder

Följande koder för HTTP-svaret anger att en händelse har har levererats till din webhooken. Händelsen rutnätet anser leverans slutförd.

- 200 OK
- 202 godkända

### <a name="failure-codes"></a>Felkoder

Följande HTTP-svarskoder indikera att en händelse leverans försök misslyckades. 

- 400 Felaktig förfrågan
- 401 obehörig
- 404 Hittades inte
- 408 begäran-timeout
- 414 URI för lång
- 500 Internt serverfel
- 503 Tjänsten är inte tillgänglig
- 504 Gateway-timeout

Om händelsen rutnät får ett felmeddelande som anger att den är inte tillgänglig, försöker den igen skicka händelsen. 

## <a name="retry-intervals-and-duration"></a>Återförsöksintervall och varaktighet

Händelsen rutnätet använder en exponentiell backoff i principen för leverans av händelsen. Om din webhook svarar inte eller returnerar en felkod, försöker händelse rutnätet leverans enligt följande schema:

1. 10 sekunder
2. 30 sekunder
3. 1 minut
4. 5 minuter
5. 10 minuter
6. 30 minuter
7. 1 timme

Händelsen rutnätet lägger till en liten slumpmässig alla återförsöksintervall. Efter en timme försöks händelse leverans en gång i timmen.

Händelsen rutnätet slutar att leverera alla händelser som inte levereras inom 24 timmar.

## <a name="next-steps"></a>Nästa steg

* Om du vill visa status för händelse leveranser [övervakaren händelse rutnätet meddelandeleverans](monitor-event-delivery.md).
* En introduktion till Event Grid finns i [Om Event Grid](overview.md).
* Om du vill komma igång snabbt med hjälp av händelse rutnätet kan se [skapa och flöde anpassade händelser med Azure händelse rutnätet](custom-event-quickstart.md).