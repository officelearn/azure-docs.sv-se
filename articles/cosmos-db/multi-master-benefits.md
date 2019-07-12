---
title: Flera huvudservrar fördelarna med Azure Cosmos DB
description: Förstå fördelarna med multimaster i Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789298"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Förstå flera huvudservrar fördelar i Azure Cosmos DB

Cosmos DB-Kontoansvariga bör välja lämplig globalt inställningar och kontrollera svarstid, tillgänglighet och RTO-krav för sina program. Azure Cosmos-konton som konfigurerats med flera platser för skrivning erbjuder betydande fördelar över konton med enkel skrivning plats inklusive, skriva 99,999% tillgänglighet, < 10 ms skriva svarstid SLA på 99: e percentilen och RTO = 0 i ett regionalt haveri.

## <a name="comparison-of-features"></a>Jämförelse av funktioner

|Programkrav|Flera platser för skrivning|Skrivning av enskild plats|Obs!|
|---|---|---|---|
|Skriva svarstid SLA med < 10 MS vid P99|**Ja**|Nej|Konton med samma skriva plats debiteras ytterligare interregionala Nätverksfördröjningen för varje skrivning.|
|Läsa svarstid SLA med < 10 MS vid P99|**Ja**|Ja| |
|Skriva serviceavtal på 99,999%|**Ja**|Nej|Konton med samma skriva plats garanterar SLA med 99,99%|
|RTO = 0|**Ja**|Nej|Inga driftstopp för skrivning vid regionala katastrofer. Konton med enda Skriv-platsen har RTO av 15 min.|

## <a name="next-steps"></a>Nästa steg

Om du vill fortfarande att inaktivera EnableMultipleWriteLocations i ditt Azure Cosmos-konto kan du [öppna ett supportärende](https://azure.microsoft.com/support/create-ticket/).
