<properties
    pageTitle="Ny SQL Database-installation med PowerShell | Microsoft Azure"
    description="Lär dig att skapa en SQL-databas med PowerShell. Vanliga aktiviteter för databasinstallationen kan hanteras via PowerShell-cmdletar."
    keywords="skapa ny sql-databas, databasinställningar"
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
    ms.date="08/19/2016"
    ms.author="sstein"/>


# Skapa en SQL-databas och utföra vanliga aktiviteter för databasinstallation med PowerShell-cmdletar


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Lär dig hur du skapar en SQL-databas genom att använda PowerShell-cmdletar. (För att skapa elastiska databaser, se [Skapa en ny elastisk databaspool med PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## Databasinstallation: Skapa en resursgrupp, server och brandväggsregel

När du har möjlighet att köra cmdletar mot din valda Azure-prenumeration är nästa steg att etablera den resursgrupp som innehåller servern där databasen ska skapas. Du kan redigera nästa kommando så det använder valfri giltig plats. Kör **(Get-AzureRmLocation | Where-Object {$_.Providers -eq "Microsoft.Sql"}).Location** för att hämta en lista över giltiga platser.

Kör följande kommando för att skapa en resursgrupp:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### Skapa en server

SQL-databaser skapas inuti Azure SQL Database-servrar. Kör **New-AzureRmSqlServer** för att skapa en server. Namnet på servern måste vara unikt för alla Azure SQL Database-servrar. Om servernamnet redan används får du ett felmeddelande. Det är också värt att nämna att det här kommandot kan ta flera minuter att slutföra. Du kan redigera kommandot för att använda valfri giltig plats, men du bör använda samma plats som du använde för resursgruppen som skapades i föregående steg.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

När du kör det här kommandot uppmanas du att ange användarnamn och lösenord. Ange inte dina autentiseringsuppgifter för Azure. Ange istället användarnamn och lösenord för serveradministratören. Skriptet längst ned i den här artikeln visar hur du ställer in autentiseringsuppgifterna för servern i kod.

Serverinformationen visas när servern har skapats.

### Konfigurera en brandväggsregel för servern som tillåter åtkomst

För att få åtkomst till servern måste du skapa en brandväggsregel. Kör följande kommando och ersätt start- och slut-IP-adresserna med giltiga värden för din dator.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Regeldetaljerna för brandväggen visas efter att regeln har skapats.

Lägg till en brandväggsregel och ställ in både StartIpAddress och EndIpAddress till 0.0.0.0 för att låta andra Azure-tjänster ha åtkomst till servern. Det här gör att Azure-trafik från alla Azure-prenumerationer har åtkomst till servern.

Mer information finns i [Azure SQL Database-brandvägg](sql-database-firewall-configure.md).


## Skapa en SQL-databas

Nu har du en resursgrupp, en server och en brandväggsregel som har konfigurerats så att du har åtkomst till servern.

Följande kommando skapar en (tom) SQL-databas på standard-tjänstnivå och på S1-prestandanivå:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Databasinformationen visas efter att databasen har skapats.

## Skapa ett PowerShell-skript för SQL-databasen

Följande PowerShell-skript skapar en SQL-databas och alla beroende resurser. Ersätt alla `{variables}` med värden som är specifika för din prenumeration och dina resurser (ta bort **{}** när du anger värden).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## Nästa steg
När du har skapat SQL-databasen och utfört grundläggande installationsåtgärder på databasen är du redo för följande:

- [Hantera SQL Database med PowerShell](sql-database-command-line-tools.md)
- [Anslut till SQL Database med SQL Server Management Studio och kör en exempelfråga i T-SQL](sql-database-connect-query-ssms.md)


## Ytterligare resurser

- [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)



<!--HONumber=Sep16_HO3-->


