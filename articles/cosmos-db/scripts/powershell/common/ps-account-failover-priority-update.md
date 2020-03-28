---
title: PowerShell-skript för att ändra redundansprioritet för ett Azure Cosmos-konto med en huvudhanterare
description: Azure PowerShell-skriptexempel - Ändra redundansprioritet eller utlösa redundans för ett Azure Cosmos DB-enmanskonto
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: a81938675e72d9ec3a18c920121951e38580b91e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366110"
---
# <a name="change-failover-priority-or-trigger-failover-for-an-azure-cosmos-db-single-master-account-using-powershell"></a>Ändra redundansprioritet eller utlösa redundans för ett Azure Cosmos DB-enhuvudkonto med PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

> [!NOTE]
> Alla ändringar i `failoverPriority=0` en region med utlöser en manuell redundans och kan endast göras på ett konto som konfigurerats för manuell redundans. Ändringar i alla andra regioner ändrar helt enkelt redundansprioriteten för ett Cosmos-konto.
> [!NOTE]
> Det här exemplet visas med hjälp av ett SQL-API-konto (Core). Om du vill använda det här exemplet för andra API:er kopierar du de relaterade egenskaperna och gäller för ditt API-specifika skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-failover-priority-update.ps1 "Update failover priority for an Azure Cosmos account or trigger a manual failover")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
|**Azure Cosmos DB**| |
| [Få-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Visar Cosmos DB-konton eller får ett angivet Cosmos DB-konto. |
| [Uppdatering-AzCosmosDBAccountFailoverPriority](https://docs.microsoft.com/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Uppdatera redundansprioritetsordningen för en Cosmos DB-kontos regioner. |
|**Azure-resursgrupper**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../../../powershell-samples.md).
