---
title: Azure CLI-exempel för Azure Cosmos DB API för MongoDB
description: Azure CLI-exempel för Azure Cosmos DB API för MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 41943a30f78bd906d314d6ec10fa01866708f534
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073054"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>Azure CLI-exempel för Azure Cosmos DB API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure Cosmos DB. Använd länkarna till höger för att navigera till API-/regionsspecifika exempel. Vanliga exempel är samma för alla API: er. Referens sidor för alla Azure Cosmos DB CLI-kommandon är tillgängliga i [Azure CLI-referensen](/cli/azure/cosmosdb). Du kan också hitta Azure Cosmos DB CLI-skript exempel i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

De här exemplen kräver Azure CLI version 2.12.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>Vanliga exempel

Dessa exempel gäller alla Azure Cosmos DB API: er

|Uppgift | Beskrivning |
|---|---|
| [Lägga till eller redundansväxla regioner](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Lägg till en region, ändra prioritet för redundans, Utlös en manuell redundansväxling.|
| [Konto nycklar och anslutnings strängar](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Visa lista med konto nycklar, skrivskyddade nycklar, återskapa nycklar och lista anslutnings strängar.|
| [Skydda med IP-brandvägg](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto med IP-brandvägg konfigurerad.|
| [Säkert nytt konto med tjänst slut punkter](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto och skydda med tjänst slut punkter.|
| [Skydda det befintliga kontot med tjänstens slut punkter](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Uppdatera ett Cosmos-konto för att skydda med tjänst slut punkter när under nätet slutligen har kon figurer ATS.|
|||

## <a name="mongodb-api-samples"></a>MongoDB API-exempel

|Uppgift | Beskrivning |
|---|---|
| [Skapa ett Azure Cosmos-konto, en databas och en samling](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en samling för MongoDB-API: et. |
| [Skapa ett Azure Cosmos-konto, en databas med autoskalning och två samlingar med delat data flöde](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas med autoskalning och två samlingar med delat data flöde för MongoDB-API. |
| [Data flödes åtgärder](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Läsa, uppdatera och migrera mellan autoskalning och standard-genomflöde i en databas och samling.|
| [Lås resurser från borttagning](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||
