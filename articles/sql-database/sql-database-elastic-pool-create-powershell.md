---
title: Skapa en ny elastisk databaspool med PowerShell | Microsoft Docs
description: "Lär dig hur du använder PowerShell för att skala ut Azure SQL Database-resurser genom att skapa en skalbar elastisk databaspool för att hantera flera databaser."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 37a707ee-9223-43ae-8c35-1ccafde8b83e
ms.service: sql-database
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: daf8bd6421ae563e542b0874a6e7748a3ca52738


---
# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Skapa en ny elastisk databaspool med PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
> 
> 

Lär dig hur du skapar en [elastisk databaspool](sql-database-elastic-pool.md) med PowerShell-cmdletar. 

Vanliga felkoder finns i [SQL-felkoder för SQL Database-klientprogram: anslutningsfel för databasen och andra problem](sql-database-develop-error-messages.md).

> [!NOTE]
> Elastiska pooler är allmänt tillgänglig (GA) i alla Azure-regioner utom Norra centrala USA och Västra Indien där de genomgår förhandsgranskning.  Allmän tillgänglighet för elastiska pooler i dessa regioner kommer att erbjudas så snart som möjligt. För närvarande stöder inte elastiska pooler databaser med [minnesintern OLTP eller minnesintern analys](sql-database-in-memory.md).
> 
> 

Du måste köra Azure PowerShell 1.0 eller högre. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Skapa en ny pool
Cmdleten [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) skapar en ny pool. Värdena för eDTU per pool, min och max Dtu:er, är begränsade av tjänstnivåvärdet (basic, standard eller premium). Se [eDTU och lagringsgränser för elastiska pooler och elastiska databaser](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Skapa en ny elastisk databas i en pool
Använd cmdleten [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) och ange paramtern **ElasticPoolName** till målpoolen. För att flytta en befintlig databas till en pool, se [Flytta en databas till en elastisk pool](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Skapa en pool och fyll den med flera nya databaser
Det kan ta tid att skapa ett stort antal databaser i en pool när det görs med portalen eller PowerShell-cmdletar som bara skapar en databas åt gången. För att automatisera skapande i en ny pool, se [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Exempel: skapa en pool med PowerShell
Det här skriptet skapar en ny Azure-resursgrupp och en ny server. När du uppmanas, anger du ett användarnamn och lösenord för administratören för den nya servern (inte dina Azure-autentiseringsuppgifter).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Nästa steg
* [Hantera din pool](sql-database-elastic-pool-manage-powershell.md)
* [Skapa elastiska jobb](sql-database-elastic-jobs-overview.md): Elastiska jobb låter dig köra T-SQL-skript mot valfritt antal databaser i poolen.
* [Skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md): Använd elastiska databasverktyg för att skala ut.




<!--HONumber=Nov16_HO2-->


