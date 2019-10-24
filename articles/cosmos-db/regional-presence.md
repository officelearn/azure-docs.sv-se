---
title: Regional närvaro med Azure Cosmos DB
description: I den här artikeln beskrivs regional förekomst av Azure Cosmos DB och olika moln miljöer.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753231"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regional närvaro med Azure Cosmos DB

Azure Cosmos DB är en grundläggande tjänst i Azure och är som standard alltid tillgänglig i alla regioner där Azure är tillgängligt. Azure är för närvarande tillgängligt i [54-regioner](https://azure.microsoft.com/global-infrastructure/regions/) över hela världen. 

[![regioner där Azure Cosmos DB är tillgängligt](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB är tillgängligt i alla fem olika Azure Cloud-miljöer som är tillgängliga för kunder:

* **Offentligt Azure** -moln, som är tillgängligt globalt.

* **Azure Kina 21Vianet** är tillgängligt via ett unikt partnerskap mellan Microsoft och 21Vianet, en av landets största Internet leverantörer i Kina.

* **Azure Germany** tillhandahåller tjänster under en data förvaltar modell, som säkerställer att kunddata förblir i Tyskland under kontroll av T-Systems International GmbH, ett dotter bolag till tyska telekom, som fungerar som den tyska data förvaltaren.

* **Azure Government** finns i fyra regioner i USA till myndigheter i myndigheter och deras partner. 

* **Azure Government för DoD (Department of försvar)** är tillgänglig i två regioner i USA till USA: s försvars departement.

## <a name="regional-presence-with-global-distribution"></a>Regional närvaro med global distribution

Alla API: er som exponeras av Azure Cosmos DB (inklusive SQL, MongoDB, Cassandra, Gremlin och Table) är tillgängliga i alla Azure-regioner som standard. Du kan till exempel ha MongoDB-och Cassandra-API: er som exponeras av Azure Cosmos DB inte bara i alla globala Azure-regioner, utan även i suveräna moln som Kina, Tyskland, myndigheter och DoD (Department of försvar).

Azure Cosmos DB är en [globalt distribuerad](distribute-data-globally.md) databas tjänst. Du kan associera ett valfritt antal Azure-regioner med ditt Azure Cosmos-konto så kommer dina data automatiskt att replikeras. Du kan när som helst lägga till eller ta bort en region i ditt Azure Cosmos-konto. Med den nyckel färdiga globala distributions funktionen och multi-masterd Replication-protokollet, Azure Cosmos DB erbjuda mindre än 10 MS-Läs-och skriv fördröjningar på 99: e percentilen, 99,999 Läs-och skriv tillgänglighet och möjlighet att elastiskt skala etablerade data flöde för läsningar och skrivningar i alla regioner som är kopplade till ditt Azure Cosmos-konto. Azure Cosmos DB erbjuder även fem väldefinierade konsekvens modeller och du kan välja att tillämpa en viss konsekvens modell för dina data. Slutligen är Azure Cosmos DB den enda databas tjänsten i branschen som ger en heltäckande [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) som omfattar tillhandahållet data flöde, svars tid vid 99 percentil, hög tillgänglighet och konsekvens. Ovanstående funktioner är tillgängliga i alla Azure-moln.

## <a name="next-steps"></a>Nästa steg

Nu kan du lära dig mer om grundläggande begrepp för Azure Cosmos DB med följande artiklar:

* [Global data distribution](distribute-data-globally.md)
* [Hantera ett Azure Cosmos DB konto](manage-account.md)
* [Etablera data flöde för Azure Cosmos-behållare och databaser](set-throughput.md)
* [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
