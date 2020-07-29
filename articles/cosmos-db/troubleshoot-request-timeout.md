---
title: Felsök Azure Cosmos DB timeout-undantag för tjänstbegäran
description: Diagnostisera och åtgärda timeout-undantag för Cosmos DB tjänstbegäran
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294528"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Diagnostisera och Felsök Azure Cosmos DB begär timeout
Azure Cosmos DB returnerade en timeout för HTTP 408-begäran

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för timeout-undantag för begäran.

### <a name="1-check-the-sla"></a>1. kontrol lera SLA
Kunden bör kontrol lera [Azure Cosmos DB övervakning](monitor-cosmos-db.md) för att kontrol lera om antalet 408 undantag strider mot Cosmos DB SLA.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Lösning 1: det strider inte mot Cosmos DB SLA
Programmet bör hantera det här scenariot och försöka igen med de här tillfälliga felen.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Lösning 2: den strider mot Cosmos DB SLA
Kontakta Azure-supporten:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. snabb partitionsnyckel
Azure Cosmos DB distribuerar det övergripande allokerade data flödet jämnt över fysiska partitioner. När det finns en aktiv partition tar en eller flera logiska partitionsnyckel på en fysisk partition över alla den fysiska partitionen RU/s, medan RU/s på andra fysiska partitioner går in. Det totala antalet RU/s som förbrukas är mindre än den totala etablerade RU/s i databasen eller behållaren, men du kommer fortfarande att se begränsning (429s) i begär Anden mot den heta logiska partitionsnyckel. Använd [måttet normaliserad ru-förbrukning](monitor-normalized-request-units.md) för att se om arbets belastningen påträffar en aktiv partition. 

#### <a name="solution"></a>Lösning:
Välj en lämplig partitionsnyckel som jämnt distribuerar begär ande volym och lagring. Lär dig hur du [ändrar partitionsnyckel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)