---
title: Felsöka Azure Cosmos DB obehörigt undantag
description: Diagnostisera och åtgärda otillåtna undantag
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 872780ff65dabe5a931ae712c0f99b6e4f3e726d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294517"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exception"></a>Diagnostisera och Felsök Azure Cosmos DB obehörigt undantag

HTTP 401: MAC-signaturen som hittades i HTTP-begäran är inte samma som den beräknade signaturen.
Om du fick följande 401-felmeddelande: MAC-signaturen som hittades i HTTP-begäran är inte samma som den beräknade signaturen. Det kan orsakas av följande scenarier.

Äldre SDK: er kan visas som ett ogiltigt JSON-undantag i stället för rätt 401 obehörig undantag. Nyare SDK: er hanterar det här scenariot korrekt och ger ett giltigt fel meddelande.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för obehörigt undantag.

### <a name="1-key-was-not-properly-rotated-is-the-most-common-scenario"></a>1. nyckeln roterades inte ordentligt i det vanligaste scenariot.
401-felet för MAC-signaturen visas strax efter en nyckelrotation och upphör att gälla utan några ändringar. 

#### <a name="solution"></a>Lösning:
Nyckeln roterades och följer inte [metodtipsen](secure-access-to-data.md#key-rotation). Cosmos DB konto nyckel rotationen kan ta var som helst från några sekunder till några dagar, beroende på Cosmos DB kontots storlek.

### <a name="2-the-key-is-misconfigured"></a>2. nyckeln är felkonfigurerad 
401 MAC-signaturen är konsekvent och inträffar för alla anrop som använder nyckeln

#### <a name="solution"></a>Lösning:
Nyckeln är felkonfigurerad i programmet och använder fel nyckel för kontot eller så har hela nyckeln inte kopierats.

### <a name="3-the-application-is-using-the-read-only-keys-for-write-operations"></a>3. programmet använder skrivskyddade nycklar för Skriv åtgärder
401 MAC-signaturkrav sker bara för Skriv åtgärder som att skapa eller ersätta, men Läs förfrågan lyckades.

#### <a name="solution"></a>Lösning:
Växla programmet till att använda en Läs/skriv-nyckel för att utföra åtgärderna.

### <a name="4-race-condition-with-create-container"></a>4. tävlings villkor med Create Container
401 MAC-signaturkrav visas strax efter att en container har skapats. Detta sker endast förrän behållaren har skapats.

#### <a name="solution"></a>Lösning:
Det finns ett konkurrenstillstånd för containerskapande. En programinstans försöker komma åt containern innan den har skapats. Det vanligaste scenariot för det här tävlings tillståndet är om programmet körs och behållaren tas bort och återskapas med samma namn. SDK:n kommer att försöka använda den nya containern, men containerskapandet pågår fortfarande och har inte nycklarna.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)