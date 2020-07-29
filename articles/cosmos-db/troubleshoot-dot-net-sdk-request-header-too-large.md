---
title: Felsök begär ande huvudet för stort eller 400 Felaktig begäran i Azure Cosmos DB
description: Så här diagnostiserar och åtgärdar du för stort undantag för begär ande huvudet
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 942af52c43a1d4526a42f20df40abf3f510e0ee6
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294671"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnostisera och Felsök Azure Cosmos DB begär ande rubriken för stort meddelande
Begär ande huvudet för stort meddelande har utlösts av en HTTP-felkod 400. Det här felet uppstår om storleken på begär ande rubriken har blivit för stor och överskrider den maximalt tillåtna storleken. Vi rekommenderar att du använder den senaste versionen av SDK. Se till att du använder minst version 3. x eller 2. x, eftersom dessa versioner lägger till huvud storleks spårning i undantags meddelandet.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Begär ande huvudet för stort meddelande visas om sessionen eller den fortsatta token är för stor. I följande avsnitt beskrivs orsaken och lösningen på problemet i varje kategori.

### <a name="1-session-token-too-large"></a>1. sessionstoken för stor

#### <a name="cause"></a>Orsak:
400 Felaktig begäran orsakas sannolikt av att sessionstoken är för stor. Om följande påståenden är sanna bekräftar den att sessionstoken är för stor.

* Felet uppstår vid punkt åtgärden som att skapa, läsa, uppdatera och osv. där det inte finns någon fortsättnings-token.
* Undantaget startades utan att göra några ändringar i programmet. Sessionstoken ökar när antalet partitioner ökar i behållaren. Antalet partitioner ökar med mängden data som ökar eller om data flödet ökar.

#### <a name="temporary-mitigation"></a>Temporär minskning: 
Starta om klient programmet för att återställa alla sessionstoken. Sessionstoken kommer slutligen att växa tillbaka till den tidigare storlek som orsakade problemet. Använd lösningen i nästa avsnitt för att undvika det här problemet helt.

#### <a name="solution"></a>Lösning:
1. Följ anvisningarna i artikeln prestanda tips för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) eller [.NET v2](performance-tips.md) och konvertera programmet så att det använder direkt anslutnings läge med TCP-protokollet. Direct-läget med TCP-protokollet har inte begränsning för sidhuvuds storlek som HTTP-protokollet, så det undviker det här problemet. Se till att använda den senaste versionen av SDK, som har en korrigering för Query-åtgärder när tjänst-interop inte är tillgängligt.
2. Om direkt anslutnings läge med TCP-protokollet inte är ett alternativ för din arbets belastning kan du undvika det genom att ändra [klientens konsekvens nivå](how-to-manage-consistency.md). Sessionstoken används endast för konsekvens av sessioner, vilket är standard konsekvens nivån för Azure Cosmos DB. Andra konsekvens nivåer använder inte session-token.

### <a name="2-continuation-token-too-large"></a>2. fortsättnings-token är för stor

#### <a name="cause"></a>Orsak:
Den dåliga 400-begäran sker vid frågor som används för att använda en fortsättnings-token. Om den fortsatta token har blivit för stor eller om olika frågor har olika storlekar för att ändra token.
    
#### <a name="solution"></a>Lösning:
1. Följ anvisningarna i artikeln prestanda tips för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) eller [.NET v2](performance-tips.md) och konvertera programmet så att det använder direkt anslutnings läge med TCP-protokollet. Direct-läget med TCP-protokollet har inte begränsning för sidhuvuds storlek som HTTP-protokollet, så det undviker det här problemet. 
3. Om direkt anslutnings läge med TCP-protokollet inte är ett alternativ för din arbets belastning, kan du försöka att ange `ResponseContinuationTokenLimitInKb` alternativet. Du kan hitta det här alternativet i `FeedOptions` for v2 eller `QueryRequestOptions` i v3.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)
