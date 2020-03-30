---
title: Förfalla data i Azure Cosmos DB med tid att leva
description: Med TTL ger Microsoft Azure Cosmos DB möjlighet att få dokument som automatiskt rensas från systemet efter en viss tid.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188724"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live i Azure Cosmos DB 

Med **Time to Live** eller TTL ger Azure Cosmos DB möjligheten att ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Som standard kan du ange tid för att leva på behållarnivå och åsidosätta värdet per artikel. När du har angett TTL på en behållare eller på en artikelnivå tar Azure Cosmos DB automatiskt bort dessa objekt efter tidsperioden, eftersom den tidpunkt då de senast ändrades. Tid att leva-värdet konfigureras på några sekunder. När du konfigurerar TTL tar systemet automatiskt bort de utgångna objekten baserat på TTL-värdet, utan att behöva en borttagningsåtgärd som uttryckligen utfärdas av klientprogrammet.

Borttagning av utgångna objekt är en bakgrundsaktivitet som förbrukar överblivna [begärandeenheter,](request-units.md)det vill ha begärandeenheter som inte har förbrukats av användarbegäranden. Även efter att TTL har gått ut, om behållaren är överbelastad med begäranden och om det inte finns tillräckligt med RU:er tillgängliga, försenas dataradering. Data tas bort när det finns tillräckligt många ru:er tillgängliga för att utföra borttagningsåtgärden. Även om dataradering försenas returneras inte data av några frågor (av något API) efter att TTL har upphört att gälla.

## <a name="time-to-live-for-containers-and-items"></a>Dags att leva för containrar och föremål

Tiden till live-värdet anges i sekunder och tolkas som ett delta från det att ett objekt senast ändrades. Du kan ange tid för att leva på en behållare eller ett objekt i behållaren:

1. **Tid att leva på** en `DefaultTimeToLive`behållare (ställ in med):

   - Om objekt saknas (eller är inställda på null) upphör de inte att gälla automatiskt.

   - Om det finns och värdet är inställt på "-1" är det lika med oändlighet och objekt upphör inte att gälla som standard.

   - Om det finns och värdet är inställt på ett visst tal *"n"* – förfaller *objekten "n"* sekunder efter den senaste ändrade tiden.

2. **Tid att leva på** ett `ttl`objekt (ställ in med):

   - Den här egenskapen `DefaultTimeToLive` är endast tillämplig om den finns och den inte är inställd på null för den överordnade behållaren.

   - Om det finns, `DefaultTimeToLive` åsidosätter det värdet för den överordnade behållaren.

## <a name="time-to-live-configurations"></a>Tid till Live-konfigurationer

* Om TTL är inställt på *"n"* på en behållare upphör artiklarna i behållaren att gälla efter *n* sekunder.  Om det finns objekt i samma behållare som har sin egen tid att leva, ställ in på -1 (vilket indikerar att de inte upphör att gälla) eller om vissa objekt har åsidosatt tiden för att leva inställningen med ett annat nummer, dessa objekt upphör att gälla baserat på deras egna konfigurerade TTL-värde. 

* Om TTL inte är inställt på en behållare har tiden att leva på ett objekt i den här behållaren ingen effekt. 

* Om TTL på en behållare är inställt på -1 upphör ett objekt i den här behållaren som har tid att leva inställt på n att gälla efter n sekunder och återstående objekt upphör inte att gälla.

## <a name="examples"></a>Exempel

I det här avsnittet visas några exempel med olika tid till livevärden som tilldelats behållare och objekt:

### <a name="example-1"></a>Exempel 1

TTL på behållaren är inställd på null (DefaultTimeToLive = null)

|TTL på objekt| Resultat|
|---|---|
|ttl = null|    TTL är inaktiverat. Objektet upphör aldrig att gälla (standard).|
|ttl = -1   |TTL är inaktiverat. Objektet upphör aldrig att gälla.|
|ttl = 2000 |TTL är inaktiverat. Objektet upphör aldrig att gälla.|


### <a name="example-2"></a>Exempel 2

TTL på behållaren är inställd på -1 (DefaultTimeToLive = -1)

|TTL på objekt| Resultat|
|---|---|
|ttl = null |TTL är aktiverat. Objektet upphör aldrig att gälla (standard).|
|ttl = -1   |TTL är aktiverat. Objektet upphör aldrig att gälla.|
|ttl = 2000 |TTL är aktiverat. Objektet upphör att gälla efter 2000 sekunder.|


### <a name="example-3"></a>Exempel 3

TTL på behållaren är inställd på 1000 (DefaultTimeToLive = 1000)

|TTL på objekt| Resultat|
|---|---|
|ttl = null|    TTL är aktiverat. Objektet upphör att gälla efter 1000 sekunder (standard).|
|ttl = -1   |TTL är aktiverat. Objektet upphör aldrig att gälla.|
|ttl = 2000 |TTL är aktiverat. Objektet upphör att gälla efter 2000 sekunder.|

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar Tid att leva i följande artiklar:

* [Så här konfigurerar du Tid att leva](how-to-time-to-live.md)
