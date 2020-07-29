---
title: Felsöka Azure Cosmos DB begär ande frekvens till ett stort undantag
description: Så här diagnostiserar och korrigerar du begär ande frekvens till ett stort undantag
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294525"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Diagnostisera och Felsök Cosmos DB för många begär Anden
"Begär ande frekvens för stor" eller felkod 429 anger att dina begär Anden begränsas.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för för många begär Anden.

### <a name="1-check-the-metrics"></a>1. kontrol lera måtten
Kunden bör kontrol lera [Azure Cosmos DB övervakning](monitor-cosmos-db.md) för att kontrol lera om antalet 429-undantag.

## <a name="cause"></a>Orsak:
Det förbrukade data flödet (RU/s) har överskridit det [etablerade data flödet](set-throughput.md). SDK: n kommer automatiskt att försöka utföra begär anden igen baserat på den angivna återförsöks principen. Om du får det här felet ofta bör du överväga att öka data flödet för samlingen. Kontrol lera portalens mått för att se om du får 429 fel. Granska din partitionsnyckel för att säkerställa att den resulterar i en [jämn fördelning av lagrings utrymme och begär ande volym](partition-data.md).

## <a name="solution"></a>Lösning:
1. Använd [portalen eller SDK: n](set-throughput.md) för att öka det etablerade data flödet.
2. Växla databasen eller behållaren till [autoskalning](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)