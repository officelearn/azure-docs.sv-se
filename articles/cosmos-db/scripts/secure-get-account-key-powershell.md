---
title: Azure PowerShell-skript – Hämta kontonycklar för Azure Cosmos DB
description: Exempel på Azure PowerShell-skript – Hämta kontonycklar för Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: rockboyfor
ms.author: v-yeche
ms.devlang: PowerShell
ms.topic: sample
origin.date: 05/10/2017
ms.date: 04/15/2019
ms.reviewer: sngun
ms.openlocfilehash: 088b09e6d97bcf0dc62a96754d2acd0e611865c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60446167"
---
# <a name="get-account-keys-for-azure-cosmos-db-using-powershell"></a>Hämta kontonycklar för Azure Cosmos DB med PowerShell

Det här exemplet hämtar kontonycklar för alla typer av Azure Cosmos DB-konton.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

```powershell
# Set the Azure resource group name and location
$resourceGroupName = "myResourceGroup"
$resourceGroupLocation = "chinanorth"

# Create the resource group
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation

# Database name
$DBName = "testdb"

# Write and read locations and priorities for the database
$locations = @(@{"locationName"="chinanorth"; 
                 "failoverPriority"=0}, 
               @{"locationName"="chinaeast"; 
                  "failoverPriority"=1})

# Consistency policy
$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; 
                       "maxIntervalInSeconds"="10"; 
                       "maxStalenessPrefix"="200"}

# DB properties
$DBProperties = @{"databaseAccountOfferType"="Standard"; 
                          "locations"=$locations; 
                          "consistencyPolicy"=$consistencyPolicy;}

# Create the database
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
                    -ApiVersion "2015-04-08" `
                    -ResourceGroupName $resourceGroupName `
                    -Location $resourceGroupLocation `
                    -Name $DBName `
                    -PropertyObject $DBProperties

# Retrieve the primary and secondary account keys
Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName `
    -Name $DBName

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
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Anropar en åtgärd på Azure CosmosDB-kontot. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../powershell-samples.md).

<!-- Update_Description: update meta properties -->