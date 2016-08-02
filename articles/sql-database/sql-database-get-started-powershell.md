<properties 
    pageTitle="Ny SQL Database-installation med PowerShell | Microsoft Azure" 
    description="Lär dig att skapa en ny SQL-databas med PowerShell. Vanliga aktiviteter för databasinstallationen kan hanteras via PowerShell-cmdletar." 
    keywords="create new sql database,database setup"
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="05/09/2016"
    ms.author="sstein"/>

# Skapa en ny SQL-databas och utför vanliga aktiviteter för databasinstallation med PowerShell-cmdletar 


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Lär dig hur du skapar en ny SQL-databas med PowerShell-cmdletar. (För att skapa elastiska databaser, se [Skapa en ny elastisk databaspool med PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## Databasinstallation: Skapa en resursgrupp, server och brandväggsregel

Du har nu åtkomst att köra cmdletar mot din valda Azure-prenumeration så nästa steg går ut på att etablera resursgrupperna som innehåller servern där databasen ska skapas. Du kan redigera nästa kommando så det använder valfri giltig plats. Kör **(Get-AzureRmLocation | Where-Object {$_.Providers -eq "Microsoft.Sql"}).Location** för att hämta en lista över giltiga platser.

Kör följande kommando för att skapa en ny resursgrupp:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

När du har skapat den nya resursgruppen, kommer du att se information på skärmen som inkluderar **ProvisioningState : lyckades**.


### Skapa en server 

SQL-databaser skapas inuti Azure SQL Database-servrar. Kör **New-AzureRmSqlServer** för att skapa en ny server. Ersätt ServerName med namnet på din server. Det måste vara unikt för alla Azure SQL-servrar så du får ett felmeddelande om servernamnet redan är upptaget. Det är också värt att nämna att det här kommandot kan ta flera minuter att slutföra. Du kan redigera kommandot för att använda valfri giltig plats som du väljer, men du bör använda på samma plats som du använde för resursgruppen som skapades i föregående steg.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

När du kör det här kommandot, kommer ett fönster upp som ber om **Användarnamn** och **Lösenord**. Det handlar inte om dina autentiseringsuppgifter för Azure, fyll i det användarnamn och lösenord som administratören för den nya servern ska ha.

Serverinformationen visas när servern har skapats.

### Konfigurera en brandväggsregel för servern som tillåter åtkomst

Etablera en brandväggsregel för åtkomst till servern. Kör följande kommando och ersätt start- och slut-IP-adresserna med giltiga värden för din dator.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Regeldetaljerna för brandväggen visas efter att regeln har skapats.

Lägg till en brandväggsregel och ställ in både StartIpAddress och EndIpAddress till 0.0.0.0 för att låta andra Azure-tjänster ha åtkomst till servern. Observera att det här gör att Azure-trafik från alla Azure-prenumerationer har åtkomst till servern.

Mer information finns i [Azure SQL Database-brandvägg](sql-database-firewall-configure.md).


## Skapa en ny SQL-databas

Nu har du en resursgrupp, en server och en brandväggsregel som har konfigurerats så att du har åtkomst till servern.

Följande kommando skapar en ny (tom) SQL-databas på standard-tjänstnivå och på S1-prestandanivå:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Databasinformationen visas efter att databasen har skapats.

## Skapa ett nytt PowerShell-skript för SQL-databasen

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Nästa steg
Efter att du skapat den nya SQL-databasen och utfört grundläggande installationsåtgärder på databasen, är du redo för följande:

- [Anslut till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL](sql-database-connect-query-ssms.md)


## Ytterligare resurser

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Jun16_HO2-->


