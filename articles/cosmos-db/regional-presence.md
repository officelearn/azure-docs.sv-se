---
title: Regional närvaro med Azure Cosmos DB
description: Den här artikeln förklarar om den regionala närvaron av Azure Cosmos DB och olika molnmiljöer.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753231"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regional närvaro med Azure Cosmos DB

Azure Cosmos DB är en grundläggande tjänst i Azure och är som standard alltid tillgänglig i alla regioner där Azure är tillgängligt. För närvarande är Azure tillgängligt i [54 regioner](https://azure.microsoft.com/global-infrastructure/regions/) över hela världen. 

[![Regioner där Azure Cosmos DB är tillgängligt](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB är tillgängligt i alla fem olika Azure-molnmiljöer som är tillgängliga för kunder:

* **Azure offentligt** moln, som är tillgängligt globalt.

* **Azure China 21Vianet** är tillgängligt genom ett unikt partnerskap mellan Microsoft och 21Vianet, en av landets största internetleverantörer i Kina.

* **Azure Germany** tillhandahåller tjänster enligt en dataförvaltare modell, som säkerställer att kunddata kvar i Tyskland under kontroll av T-Systems International GmbH, ett dotterbolag till Deutsche Telecom, som agerar som den tyska dataförvaltare.

* **Azure Government** är tillgängligt i fyra regioner i USA för amerikanska myndigheter och deras partner. 

* **Azure Government for Department of Defense (DoD)** är tillgängligt i två regioner i USA till DET AMERIKANSKA försvarsdepartementet.

## <a name="regional-presence-with-global-distribution"></a>Regional närvaro med global distribution

Alla API:er som exponeras av Azure Cosmos DB (inklusive SQL, MongoDB, Cassandra, Gremlin och Table) är som standard tillgängliga i alla Azure-regioner. Du kan till exempel ha MongoDB- och Cassandra-API:er som exponeras av Azure Cosmos DB inte bara i alla globala Azure-regioner, utan även i suveräna moln som Kina, Tyskland, regeringen och Försvarsdepartementet (DoD) regioner.

Azure Cosmos DB är en [globalt distribuerad](distribute-data-globally.md) databastjänst. Du kan associera valfritt antal Azure-regioner med ditt Azure Cosmos-konto och dina data replikeras automatiskt och transparent. Du kan lägga till eller ta bort en region i ditt Azure Cosmos-konto när som helst. Med den nyckelfärdiga globala distributionskapaciteten och flerbehärskningsprotokollet erbjuder Azure Cosmos DB mindre än 10 ms läs- och skrivfördrevenden vid den 99:e percentilen, 99,999 läs- och skrivtillgänglighet och möjlighet att elastiskt skala etablerat dataflöde för läsningar och skrivningar i alla regioner som är associerade med ditt Azure Cosmos-konto. Azure Cosmos DB, erbjuder också fem väldefinierade konsekvensmodeller och du kan välja att tillämpa en specifik konsekvensmodell på dina data. Slutligen är Azure Cosmos DB den enda databastjänsten i branschen som tillhandahåller ett omfattande [servicenivåavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) som omfattar etablerat dataflöde, svarstid vid den 99:e percentilen, hög tillgänglighet och konsekvens. Ovanstående funktioner är tillgängliga i alla Azure-moln.

## <a name="next-steps"></a>Nästa steg

Du kan nu lära dig mer om grundläggande begrepp i Azure Cosmos DB med följande artiklar:

* [Global datadistribution](distribute-data-globally.md)
* [Hantera ett Azure Cosmos DB-konto](manage-account.md)
* [Etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md)
* [DLA för Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
