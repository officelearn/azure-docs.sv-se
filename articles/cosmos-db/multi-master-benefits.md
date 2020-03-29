---
title: Azure Cosmos DB multi-master fördelar
description: Förstå fördelarna med multi-master i Azure Cosmos DB, jämförelse av svarstid och SLA-krav på enstaka och flera skrivplatser.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872017"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Förstå multi-master-fördelar i Azure Cosmos DB

Cosmos DB-kontooperatörer bör välja lämplig global distributionskonfiguration för att säkerställa svarstid, tillgänglighet och RTO-krav för sina program. Azure Cosmos-konton som konfigurerats med flera skrivplatser erbjuder betydande fördelar jämfört med konton med en enda skrivplats, inklusive 99,999 % SLA för skrivtillgänglighet, <10 ms skrivfördröjnings-SLA vid 99:e percentilen och RTO = 0 i en regional katastrof.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

|Ansökan krav|Flera skrivplatser|Enskild skrivplats|Obs!|
|---|---|---|---|
|Skriv latens SLA på <10ms på P99|**Ja**|Inga|Konton med enskild skrivplats medför ytterligare nätverksfördröjning mellan regioner för varje skrivning.|
|Läs latens SLA på <10ms på P99|**Ja**|Ja| |
|Skriv SLA på 99,999%|**Ja**|Inga|Konton med garanti-serviceavtal med en enda skrivplats på 99,99 %|
|RTO = 0|**Ja**|Inga|Noll ner tid för skriver i händelse av regionala katastrofer. Konton med enda skrivplats har RTO på 15 min.|

## <a name="next-steps"></a>Efterföljande moment

Om du fortfarande vill inaktivera EnableMultipleWriteLocations i ditt Azure Cosmos-konto öppnar du [en supportbiljett](https://azure.microsoft.com/support/create-ticket/).
