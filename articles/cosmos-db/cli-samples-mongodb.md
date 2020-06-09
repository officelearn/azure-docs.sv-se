---
title: Azure CLI-exempel för Azure Cosmos DB MongoDB-API
description: Azure CLI-exempel för Azure Cosmos DB MongoDB-API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: af510e5d29e494e96f6df6143fe012db20fb6ca4
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509465"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Azure CLI-exempel för Azure Cosmos DB MongoDB-API

Följande tabell innehåller länkar till exempel på Azure CLI-skript för Azure Cosmos DB MongoDB-API. Referens sidor för alla Azure Cosmos DB CLI-kommandon är tillgängliga i [Azure CLI-referensen](/cli/azure/cosmosdb). Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> För närvarande kan du bara skapa 3,2-versionen (det vill säga konton som använder slut punkten i formatet `*.documents.azure.com` ) för Azure Cosmos DBS API för MongoDB-konton med hjälp av PowerShell-, CLI-och Resource Manager-mallar. Om du vill skapa en 3,6-version av konton använder du i stället Azure Portal.

| |  |
|---|---|
| [Skapa ett Azure Cosmos-konto, en databas och en samling](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB konto, en databas och en samling för MongoDB-API: et. |
| [Ändra data flöde](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Uppdatera RU/s i en databas och samling.|
| [Lägga till eller redundansväxla regioner](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Lägg till en region, ändra prioritet för redundans, Utlös en manuell redundansväxling.|
| [Konto nycklar och anslutnings strängar](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Visa lista med konto nycklar, skrivskyddade nycklar, återskapa nycklar och lista anslutnings strängar.|
| [Skydda med IP-brandvägg](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto med IP-brandvägg konfigurerad.|
| [Säkert nytt konto med tjänst slut punkter](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto och skydda med tjänst slut punkter.|
| [Skydda det befintliga kontot med tjänstens slut punkter](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Uppdatera ett Cosmos-konto för att skydda med tjänst slut punkter när under nätet slutligen har kon figurer ATS.|
| [Lås resurser från borttagning](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Förhindra att resurser tas bort med resurs lås.|
|||
