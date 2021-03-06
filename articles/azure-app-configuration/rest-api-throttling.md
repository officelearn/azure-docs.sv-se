---
title: Azure App konfiguration REST API-begränsning
description: Referens sidor för att förstå begränsning när du använder Azure App konfigurations REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 26464138b7739bb476d9d0dd1078194473ecd23e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424478"
---
# <a name="throttling"></a>Begränsning

Konfigurations lager har gränser för de begär Anden som de kan hantera. Alla begär Anden som överskrider en tilldelade kvot för ett konfigurations lager får ett HTTP 429-svar (för många begär Anden).

Begränsningen är indelad i olika kvot principer:

- **Totalt antal förfrågningar** – totalt antal begär Anden
- **Total bandbredd** – utgående data i byte
- **Lagring** -total lagrings storlek för användar data i byte

## <a name="handling-throttled-responses"></a>Hantering av begränsade svar

När frekvens gränsen för en specifik kvot har nåtts svarar servern på ytterligare förfrågningar av den typen med en status kod för _429_ . _429_ -svaret innehåller ett _återförsök-efter-MS-_ huvud som ger klienten en föreslagen vänte tid (i millisekunder) för att tillåta begär ande kvot att återfyllas.

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

I exemplet ovan har klienten överskridit sin tillåtna kvot och vi rekommenderar att du saktar ned och väntar 10 millisekunder innan du försöker utföra ytterligare förfrågningar. Klienterna bör även överväga progressiv backoff.

## <a name="other-retry"></a>Annat försök

Tjänsten kan identifiera andra situationer än begränsningen som kräver ett klient försök (t. ex. 503 tjänsten är inte tillgänglig). I alla sådana fall `retry-after-ms` kommer svars huvudet att tillhandahållas. För att öka stabiliteten bör klienten följa det föreslagna intervallet och utföra ett nytt försök.

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
