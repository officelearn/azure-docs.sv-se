---
title: Hantera Azure SQL Database med PowerShell | Microsoft Docs
description: Hantering av Azure SQL Database med PowerShell.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: adad6b8e27e0996559d5e6dacb8dd60fbf52a631
ms.openlocfilehash: 0c1ce1c29e447d9db4ef0df7873ef89cb835abee


---
# <a name="managing-azure-sql-database-using-powershell"></a>Hantera Azure SQL Database med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-manage-portal.md)
> * [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

Det här avsnittet visar PowerShell-cmdletar som används för att utföra många aktiviteter för Azure SQL Database. En fullständig lista finns i [Cmdlet:ar för Azure SQL Database](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx).

> [!TIP]
> En genomgång som visar dig hur du skapar en server, skapar en serverbaserad brandvägg, visar egenskaper, ansluter och frågar huvuddatabasen, skapar en exempeldatabas och en tom databas, frågar om databasegenskaper, ansluter till och frågar exempeldatabasen, finns i [Komma igång - Självstudier](sql-database-get-started-powershell.md).
>

## <a name="how-do-i-create-a-resource-group"></a>Hur skapar jag en resursgrupp?
Så här skapar du en resursgrupp för SQL-databasen och relaterade Azure-resurser i cmdlet:en [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Mer information finns i [Använda Azure PowerShell med Azure Resource Manager](../powershell-azure-resource-manager.md).
En fullständig genomgång finns i [Komma igång med Azure SQL Database-baserade servrar, databaser och brandväggsregler med hjälp av Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server"></a>Hur skapar jag en SQL Database-server?
Om du vill skapa en SQL Database-server använder du cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx). Ersätt *server1* med namnet på servern. Servernamnet måste vara unikt inom alla Azure SQL Database-servrar. Om servernamnet redan används får du ett felmeddelande. Det här kommandot kan ta flera minuter att slutföra. Resursgruppen måste redan finnas i din prenumeration.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Mer information om servrar finns i [SQL Database-funktioner](sql-database-features.md). En fullständig genomgång finns i [Komma igång med Azure SQL Database-baserade servrar, databaser och brandväggsregler med hjälp av Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>Hur skapar jag en brandväggsregel för SQL Database-server?
Om du vill skapa en brandväggsregel för att komma åt servern använder du cmdlet [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx). Kör följande kommando och ersätt start- och slut-IP-adresserna med giltiga värden för din klient. Resursgruppen och servern måste redan finnas i din prenumeration.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Skapa en brandväggsregel för att tillåta andra Azure-tjänster åtkomst till servern och ange både `-StartIpAddress` och `-EndIpAddress` till **0.0.0.0**. Den här särskilda brandväggsregel tillåter all Azure-trafik att ansluta till servern.

Mer information finns i [Azure SQL Database-brandvägg](https://msdn.microsoft.com/library/azure/ee621782.aspx). En fullständig genomgång finns i [Komma igång med Azure SQL Database-baserade servrar, databaser och brandväggsregler med hjälp av Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-create-a-sql-database"></a>Hur skapar jag en SQL Database?
Du kan skapa en SQL Database med cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx). Resursgruppen och servern måste redan finnas i din prenumeration. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Mer information finns i [Vad är SQL Database](sql-database-technical-overview.md). En fullständig genomgång finns i [Komma igång med Azure SQL Database-baserade servrar, databaser och brandväggsregler med hjälp av Azure PowerShell](sql-database-get-started-powershell.md).

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>Hur ändrar jag prestandanivån för en SQL Database?
Om du vill ändra prestandanivån, skalar du upp eller ned databasen med cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx). Resursgruppen, servern och databasen måste redan finnas i din prenumeration. Ange `-RequestedServiceObjectiveName` till ett enda blanksteg (som följande kodfragment) för basnivån. Ange den till *S0*, *S1*, *P1*, *P6*, o.s.v. som i föregående exempel för andra nivåer.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Mer information finns i [SQL Database, alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder](sql-database-service-tiers.md). Ett exempelskript finns i [Exempel på PowerShell-skript för att ändra tjänst- och prestandanivån för SQL Database](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>Hur kopierar jag en SQL-databas på samma server?
Om du vill kopiera en SQL-databas på samma server använder du cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx). Ange `-CopyServerName` och `-CopyResourceGroupName` till samma värden som din källdatabasserver och resursgrupp.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Mer information finns i [Kopiera en Azure SQL Database](sql-database-copy.md). Ett exempelskript finns i [Kopiera ett PowerShell-skript för SQL Database](sql-database-copy-powershell.md#example-powershell-script).

## <a name="how-do-i-delete-a-sql-database"></a>Hur tar jag bort en SQL Database?
Ta bort en SQL Database. Använd cmdlet [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx). Resursgruppen, servern och databasen måste redan finnas i din prenumeration.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>Hur tar jag bort en SQL Database-server?
Om du vill ta bort en SQL Database-server använder du cmdlet [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx).

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>Hur skapar och hanterar jag elastiska pooler med PowerShell?
Mer information om hur du skapar elastiska pooler med hjälp av PowerShell finns i [Skapa en ny elastisk pool med PowerShell](sql-database-elastic-pool-create-powershell.md).

Mer information om hur du hanterar elastiska pooler med hjälp av PowerShell finns i [Övervaka och hantera en elastisk pool med PowerShell](sql-database-elastic-pool-manage-powershell.md).

## <a name="related-information"></a>Relaterad information
* [Cmdlet:ar för Azure SQL Database](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure Cmdlet-referens](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Jan17_HO1-->


