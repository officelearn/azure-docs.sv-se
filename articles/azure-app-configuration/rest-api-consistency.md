---
title: Azure App konfiguration REST API-konsekvens
description: Referens sidor för att säkerställa konsekvens i real tid med hjälp av Azure App konfigurations REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: db9553c2c9c79a6beb9c66d0cb1a1a60435b2abd
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253345"
---
# <a name="real-time-consistency"></a>Konsekvens i real tid

På grund av typen av vissa distribuerade system är konsekvensen i real tid mellan förfrågningar svårt att verkställa implicit. En lösning är att tillåta protokoll stöd i form av flera tokens. Tokens för synkronisering är valfria.

## <a name="initial-request"></a>Första begäran

Använd valfria begärandehuvuden för begäran och svar för att garantera konsekvens i real tid mellan olika klient instanser och begär Anden `Sync-Token` .

Syntax:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parameter|Beskrivning|
|--|--|
| `<id>` | Token-ID (ogenomskinlig) |
| `<value>` | Token-värde (ogenomskinligt). Tillåter Base64-kodad sträng. |
| `<sn>` | Token Sequence Number (version). Högre innebär en nyare version av samma token. Tillåter bättre samtidighet och cachelagring av klienter. Klienten kan välja att endast använda token senaste version, eftersom token-versioner är inkluderade. Den här parametern krävs inte för begär Anden. |

## <a name="response"></a>Svarsåtgärder

Tjänsten tillhandahåller ett `Sync-Token` huvud med varje svar.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Efterföljande begär Anden

Eventuella efterföljande begär Anden garanteras i real tid i förhållande till den angivna `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Om du utelämnar `Sync-Token` rubriken från begäran är det möjligt för tjänsten att svara med cachelagrade data under en kort tids period (upp till några sekunder) innan den kvittas internt. Det här beteendet kan orsaka inkonsekventa läsningar om ändringarna har inträffat omedelbart före läsning.

## <a name="multiple-sync-tokens"></a>Flera Sync-token

Servern kan svara med flera tokens för en enskild begäran. För att upprätthålla konsekvensen i real tid för nästa begäran måste klienten svara med alla mottagna token för synkronisering. Flera huvud värden måste vara kommaavgränsade.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
