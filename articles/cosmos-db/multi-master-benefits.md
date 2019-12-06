---
title: Azure Cosmos DB flera huvud förmåner
description: Förstå fördelarna med multi-master i Azure Cosmos DB, jämförelse av latens-och SLA-krav på enkla och flera Skriv platser.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872017"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Förstå fördelarna med flera huvud servrar i Azure Cosmos DB

Cosmos DB konto ansvariga bör välja lämplig global distributions konfiguration för att säkerställa svars tid, tillgänglighet och RTO krav för sina program. Azure Cosmos-konton som kon figurer ATS med flera Skriv platser ger avsevärda fördelar för konton med en enda Skriv plats, inklusive, 99,999% tillgänglighets-SLA för Skriv beredskap, < 10 MS Skriv latens med svars tid för svars tid för 99 och RTO = 0

## <a name="comparison-of-features"></a>Jämförelse av funktioner

|Program krav|Flera Skriv platser|Enskild Skriv plats|Obs!|
|---|---|---|---|
|SLA för Skriv latens för < 10ms på P99|**Ja**|Nej|Konton med en enda Skriv plats debiteras ytterligare nätverks fördröjning i flera regioner för varje skrivning.|
|Läs latens i SLA för < 10ms på P99|**Ja**|Ja| |
|Skriv SLA på 99,999%|**Ja**|Nej|Konton med ett service avtal med en enda Skriv plats garanti på 99,99%|
|RTO = 0|**Ja**|Nej|Ingen tids gräns för skrivningar i händelse av regionala katastrofer. Konton med en enda Skriv plats har RTO på 15 min.|

## <a name="next-steps"></a>Nästa steg

Om du fortfarande vill inaktivera EnableMultipleWriteLocations i ditt Azure Cosmos-konto [öppnar du ett support ärende](https://azure.microsoft.com/support/create-ticket/).
