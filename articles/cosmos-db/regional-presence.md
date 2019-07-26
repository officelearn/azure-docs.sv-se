---
title: Regionala närvaro med Azure Cosmos DB
description: Den här artikeln förklarar om regionala förekomsten av Azure Cosmos DB och olika molnmiljöer.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 775ca8c57a9a444df2f7d0a4df4224b7eba9d677
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384040"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionala närvaro med Azure Cosmos DB

Azure Cosmos DB är en grundläggande tjänst i Azure och är som standard alltid tillgänglig i alla regioner där Azure är tillgängligt. Azure är för närvarande tillgängliga i [54 regioner](https://azure.microsoft.com/global-infrastructure/regions/) över hela världen. 

[![Regioner där Azure Cosmos DB är tillgängligt](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB är tillgängliga i alla fem olika Azure-molnmiljöer tillgängliga för kunder:

* **Azures offentliga** moln, vilket är tillgängligt globalt.

* **Azure Kina 21Vianet** är tillgängligt via ett unikt partnerskap mellan Microsoft och 21Vianet, en av landets största Internet leverantörer i Kina.

* **Azure Tyskland** erbjuder tjänster enligt en dataförvaltningsmodell, vilket garanterar att kundernas data blir kvar i Tyskland kontrolleras av T-Systems International GmbH, ett dotterbolag till Deutsche telekom, fungerar som den tyska dataförvaltningen.

* **Azure Government** är tillgängligt i fyra regioner i USA för amerikanska myndigheter och deras partner. 

* **Azure Government för DoD (Department of försvar)** är tillgänglig i två regioner i USA till USA: s försvars departement.

## <a name="regional-presence-with-global-distribution"></a>Regionala närvaro med global distribution

Alla API: er som exponeras av Azure Cosmos DB (inklusive SQL, MongoDB, Cassandra, Gremlin och Table) är tillgängliga i alla Azure-regioner som standard. Du kan till exempel ha MongoDB-och Cassandra-API: er som exponeras av Azure Cosmos DB inte bara i alla globala Azure-regioner, utan även i suveräna moln som Kina, Tyskland, myndigheter och DoD (Department of försvar).

Azure Cosmos DB är en [globalt distribuerad](distribute-data-globally.md) databas tjänst. Du kan associera valfritt antal Azure-regioner med ditt Azure Cosmos-konto och dina data replikeras automatiskt och transparent. Du kan lägga till eller ta bort en region till ditt Azure Cosmos-konto när som helst. Med funktionen nyckelfärdig global distribution och multi-hanterat replikering protokoll erbjuder Azure Cosmos DB mindre än 10 ms läsa och skriva svarstid på 99: e percentilen 99,999 Läs- och skrivtillgänglighet och möjligheten att Elastiskt skala etablerats dataflöde för läsningar och skrivningar i alla regioner som är associerat med ditt Azure Cosmos-konto. Azure Cosmos DB erbjuder även fem väldefinierade konsekvensmodeller och du kan välja att använda en specifik konsekvensmodell på data. Slutligen är Azure Cosmos DB den enda databas tjänsten i branschen som ger en heltäckande [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) som omfattar tillhandahållet data flöde, svars tid vid 99 percentil, hög tillgänglighet och konsekvens. Ovanstående funktioner är tillgängliga i alla Azure-moln.

## <a name="next-steps"></a>Nästa steg

Nu kan du lära dig mer om grundläggande begrepp för Azure Cosmos DB med följande artiklar:

* [Globala Datadistribution](distribute-data-globally.md)
* [Så här hanterar du ett Azure Cosmos DB-konto](manage-account.md)
* [Etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md)
* [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
