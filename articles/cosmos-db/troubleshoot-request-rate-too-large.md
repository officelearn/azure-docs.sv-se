---
title: Felsöka Azure Cosmos DB begär ande frekvens för stora undantag
description: Lär dig hur du diagnostiserar och åtgärdar begärans frekvens för stora undantag.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 10917ce51ff64292809f2fa8799c84b5cdeb3d6c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097914"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Diagnostisera och Felsök Azure Cosmos DB begär ande frekvens för stora undantag
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En "begär ande frekvens för stor"-meddelande eller felkod 429 anger att dina förfrågningar begränsas.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande avsnitt innehåller kända orsaker och lösningar för för många begär Anden.

### <a name="check-the-metrics"></a>Kontrol lera måtten
Kontrol [Azure Cosmos DB övervakning](monitor-cosmos-db.md) för att se antalet 429-undantag.

#### <a name="cause"></a>Orsak:
Det förbrukade data flödet (enheter för programbegäran per sekund) har överskridit det [etablerade data flödet](set-throughput.md). SDK: n försöker automatiskt att utföra förfrågningar baserat på den angivna återförsöks principen. Om du får det här felet ofta bör du överväga att öka data flödet för samlingen. Kontrol lera portalens mått för att se om du får 429 fel. Granska din partitionsnyckel för att säkerställa att den resulterar i en [jämn fördelning av lagrings utrymme och begär ande volym](partitioning-overview.md).

#### <a name="solution"></a>Lösning:
1. Använd [portalen eller SDK: n](set-throughput.md) för att öka det etablerade data flödet.
1. Växla databasen eller behållaren till [autoskalning](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK.
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md).