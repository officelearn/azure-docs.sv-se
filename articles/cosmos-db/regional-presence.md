---
title: Regionala närvaro med Azure Cosmos DB
description: Den här artikeln förklarar om regionala förekomsten av Azure Cosmos DB och olika molnmiljöer.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.custom: seodec18
ms.openlocfilehash: 2062862670f5b373d3153c473e45455e10d5e996
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548688"
---
# <a name="regional-presence-of-azure-cosmos-db"></a>Regionala förekomst av Azure Cosmos DB

Azure är för närvarande tillgängliga i [54 regioner](https://azure.microsoft.com/global-infrastructure/regions/) över hela världen. Azure Cosmos DB är en grundläggande tjänst i Azure och är tillgänglig i alla regioner där Azure är tillgängligt.

[![Regioner med Azure Cosmos DB](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB är tillgängliga i alla fem olika Azure-molnmiljöer tillgängliga för kunder:

* **Azures offentliga** moln, vilket är tillgängligt globalt.

* **Azure Kina** är tillgänglig via ett unikt partnerskap mellan Microsoft och 21Vianet, en av landets största Internetleverantörer.

* **Azure Tyskland** erbjuder tjänster enligt en dataförvaltningsmodell, vilket garanterar att kundernas data blir kvar i Tyskland kontrolleras av T-Systems International GmbH, ett dotterbolag till Deutsche telekom, fungerar som den tyska dataförvaltningen.

* **Azure Government** är tillgängligt i fyra regioner i USA för amerikanska myndigheter och deras partner. 

* **Azure Government för avdelning Defense** finns i två regioner i USA till oss Försvarsmyndighet.

## <a name="regional-presence-with-global-distribution"></a>Regionala närvaro med global distribution

Olika API: er som exponeras av Azure Cosmos DB (inklusive SQL, MongoDB, Cassandra, Gremlin och Azure Table storage) är tillgängliga i alla Azure-regioner. Exempel: du kan ha MongoDB och Cassandra-API: er som exponeras av Azure Cosmos DB inte bara i alla globala Azure-regioner utan även i Azure landsbaserade regioner som Kina, Tyskland, myndigheter och Department of Defense (DoD) regioner.

Azure Cosmos DB är en [globalt distribuerade](distribute-data-globally.md) databas. Du kan associera valfritt antal Azure-regioner med ditt Azure Cosmos-konto och dina data replikeras automatiskt och transparent. Du kan lägga till eller ta bort en region till ditt Azure Cosmos-konto när som helst. Med funktionen nyckelfärdig global distribution och multi-hanterat replikering protokoll erbjuder Azure Cosmos DB mindre än 10 ms läsa och skriva svarstid på 99: e percentilen 99,999 Läs- och skrivtillgänglighet och möjligheten att Elastiskt skala etablerats dataflöde för läsningar och skrivningar i alla regioner som är associerat med ditt Azure Cosmos-konto. Azure Cosmos DB erbjuder även fem väldefinierade konsekvensmodeller och du kan välja att använda en specifik konsekvensmodell på data. Azure Cosmos DB är dessutom den enda databastjänsten i branschen som ger en omfattande serviceavtal (SLA) omfattar etablerat dataflöde, svarstid på 99: e percentilen, hög tillgänglighet och konsekvens.

## <a name="next-steps"></a>Nästa steg

Du kan nu lära dig om olika begrepp för Azure Cosmos DB i följande artiklar:

* [Globala Datadistribution](distribute-data-globally.md)
* [Så här hanterar du ett Azure Cosmos DB-konto](manage-account.md)
* [Etablera dataflöde för Azure Cosmos-behållare och databaser](set-throughput.md)
* [Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
