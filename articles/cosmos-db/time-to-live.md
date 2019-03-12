---
title: Ta bort data i Azure Cosmos DB med Time to Live
description: Med TTL tillhandahåller Microsoft Azure Cosmos DB möjligheten att låta dokument automatiskt bort från systemet efter en viss tidsperiod.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: f9dec5b3aeb951316985c965de70a372f55b8225
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57549195"
---
# <a name="time-to-live-in-azure-cosmos-db"></a>TTL-värde i Azure Cosmos DB 

Med ”Time to Live” eller TTL kan Azure Cosmos DB du ta bort objekt automatiskt från en behållare efter en viss tidsperiod. Du kan ange tiden för live på behållarenivån och åsidosättningsvärde på basis av per objekt som standard. När du ställer in TTL-Perioden på en behållare eller på en nivå, tar Azure Cosmos DB automatiskt bort dessa objekt efter hur lång tid sedan du de senast ändrades. Tid TTL-värde konfigureras på några sekunder. När du konfigurerar TTL systemet automatiskt att ta bort inaktuella objekt baserat på TTL-värdet, till skillnad från en borttagningsåtgärd som uttryckligen har utfärdats av klientprogrammet.

## <a name="time-to-live-for-containers-and-items"></a>TTL-värde för behållare och objekt

Tiden TTL-värde har angetts i sekunder och tolkas det som en lista från den tidpunkt då ett objekt senast ändrades. Du kan ställa in TTL-värde på en behållare eller ett objekt i behållaren:

1. **Time to Live-värde i en behållare** (anges med `DefaultTimeToLive`):

   - Om det saknas (eller inställt på null) objekt inte har förfallit automatiskt.

   - Om närvarande och värdet är inställt på ”-1”, är det lika oändlig – objekt upphör att gälla inte som standard.

   - Om närvarande och värdet anges till något nummer (”n”) – objekt upphör att gälla ”n” sekunder efter deras senast ändrades.

2. **Time to Live-värde på ett objekt** (anges med `ttl`):

   - Den här egenskapen gäller bara om `DefaultTimeToLive` finns och inte har angetts till null för den överordnade behållaren.

   - Om det finns åsidosätter den `DefaultTimeToLive` värdet för den överordnade behållaren.

## <a name="time-to-live-configurations"></a>Tid för Live-konfigurationer

* Om TTL-värdet är inställt på ”n” i en behållare, upphör objekten i den behållaren efter n sekunder.  Om det finns objekt i samma behållare som har sina egna tid att live genom att ange -1 (som anger att de inte går ut) eller om några objekt har åsidosatts time to live inställning med ett annat nummer gäller de här objekten går ut baserat på det konfigurerade TTL-värdet. 

* Om TTL inte har angetts för en behållare har ingen effekt med time to live på ett objekt i den här behållaren. 

* Om TTL-värdet från en behållare har värdet-1, ett objekt i den här behållaren som innehåller time to live inställt n, upphör att gälla efter n sekunder och återstående objekt upphör inte att gälla. 

Tar bort objekt baserat på TTL är kostnadsfri. Det finns ingen extra kostnad (det vill säga används inga ytterligare ru: er) när objektet tas bort till följd av TTL upphör.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar Time to Live i följande artiklar:

* [Så här konfigurerar du Time to Live](how-to-time-to-live.md)
