---
title: Azure App konfiguration REST API-konsekvens
description: Referens sidor för att säkerställa konsekvens i real tid med hjälp av Azure App konfigurations REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424542"
---
# <a name="real-time-consistency"></a>Konsekvens i real tid

På grund av typen av vissa distribuerade system är konsekvensen i real tid mellan förfrågningar svårt att verkställa implicit. En lösning är att tillåta protokoll stöd i form av flera **tokens**. Tokens för synkronisering är valfria.

## <a name="initial-request"></a>Första begäran

Använd valfria `Sync-Token` huvuden för begäran/svar för att garantera konsekvens i real tid mellan olika klient instanser och begär Anden.

Syntax:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parameter|Beskrivning|
|--|--|
| `<id>` | Token-ID (ogenomskinlig) |
| `<value>` | Token-värde (ogenomskinligt). Tillåter Base64-kodad sträng |
| `<sn>` | Token Sequence Number (version). Högre innebär nyare version av samma token. Tillåter bättre samtidighet och cachelagring av klienter. Klienten kan välja att endast använda token senaste version, eftersom token-versioner är inkluderade. Krävs inte för begär Anden. |

## <a name="response"></a>Svarsåtgärder

Tjänsten tillhandahåller ett `Sync-Token` huvud med varje svar.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Efterföljande begär Anden

Eventuella efterföljande begär Anden garanteras i **real tid** i förhållande till den angivna `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Om `Sync-Token` rubriken utelämnas från begäran är det möjligt för tjänsten att svara med cachelagrade data under en kort tids period (upp till några sekunder) innan den kvittas internt. Det här beteendet kan orsaka inkonsekventa läsningar om ändringar har inträffat omedelbart före läsning.

## <a name="multiple-sync-tokens"></a>Flera Sync-token

Servern kan svara med flera Sync-tokens för en enskild begäran. För att upprätthålla konsekvensen i **real tid** för nästa begäran måste klienten svara med alla mottagna Sync-tokens. Per RFC måste flera huvud värden vara kommaavgränsade.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
