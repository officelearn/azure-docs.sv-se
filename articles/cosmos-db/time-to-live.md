---
title: Förfaller data i Azure Cosmos DB med Time to Live
description: Med TTL ger Microsoft Azure Cosmos DB möjlighet att automatiskt rensa dokument från systemet efter en viss tids period.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: f66508a4794b8009523cc2820efe0156b4a9e2f6
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756852"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live (TTL) i Azure Cosmos DB 

Med **Time to Live** eller TTL ger Azure Cosmos DB möjlighet att ta bort objekt automatiskt från en behållare efter en viss tids period. Som standard kan du ange Time to Live på behållar nivån och åsidosätta värdet per objekt-basis. När du har ställt in TTL på en behållare eller på en objekt nivå, tar Azure Cosmos DB automatiskt bort objekten efter tids perioden, sedan den tid de senast ändrades. Time to Live-värdet är konfigurerat på några sekunder. När du konfigurerar TTL tar systemet automatiskt bort de utgångna objekten baserat på TTL-värdet, utan att det krävs någon borttagnings åtgärd som uttryckligen utfärdas av klient programmet.

Borttagning av utgångna objekt är en bakgrunds aktivitet som förbrukar [enheter](request-units.md)med överdrivet utrymme, det vill säga enheter som inte har använts av användar förfrågningar. Förfallo datum kan fördröjas om behållaren är hårt belastad och ingen enhet för begäran har kvar för underhålls aktiviteter.

## <a name="time-to-live-for-containers-and-items"></a>Tid till Live för behållare och objekt

TTL-värdet är inställt på några sekunder och tolkas som en delta från den tidpunkt då ett objekt senast ändrades. Du kan ställa in Time to Live på en behållare eller ett objekt i behållaren:

1. **Time to Live på en behållare** (anges med `DefaultTimeToLive`):

   - Om det saknas (eller är inställt på null), kommer objekten inte att förfalla automatiskt.

   - Om det är tillgängligt och värdet har angetts till "-1", är det lika med oändlighet och objekt förfaller inte som standard.

   - Om det är tillgängligt och värdet är inställt på en siffra *"n"* , kommer objekten att förfalla *"n"* sekunder efter deras senaste ändrings tid.

2. **Time to Live på ett objekt** (anges med `ttl`):

   - Den här egenskapen gäller endast om `DefaultTimeToLive` finns och inte har angetts till null för den överordnade behållaren.

   - Om det här alternativet anges åsidosätts `DefaultTimeToLive`-värdet för den överordnade behållaren.

## <a name="time-to-live-configurations"></a>Time to Live konfigurationer

* Om TTL är inställt på *"n"* i en behållare går objekten i den behållaren ut efter *n* sekunder.  Om det finns objekt i samma behållare som har sin egen tid att leva, anger du till-1 (indikerar att de inte går ut) eller om vissa objekt har åsidosatt tiden till Live-inställningen med ett annat antal, förfaller dessa objekt baserat på sitt eget konfigurerade TTL-värde. 

* Om TTL inte är inställt på en behållare, har tiden till Live på ett objekt i den här behållaren ingen påverkan. 

* Om TTL på en behållare är inställt på-1, kommer ett objekt i den här behållaren som har TTL-värdet n att förfalla efter n sekunder, och återstående objekt upphör inte att gälla. 

Borttagning av objekt baserat på TTL är kostnads fritt. Det finns ingen ytterligare kostnad (det vill säga inga ytterligare ru: er förbrukas) när objektet tas bort till följd av förfallo tid för TTL.

## <a name="examples"></a>Exempel

I det här avsnittet visas några exempel på olika tidpunkter för Live-värden som tilldelas behållare och objekt:

### <a name="example-1"></a>Exempel 1

TTL för container är inställt på null (DefaultTimeToLive = null)

|TTL för objekt| Resultat|
|---|---|
|TTL = null|    TTL har inaktiverats. Objektet upphör aldrig att gälla (standard).|
|TTL =-1   |TTL har inaktiverats. Objektet upphör aldrig att gälla.|
|TTL = 2000 |TTL har inaktiverats. Objektet upphör aldrig att gälla.|


### <a name="example-2"></a>Exempel 2

TTL för container är inställt på-1 (DefaultTimeToLive =-1)

|TTL för objekt| Resultat|
|---|---|
|TTL = null |TTL har Aktiver ATS. Objektet upphör aldrig att gälla (standard).|
|TTL =-1   |TTL har Aktiver ATS. Objektet upphör aldrig att gälla.|
|TTL = 2000 |TTL har Aktiver ATS. Objektet upphör att gälla efter 2000 sekunder.|


### <a name="example-3"></a>Exempel 3

TTL på container är inställt på 1000 (DefaultTimeToLive = 1000)

|TTL för objekt| Resultat|
|---|---|
|TTL = null|    TTL har Aktiver ATS. Objektet upphör att gälla efter 1000 sekunder (standard).|
|TTL =-1   |TTL har Aktiver ATS. Objektet upphör aldrig att gälla.|
|TTL = 2000 |TTL har Aktiver ATS. Objektet upphör att gälla efter 2000 sekunder.|

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar Time to Live i följande artiklar:

* [Så här konfigurerar du Time to Live](how-to-time-to-live.md)
