---
title: 'Självstudiekurs: Lägga till en elastisk pool i en redundansgrupp'
description: Lägg till en elastisk Azure SQL Database-pool i en redundansgrupp med Azure-portalen, PowerShell eller Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268982"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Självstudiekurs: Lägga till en elastisk Azure SQL Database-pool i en redundansgrupp

Konfigurera en redundanskitetsgrupp för en elastisk Azure SQL Database-pool och testa redundans med Azure-portalen.  I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> - Skapa en enda Azure SQL-databas.
> - Lägg till den enda databasen i en elastisk pool. 
> - Skapa en [redundanskitetsgrupp](sql-database-auto-failover-group.md) för två elastiska pooler mellan två logiska SQL-servrar.
> - Testa redundans.

## <a name="prerequisites"></a>Krav

För att kunna följa den här självstudien måste du ha: 

- En Azure-prenumeration. [Skapa ett gratis konto](https://azure.microsoft.com/free/) om du inte redan har ett.


## <a name="1---create-a-single-database"></a>1 - Skapa en enda databas 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Lägg till en databas i elastisk pool
I det här steget ska du skapa en elastisk pool och lägga till din enda databas i den. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa din elastiska pool med Azure-portalen. 


1. Välj **Azure SQL** i menyn till vänster i Azure-portalen. Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Välj **+ Lägg till** om du vill öppna **alternativsidan Välj SQL-distribution.** Du kan visa ytterligare information om de olika databaserna genom att välja Visa information på panelen Databaser.
1. Välj **Elastisk pool** i listrutan **Resurstyp** på panelen **SQL-databaser.** Välj **Skapa** om du vill skapa din elastiska pool. 

    ![Välj elastisk pool](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Konfigurera din elastiska pool med följande värden:
   - **Namn**: Ange ett unikt namn för `myElasticPool`den elastiska poolen, till exempel . 
   - **Prenumeration**: Välj din prenumeration i listrutan.
   - **ResourceGroup**: `myResourceGroup` Välj i listrutan, den resursgrupp som du skapade i avsnitt 1. 
   - **Server:** Välj den server som du skapade i avsnitt 1 i listrutan.  

       ![Skapa ny server för elastisk pool](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Beräkning + lagring:** Välj **Konfigurera elastisk pool** för att konfigurera din beräkning, lagring och lägga till din enda databas i din elastiska pool. På fliken **Poolinställningar** lämnar du standardvärdet Gen5 med 2 virtuella kärnor och 32 gb. 

1. På sidan **Konfigurera** markerar du fliken **Databaser** och väljer sedan att **lägga till databas**. Välj den databas som du skapade i avsnitt 1 och välj sedan **Använd** för att lägga till den i den elastiska poolen. Välj **Använd** igen om du vill använda inställningarna för den elastiska poolen och stäng sidan **Konfigurera.** 

    ![Lägga till SQL DB i elastisk pool](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Välj **Granska + skapa** om du vill granska inställningarna för elastiska pooler och välj sedan **Skapa** för att skapa din elastiska pool. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Skapa dina elastiska pooler och sekundära server med PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Ny-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk databaspool för en Azure SQL-databas.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. | 

---

## <a name="3---create-the-failover-group"></a>3 - Skapa redundansgruppen 
I det här steget skapar du en [redundansgrupp](sql-database-auto-failover-group.md) mellan en befintlig Azure SQL-server och en ny Azure SQL-server i en annan region. Lägg sedan till den elastiska poolen i redundansgruppen. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa din redundansk grupp med Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den elastiska pool som skapats i föregående avsnitt, till exempel `myElasticPool`. 
1. Öppna inställningarna för servern i fönstret **Server name** **Översikt.**
  
    ![Öppen server för elastisk pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan Lägg **till grupp** för att skapa en ny redundanskitetsgrupp. 

    ![Lägga till ny redundansgrupp](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. På sidan **Redundansk gruppera** du eller markera följande värden och välj sedan **Skapa:**
    - **Redundansgruppnamn**: Skriv in ett unikt `failovergrouptutorial`redundansktgruppnamn, till exempel . 
    - **Sekundär server:** Välj alternativet för att *konfigurera nödvändiga inställningar* och välj sedan att skapa en ny **server**. Du kan också välja en redan befintlig server som sekundär server. När du har angett följande värden för den nya sekundära servern väljer du **Välj**. 
        - **Servernamn**: Skriv in ett unikt namn `mysqlsecondary`för den sekundära servern, till exempel . 
        - **Server admin inloggning:** Typ`azureuser`
        - **Lösenord**: Skriv ett komplext lösenord som uppfyller lösenordskraven.
        - **Plats**: Välj en plats i listrutan, till exempel `East US`. Den här platsen kan inte vara samma plats som den primära servern.

       > [!NOTE]
       > Serverinloggnings- och brandväggsinställningarna måste matcha den primära serverns. 
    
       ![Skapa en sekundär server för redundansgruppen](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Välj **Databaser i gruppen** och välj sedan den elastiska pool som du skapade i avsnitt 2. En varning bör visas som uppmanar dig att skapa en elastisk pool på den sekundära servern. Välj varningen och välj sedan **OK** för att skapa den elastiska poolen på den sekundära servern. 
        
    ![Lägga till elastisk pool i redundansgruppen](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Välj **Välj om** du vill använda inställningarna för den elastiska poolen på redundansgruppen och välj sedan **Skapa** för att skapa redundansgruppen. Om du lägger till den elastiska poolen i redundansgruppen startas georeplikeringsprocessen automatiskt.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Skapa redundansgruppen med PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en logisk server. | 
| [Ny-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk databaspool för en Azure SQL-databas.| 
| [Nya-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny redundansgrupp. |
| [Tillägg-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL-databaser i en redundansgrupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Redundansgrupper för Azure SQL Database. |

---


## <a name="4---test-failover"></a>4 - Testa redundans 
I det här steget kommer du att växla över redundansgruppen till den sekundära servern och sedan växla tillbaka med Azure-portalen. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för redundansgruppen med Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den elastiska pool som skapats i föregående avsnitt, till exempel `myElasticPool`. 
1. Välj namnet på servern under **Servernamn** för att öppna inställningarna för servern.

    ![Öppen server för elastisk pool](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan den redundansgrupp som du skapade i avsnitt 2. 
  
   ![Välj redundansgruppen från portalen](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär. 
1. Välj **Redundans** i åtgärdsfönstret om du vill växla över redundansgruppen som innehåller den elastiska poolen. 
1. Välj **Ja** på varningen som meddelar dig att TDS-sessioner kommer att kopplas från. 

   ![Växla över redundansgruppen som innehåller SQL-databasen](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Granska vilken server som är primär, vilken server som är sekundär. Om redundansen lyckades bör de två servrarna ha bytt roller. 
1. Välj **Redundans** igen om du vill återställa redundansgruppen till de ursprungliga inställningarna igen. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Testa redundans för redundansgruppen med PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Redundansgruppen redundans till den sekundära servern och växla sedan tillbaka med PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Redundansgrupper för Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundans för en redundansgrupp för Azure SQL Database. |


---

## <a name="clean-up-resources"></a>Rensa resurser 

Rensa resurser genom att ta bort resursgruppen. 


# <a name="portal"></a>[Portal](#tab/azure-portal)


1. Navigera till din resursgrupp i [Azure-portalen](https://portal.azure.com).
1. Välj **Ta bort resursgrupp** om du vill ta bort alla resurser i gruppen och själva resursgruppen. 
1. Skriv namnet på resursgruppen, `myResourceGroup`i textrutan och välj sedan Ta **bort** om du vill ta bort resursgruppen. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Rensa dina resurser med PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Den här delen av självstudien använder följande PowerShell-cmdlet:

| Kommando | Anteckningar |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp | 

---

> [!IMPORTANT]
> Om du vill behålla resursgruppen men ta bort den sekundära databasen tar du bort den från redundansgruppen innan du tar bort den. Om du tar bort en sekundär databas innan den tas bort från redundansgruppen kan det orsaka oförutsägbart beteende. 

## <a name="full-script"></a>Fullständigt skript

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en logisk server. | 
| [Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en ny enda Azure SQL Database-databas. | 
| [Ny-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk databaspool för en Azure SQL-databas.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. | 
| [Nya-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny redundansgrupp. |
| [Hämta AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hämtar en eller flera SQL-databaser. |
| [Tillägg-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL-databaser i en redundansgrupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Redundansgrupper för Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundans för en redundansgrupp för Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp | 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Det finns inga skript tillgängliga för Azure-portalen.

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en elastisk Azure SQL Database-pool i en redundansgrupp och testade redundans. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa en enda Azure SQL-databas.
> - Lägg till den enda databasen i en elastisk pool. 
> - Skapa en [redundanskitetsgrupp](sql-database-auto-failover-group.md) för två elastiska pooler mellan två logiska SQL-servrar.
> - Testa redundans.

Gå vidare till nästa handledning om hur du migrerar med DMS.

> [!div class="nextstepaction"]
> [Självstudiekurs: Migrera SQL Server till en poolad databas med DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
