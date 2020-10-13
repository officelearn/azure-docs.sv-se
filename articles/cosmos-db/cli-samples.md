---
title: Azure CLI-exempel för Azure Cosmos DB
description: Azure CLI-exempel för Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 58460dffad3d44090644a544f4082b7727ece3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840344"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure Cosmos DB. Använd länkarna till höger för att navigera till API-/regionsspecifika exempel. Vanliga exempel är samma för alla API: er. Referens sidor för alla Azure Cosmos DB CLI-kommandon är tillgängliga i [Azure CLI-referensen](/cli/azure/cosmosdb). Du kan också hitta Azure Cosmos DB CLI-skript exempel i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

De här exemplen kräver Azure CLI version 2.9.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Vanliga exempel

Dessa exempel gäller alla Azure Cosmos DB API: er

|Uppgift | Beskrivning |
|---|---|
| [Lägga till eller redundansväxla regioner](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Lägg till en region, ändra prioritet för redundans, Utlös en manuell redundansväxling.|
| [Konto nycklar och anslutnings strängar](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Visa lista med konto nycklar, skrivskyddade nycklar, återskapa nycklar och lista anslutnings strängar.|
| [Skydda med IP-brandvägg](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto med IP-brandvägg konfigurerad.|
| [Säkert nytt konto med tjänst slut punkter](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto och skydda med tjänst slut punkter.|
| [Skydda det befintliga kontot med tjänstens slut punkter](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Uppdatera ett Cosmos-konto för att skydda med tjänst slut punkter när under nätet slutligen har kon figurer ATS.|
|||

## <a name="core-sql-api-samples"></a>API-exempel för Core (SQL)

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto, en databas och en behållare](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, databas och behållare för Core-API (SQL). |
| [Skapa ett Azure Cosmos-konto, en databas och en behållare med autoskalning](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en behållare med autoskalning för Core-API (SQL). |
| [Data flödes åtgärder](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard-genomflöde i en databas och behållare.|
| [Lås resurser från borttagning](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||

## <a name="mongodb-api-samples"></a>MongoDB API-exempel

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto, en databas och en samling](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en samling för MongoDB-API: et. |
| [Skapa ett Azure Cosmos-konto, en databas med autoskalning och två samlingar med delat data flöde](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas med autoskalning och två samlingar med delat data flöde för MongoDB-API. |
| [Data flödes åtgärder](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard-genomflöde i en databas och samling.|
| [Lås resurser från borttagning](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||

## <a name="cassandra-api-samples"></a>API för Cassandra exempel

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto, ett tecken utrymme och en tabell](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, ett blank steg och en tabell för API för Cassandra. |
| [Skapa ett Azure Cosmos-konto, nyckel utrymme och tabell med autoskalning](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, nyckel utrymme och tabell med autoskalning för API för Cassandra. |
| [Data flödes åtgärder](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard-genomflöde i ett nyckel utrymme och en tabell.|
| [Lås resurser från borttagning](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||

## <a name="gremlin-api-samples"></a>Gremlin API-exempel

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto, en databas och en graf](scripts/cli/gremlin/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en graf för Gremlin-API. |
| [Skapa ett Azure Cosmos-konto, en databas och en graf med autoskalning](scripts/cli/gremlin/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en graf med autoskalning för Gremlin-API. |
| [Data flödes åtgärder](scripts/cli/gremlin/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard-genomflöde i en databas och graf.|
| [Lås resurser från borttagning](scripts/cli/gremlin/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||

## <a name="table-api-samples"></a>Tabell-API exempel

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto och en tabell](scripts/cli/table/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto och en tabell för Tabell-API. |
| [Skapa ett Azure Cosmos-konto och en tabell med autoskalning](scripts/cli/table/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB-konto och en tabell med autoskalning för Tabell-API. |
| [Data flödes åtgärder](scripts/cli/table/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard data flöde i en tabell.|
| [Lås resurser från borttagning](scripts/cli/table/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||
