---
title: PowerShell-skript för att skapa Azure Cosmos DB Gremlin-API-databas och Graf med autoskalning
description: Azure PowerShell skript – Azure Cosmos DB skapa Gremlin-API-databas och Graf med autoskalning
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 0fec28b880dad8b610833f38aaa6ea0f53d157f8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506756"
---
# <a name="create-a-database-and-graph-with-autoscale-for-azure-cosmos-db---gremlin-api"></a>Skapa en databas och en graf med autoskalning för Azure Cosmos DB-Gremlin-API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-autoscale.ps1 "Create a database and graph with autoscale for Gremlin API")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Skapar ett Cosmos DB-konto. |
| [New-AzCosmosDBGremlinDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbgremlindatabase) | Skapar en Gremlin-API-databas. |
| [New-AzCosmosDBGremlinGraph](/powershell/module/az.cosmosdb/new-azcosmosdbgremlingraph) | Skapar ett Gremlin-API-diagram. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../../../powershell-samples.md).