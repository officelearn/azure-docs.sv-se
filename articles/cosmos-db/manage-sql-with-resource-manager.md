---
title: Skapa och hantera Azure Cosmos DB med Resource Manager-mallar
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure Cosmos DB för Core-API (SQL)
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 3684feff10ee3a09cc07276895496524fd64deee
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262318"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Hantera API-resurser för Azure Cosmos DB Core (SQL) med Azure Resource Manager-mallar

I den här artikeln får du lära dig hur du använder Azure Resource Manager mallar för att distribuera och hantera dina Azure Cosmos DB-konton, databaser och behållare.

Den här artikeln innehåller bara exempel på Azure Resource Manager mallar för Core-API-konton (SQL). Du kan också hitta mall exempel för API: er för [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)och [Table](manage-table-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Konto namn är begränsade till 44 tecken, alla gemener.
> * Distribuera om mallen med uppdaterade RU/s om du vill ändra data flödes värden.
> * När du lägger till eller tar bort platser i ett Azure Cosmos-konto kan du ändra andra egenskaper samtidigt. Dessa åtgärder måste utföras separat.

Om du vill skapa någon av Azure Cosmos DB resurserna nedan kopierar du följande exempel-mall till en ny JSON-fil. Du kan också skapa en parameter-JSON-fil som ska användas när du distribuerar flera instanser av samma resurs med olika namn och värden. Det finns många sätt att distribuera Azure Resource Manager mallar, inklusive [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Azure Cosmos-konto med autoskalning av data flöde

Den här mallen skapar ett Azure Cosmos-konto i två regioner med alternativ för konsekvens och redundans, med databas och behållare som kon figurer ATS för automatisk skalning av data flöde som har de flesta princip alternativ aktiverade. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Azure Cosmos-konto med analys lager

Den här mallen skapar ett Azure Cosmos-konto i en region med en behållare med analytiskt TTL aktiverat och alternativ för manuell eller autoskalning av data flöde. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Azure Cosmos-konto med standard-allokerat data flöde

Den här mallen skapar ett Azure Cosmos-konto i två regioner med alternativ för konsekvens och redundans, med databas och behållare som kon figurer ATS för standard data flöde som har de flesta princip alternativ aktiverade. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB behållare med funktioner på Server Sidan

Den här mallen skapar ett Azure Cosmos-konto, en databas och en container med en lagrad procedur, utlösare och användardefinierad funktion. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Azure Cosmos DB konto på den kostnads fria nivån

Den här mallen skapar ett Azure Cosmos-konto på kostnads nivå och en databas med delat data flöde som kan delas med upp till 25 behållare. Den här mallen är också tillgänglig för ett-Klicka på distribuera från galleriet för Azure snabb starts mallar.

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Nästa steg

Här följer några ytterligare resurser:

* [Azure Resource Manager dokumentation](/azure/azure-resource-manager/)
* [Schema för Azure Cosmos DB Resource Provider](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB Snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Felsök vanliga Azure Resource Manager distributions fel](../azure-resource-manager/templates/common-deployment-errors.md)
