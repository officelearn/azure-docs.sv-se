---
title: Skript för Azure PowerShell – Replikering i flera regioner för Azure Cosmos DB
description: Skriptexempel för Azure PowerShell – Replikering i flera regioner för Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: rockboyfor
ms.author: v-yeche
ms.devlang: PowerShell
ms.topic: sample
origin.date: 05/10/2017
ms.date: 04/15/2019
ms.reviewer: sngun
ms.openlocfilehash: 02628401bed6e65784bf7ddc4a7082f617640cf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60448477"
---
# <a name="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-powershell"></a>Replikera ett Azure Cosmos DB-databaskonto i flera regioner och konfigurera redundansprioriteringar med hjälp av PowerShell

Det här exemplet replikerar alla typer av Azure Cosmos DB-databaskonton i flera regioner och konfigurerar redundansprioriteringar med hjälp av PowerShell. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

<!--First make chinaeast2 as wirte region-->
<!--Second make chinanorth as write region-->
<!--Last add chinanorth2 new region-->

```powershell
# Set the Azure resource group name and location
$resourceGroupName = "myResourceGroup"
$resourceGroupLocation = "chinaeast2"

# Database name
$DBName = "testdb"
# Distribution locations
$locations = @(@{"locationName"="chinaeast"; 
                 "failoverPriority"=2},
               @{"locationName"="chinanorth"; 
                 "failoverPriority"=1},
               @{"locationName"="chinaeast2"; 
                 "failoverPriority"=0})


# Create the resource group
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation

# Consistency policy
$consistencyPolicy = @{"maxIntervalInSeconds"="10"; 
                       "maxStalenessPrefix"="200"}

# DB properties
$DBProperties = @{"databaseAccountOfferType"="Standard";
                  "Kind"="GlobalDocumentDB"; 
                  "locations"=$locations; 
                  "consistencyPolicy"=$consistencyPolicy;}

# Create the database
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
                    -ApiVersion "2015-04-08" `
                    -ResourceGroupName $resourceGroupName `
                    -Location $resourceGroupLocation `
                    -Name $DBName `
                    -PropertyObject $DBProperties

# Update failoverpolicy to make China North as a write region
$NewfailoverPolicies = @(@{"locationName"="chinanorth"; "failoverPriority"=0}, @{"locationName"="chinaeast2"; "failoverPriority"=1}, @{"locationName"="chinaeast"; "failoverPriority"=2} )

Invoke-AzResourceAction `
    -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName `
    -Name $DBName `
    -Parameters @{"failoverPolicies"=$NewfailoverPolicies}

# Add a new locations with priorities
$newLocations = @(@{"locationName"="chinanorth"; 
                 "failoverPriority"=0},
               @{"locationName"="chinaeast"; 
                 "failoverPriority"=1},
               @{"locationName"="chinanorth2"; 
                 "failoverPriority"=2},
               @{"locationName"="chinaeast2";
                 "failoverPriority"=3})

# Updated properties
$updateDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$newLocations;}

# Update the database with the properties
Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName `
    -Name $DBName `
    -PropertyObject $UpdateDBProperties

```

## <a name="clean-up-deployment"></a>Rensa distribution

När exempelskriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Skapar en logisk server som är värd för en databas eller elastisk pool. |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Ändrar databaskontot. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../powershell-samples.md).

<!-- Update_Description: update meta properties -->