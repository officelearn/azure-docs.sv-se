---
title: Felsöka tids gräns undantag för Azure Cosmos DB tjänstbegäran
description: Lär dig hur du diagnostiserar och åtgärdar tids gränser för Azure Cosmos DB tjänstbegäran.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 76a1558534728613dcdedc78b64a0366f2bd643d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871079"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB begär timeout-undantag
Azure Cosmos DB returnerade en timeout för HTTP 408-begäran.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för timeout-undantag för begäran.

### <a name="check-the-sla"></a>Kontrol lera SLA
Kontrol lera [Azure Cosmos DB övervakning](monitor-cosmos-db.md) för att se om antalet undantag i 408 strider mot Azure Cosmos DB SLA.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Lösning 1: den strider mot Azure Cosmos DB SLA
Programmet bör hantera det här scenariot och försöka igen med de här tillfälliga felen.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Lösning 2: den strider mot Azure Cosmos DB SLA
Kontakta [Azure-supporten](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Nyckel för snabb partition
Azure Cosmos DB distribuerar det övergripande allokerade data flödet jämnt över fysiska partitioner. När det finns en aktiv partition tar en eller flera logiska partitionsnyckel på en fysisk partition över alla den fysiska partitionens enheter för programbegäran per sekund (RU/s). På samma gång går RU/s på andra fysiska partitioner oanvända. Det totala antalet RU/s som är förbrukat kommer att vara mindre än den totala etablerade RU/s på databasen eller behållaren. Du ser fortfarande begränsning (429s) i begär Anden mot den heta logiska partitionsnyckel. Använd [måttet normaliserad ru-förbrukning](monitor-normalized-request-units.md) för att se om arbets belastningen påträffar en aktiv partition. 

#### <a name="solution"></a>Lösning:
Välj en lämplig partitionsnyckel som jämnt distribuerar begär ande volym och lagring. Lär dig hur du [ändrar partitionsnyckel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK.
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md).