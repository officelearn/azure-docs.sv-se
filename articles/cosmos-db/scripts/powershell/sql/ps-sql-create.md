---
title: Azure PowerShell skript-Azure Cosmos DB skapa SQL (Core) API-databas och behållare
description: Azure PowerShell skript-Azure Cosmos DB skapa SQL (Core) API-databas och behållare
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: 29b0e81dd6eac62a18307788bb023ac3d802f11a
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154856"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-core-api"></a>Skapa en databas och behållare för API: et för Azure Cosmos DB-SQL (Core)

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar ett Cosmos-konto för SQL-API i två regioner med konsekvens på sessionsläge, en databas med delat data flöde och en behållare med en partitionsnyckel, anpassad indexerings princip, unik nyckel princip, TTL, dedikerat data flöde och senaste skrivarens WINS konflikt lösnings princip med en anpassad matchnings Sök väg som ska användas `multipleWriteLocations=true`när.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL (Core) API")]

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
|**Azure-resurser**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Skapar en resurs. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../../../powershell-samples.md).
