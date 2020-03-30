---
title: 'Självstudiekurs: Lägga till en enskild databas i en redundansgrupp'
description: Lägg till en enda Azure SQL-databas i en redundansgrupp med Azure-portalen, PowerShell eller Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061697"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Självstudiekurs: Lägga till en en enda Azure SQL Database-databas i en redundansgrupp

En [redundansk grupp](sql-database-auto-failover-group.md) är ett deklarativt abstraktionslager som gör att du kan gruppera mulitple geo-replikerade databaser. Lär dig att konfigurera en redundansgrupp för en enda Azure SQL-databas och testa redundans med azure-portalen, PowerShell eller Azure CLI.  I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> - Skapa en enda Azure SQL-databas.
> - Skapa en redundansk grupp för en enskild databas mellan två logiska SQL-servrar.
> - Testa redundans.

## <a name="prerequisites"></a>Krav

# <a name="portal"></a>[Portal](#tab/azure-portal)
För att kunna följa den här självstudien måste du ha: 

- En Azure-prenumeration. [Skapa ett gratis konto](https://azure.microsoft.com/free/) om du inte redan har ett.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
För att slutföra självstudien, se till att du har följande objekt:

- En Azure-prenumeration. [Skapa ett gratis konto](https://azure.microsoft.com/free/) om du inte redan har ett.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
För att slutföra självstudien, se till att du har följande objekt:

- En Azure-prenumeration. [Skapa ett gratis konto](https://azure.microsoft.com/free/) om du inte redan har ett.
- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Skapa en enda databas 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Skapa redundansgruppen 
I det här steget skapar du en [redundansgrupp](sql-database-auto-failover-group.md) mellan en befintlig Azure SQL-server och en ny Azure SQL-server i en annan region. Lägg sedan till exempeldatabasen i redundansgruppen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Skapa din redundansgrupp och lägg till din enda databas i den med Hjälp av Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den enda databas som skapats `mySampleDatabase`i avsnitt 1, till exempel . 
1. Redundansgrupper kan konfigureras på servernivå. Välj namnet på servern under **Servernamn** för att öppna inställningarna för servern.

   ![Öppna server för en enda db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan Lägg **till grupp** för att skapa en ny redundanskitetsgrupp. 

    ![Lägga till ny redundansgrupp](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. På sidan **Redundansk gruppera** du eller markera följande värden och välj sedan **Skapa:**
    - **Redundansgruppnamn**: Skriv in ett unikt `failovergrouptutorial`redundansktgruppnamn, till exempel . 
    - **Sekundär server:** Välj alternativet för att *konfigurera nödvändiga inställningar* och välj sedan att skapa en ny **server**. Du kan också välja en redan befintlig server som sekundär server. När du har angett följande värden väljer du **Välj**. 
        - **Servernamn**: Skriv in ett unikt namn `mysqlsecondary`för den sekundära servern, till exempel . 
        - **Server admin inloggning:** Typ`azureuser`
        - **Lösenord**: Skriv ett komplext lösenord som uppfyller lösenordskraven.
        - **Plats**: Välj en plats i listrutan, till exempel `East US`. Den här platsen kan inte vara samma plats som den primära servern.

    > [!NOTE]
    > Serverinloggnings- och brandväggsinställningarna måste matcha den primära serverns. 
    
      ![Skapa en sekundär server för redundansgruppen](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Databaser i gruppen**: När en sekundär server har valts låses det här alternativet upp. Markera den för att **markera databaser som ska läggas till** och välj sedan den databas som du skapade i avsnitt 1. Om du lägger till databasen i redundansgruppen startas georeplikeringsprocessen automatiskt. 
        
    ![Lägga till SQL DB i redundansgruppen](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Skapa redundansgruppen och lägg till din enda databas med PowerShell. 

   > [!NOTE]
   > Serverinloggnings- och brandväggsinställningarna måste matcha den primära serverns. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en logisk server. | 
| [Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en ny enda Azure SQL Database-databas. | 
| [Nya-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny redundansgrupp. |
| [Hämta AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hämtar en eller flera SQL-databaser. |
| [Tillägg-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL-databaser i en redundansgrupp. |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Skapa din redundansk grupp och lägg till din enda databas i den med AZ CLI. 

   > [!NOTE]
   > Serverinloggnings- och brandväggsinställningarna måste matcha den primära serverns. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Den här delen av självstudien använder följande Az CLI-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [az sql server brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule) | Skapar en servers brandväggsregler. | 
| [az sql failover-grupp skapa](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Skapar en redundansgrupp. | 

---

## <a name="3---test-failover"></a>3 - Testa redundans 
I det här steget kommer du att växla över redundansgruppen till den sekundära servern och sedan växla tillbaka med Azure-portalen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Testa redundans med Azure-portalen. 

1. Välj **Azure SQL** i menyn till vänster i [Azure-portalen](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du Alla **tjänster**och skriver sedan Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid **Azure SQL** för att göra den favorit och lägg till den som ett objekt i vänsternavigering. 
1. Markera den enda databas som skapats `mySampleDatbase`i avsnitt 2, till exempel . 
1. Välj namnet på servern under **Servernamn** för att öppna inställningarna för servern.

   ![Öppna server för en enda db](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Välj **Redundansk grupper** under fönstret **Inställningar** och välj sedan den redundansgrupp som du skapade i avsnitt 2. 
  
   ![Välj redundansgruppen från portalen](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär. 
1. Välj **Redundans** i åtgärdsfönstret om du vill redundansdrigeringsgruppen som innehåller exempeldatabasen. 
1. Välj **Ja** på varningen som meddelar dig att TDS-sessioner kommer att kopplas från. 

   ![Växla över redundansgruppen som innehåller SQL-databasen](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Granska vilken server som nu är primär och vilken server som är sekundär. Om fail over lyckades, bör de två servrarna ha bytt roller. 
1. Välj **Redundans** igen om du vill att servrarna inte ska kunna återgå till sina ursprungliga roller. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Testa redundans med PowerShell. 


Kontrollera rollen för den sekundära repliken: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Växla över till den sekundära servern: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Återgår till redundansgruppen till den primära servern:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Redundansgrupper för Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundans för en redundansgrupp för Azure SQL Database. |



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Testa redundans med AZ CLI. 

Kontrollera vilken server som är den sekundära:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Växla över till den sekundära servern: 

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Återgår till redundansgruppen till den primära servern:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Den här delen av självstudien använder följande Az CLI-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [lista över redundansgrupp i AZ SQL](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Visar en lista över redundansgrupper på en server. |
| [az sql failover-grupp set-primär](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ange den primära för redundansgruppen genom att växla över alla databaser från den aktuella primära servern. | 

---

## <a name="clean-up-resources"></a>Rensa resurser 
Rensa resurser genom att ta bort resursgruppen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Ta bort resursgruppen med Azure-portalen. 

1. Navigera till din resursgrupp i [Azure-portalen](https://portal.azure.com).
1. Välj **Ta bort resursgrupp** om du vill ta bort alla resurser i gruppen och själva resursgruppen. 
1. Skriv namnet på resursgruppen, `myResourceGroup`i textrutan och välj sedan Ta **bort** om du vill ta bort resursgruppen.  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ta bort resursgruppen med PowerShell. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Den här delen av självstudien använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ta bort resursgruppen med hjälp av AZ CLI. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Den här delen av självstudien använder följande Az CLI-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

---


> [!IMPORTANT]
> Om du vill behålla resursgruppen men ta bort den sekundära databasen tar du bort den från redundansgruppen innan du tar bort den. Om du tar bort en sekundär databas innan den tas bort från redundansgruppen kan det orsaka oförutsägbart beteende. 


## <a name="full-scripts"></a>Fullständiga skript

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en logisk server. | 
| [Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en ny enda Azure SQL Database-databas. | 
| [Nya-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny redundansgrupp. |
| [Hämta AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hämtar en eller flera SQL-databaser. |
| [Tillägg-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL-databaser i en redundansgrupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Redundansgrupper för Azure SQL Database. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundans för en redundansgrupp för Azure SQL Database. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az-kontouppsättning](/cli/azure/account?view=azure-cli-latest#az-account-set) | Anger att en prenumeration ska vara den aktuella aktiva prenumerationen. | 
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [az sql server brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule) | Skapar en servers brandväggsregler. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Skapar en databas. | 
| [az sql failover-grupp skapa](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Skapar en redundansgrupp. | 
| [lista över redundansgrupp i AZ SQL](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Visar en lista över redundansgrupper på en server. |
| [az sql failover-grupp set-primär](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ange den primära för redundansgruppen genom att växla över alla databaser från den aktuella primära servern. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

# <a name="portal"></a>[Portal](#tab/azure-portal)
Det finns inga skript tillgängliga för Azure-portalen. 
 
---

Du hittar andra Azure SQL Database-skript här: [Azure PowerShell](sql-database-powershell-samples.md) och [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en enda Azure SQL Database-databas i en redundansgrupp och testade redundans. Du har lärt dig att: 

> [!div class="checklist"]
> - Skapa en enda Azure SQL-databas. 
> - Skapa en [redundansk grupp](sql-database-auto-failover-group.md) för en enskild databas mellan två logiska SQL-servrar.
> - Testa redundans.

Gå vidare till nästa självstudiekurs om hur du lägger till din elastiska pool i en redundansgrupp. 

> [!div class="nextstepaction"]
> [Självstudiekurs: Lägga till en elastisk Azure SQL Database-pool i en redundansgrupp](sql-database-elastic-pool-failover-group-tutorial.md)
