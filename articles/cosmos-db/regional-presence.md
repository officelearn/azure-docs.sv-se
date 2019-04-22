---
title: Regionala närvaro med Azure Cosmos DB
description: Den här artikeln förklarar om regionala förekomsten av Azure Cosmos DB och olika molnmiljöer.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 851da72a308b164b9a01b7ccfa3d541fc06810ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762199"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionala närvaro med Azure Cosmos DB

Azure Cosmos DB är en grundläggande tjänst i Azure och, som standard är alltid tillgänglig i alla regioner där Azure är tillgängligt. Azure är för närvarande tillgängliga i [54 regioner](https://azure.microsoft.com/global-infrastructure/regions/) över hela världen. 

[![Regioner med Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB är tillgängliga i alla fem olika Azure-molnmiljöer tillgängliga för kunder:

* **Azures offentliga** moln, vilket är tillgängligt globalt.

* **Azure Kina 21Vianet** är tillgänglig via ett unikt partnerskap mellan Microsoft och 21Vianet, en av landets största Internetleverantörer i Kina.

* **Azure Tyskland** erbjuder tjänster enligt en dataförvaltningsmodell, vilket garanterar att kundernas data blir kvar i Tyskland kontrolleras av T-Systems International GmbH, ett dotterbolag till Deutsche telekom, fungerar som den tyska dataförvaltningen.

* **Azure Government** är tillgängligt i fyra regioner i USA för amerikanska myndigheter och deras partner. 

* **Azure Government för Department of Defense (DoD)** finns i två regioner i USA till oss Försvarsmyndighet.

## <a name="regional-presence-with-global-distribution"></a>Regionala närvaro med global distribution

Alla API: er som exponeras av Azure Cosmos DB (inklusive SQL, MongoDB, Cassandra, Gremlin och tabellen) är tillgängliga i alla Azure-regioner som standard. Exempel: du kan ha MongoDB och Cassandra-API: er som exponeras av Azure Cosmos DB inte bara i alla globala Azure-regioner, men även i suveräna moln som Kina, Tyskland, myndigheter och Department of Defense (DoD) regioner.

Azure Cosmos DB är en [globalt distribuerade](distribute-data-globally.md) service-databas. Du kan associera valfritt antal Azure-regioner med ditt Azure Cosmos-konto och dina data replikeras automatiskt och transparent. Du kan lägga till eller ta bort en region till ditt Azure Cosmos-konto när som helst. Med funktionen nyckelfärdig global distribution och multi-hanterat replikering protokoll erbjuder Azure Cosmos DB mindre än 10 ms läsa och skriva svarstid på 99: e percentilen 99,999 Läs- och skrivtillgänglighet och möjligheten att Elastiskt skala etablerats dataflöde för läsningar och skrivningar i alla regioner som är associerat med ditt Azure Cosmos-konto. Azure Cosmos DB erbjuder även fem väldefinierade konsekvensmodeller och du kan välja att använda en specifik konsekvensmodell på data. Slutligen Azure Cosmos DB är den enda databastjänsten i branschen som ger en omfattande [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) avseende etablerat dataflöde, svarstid på 99: e percentilen, hög tillgänglighet, och konsekvens. Ovanstående funktioner är tillgängliga i alla Azure-moln.

## <a name="next-steps"></a>Nästa steg

Du kan nu lära dig om grundläggande begrepp i Azure Cosmos DB i följande artiklar:

* [Globala Datadistribution](distribute-data-globally.md)
* [Så här hanterar du ett Azure Cosmos DB-konto](manage-account.md)
* [Etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
