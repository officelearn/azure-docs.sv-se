---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 02/14/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: d800d273cce995c618422a3a9d0934b2657e6ef5
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194314"
---
I det här steget ska du skapa en Azure SQL Database enskild databas. 

> [!IMPORTANT]
> Se till att konfigurera brand Väggs regler för användning av den offentliga IP-adressen för den dator som du använder för att slutföra den här artikeln.
>
> Mer information finns i [skapa en brand Väggs regel på databas nivå](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) eller för att fastställa vilken IP-adress som används för brand Väggs regeln på server nivå för datorn se [skapa en brand vägg på server nivå](../sql-database-server-level-firewall-rule.md).  

# <a name="portal"></a>[Portalen](#tab/azure-portal)

Skapa en resurs grupp och en enkel databas med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster**och skriver sedan *Azure SQL* i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster. 
2. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa ytterligare information om de olika databaserna genom att välja **Visa information** på panelen **databaser** .
3. Välj **skapa**:

   ![Skapa en enkel databas](../media/sql-database-get-started-portal/create-single-database.png)

4. På fliken **grundläggande** i avsnittet **projekt information** skriver eller väljer du följande värden:

   - **Prenumeration**: list rutan och välj rätt prenumeration om den inte visas.
   - **Resurs grupp**: Välj **Skapa ny**, skriv `myResourceGroup`och välj **OK**.

     ![Ny SQL-databas – fliken grundläggande](../media/sql-database-get-started-portal/new-sql-database-basics.png)

5. I avsnittet **databas information** skriver eller väljer du följande värden:

   - **Databas namn**: Ange `mySampleDatabase`.
   - **Server**: Välj **Skapa ny**, ange följande värden och välj sedan **Välj**.
       - **Server namn**: typ `mysqlserver`; tillsammans med en del siffror för unikhet.
       - **Inloggning för Server administratör**: Skriv `azureuser`.
       - **Lösen ord**: Ange ett komplext lösen ord som uppfyller lösen ords kraven.
       - **Plats**: Välj en plats i list rutan, till exempel `West US`.

         ![Ny server](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Skriv ned serveradministratörens inloggning och lösenord så att du kan logga in på servern och databaserna till denna och andra snabbstarter. Om du glömmer din inloggning eller ditt lösenord kan du hämta inloggningsnamnet eller återställa lösenordet på **SQLServer**-sidan. För att öppna **SQLServer**-sidan väljer du servernamnet i databasens **översiktssida** när databasen har skapats.

   - **Vill du använda elastisk SQL-pool**: Välj alternativet **Nej** .
   - **Compute + Storage**: Välj **Konfigurera databas**. 

     ![SQL Database information](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Välj **etablerad**.  Du kan också välja **Server** lös för att skapa en databas utan server.

     ![Etablerade Gen4](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Granska inställningarna för **virtuella kärnor**och **data Max storlek**. Ändra dem efter behov. 
     - Alternativt kan du också välja **ändra konfiguration** för att ändra maskin varu genereringen.
   - Välj **Använd**.

6. Välj fliken **nätverk** och bestäm om du vill tillåta att [**Azure-tjänster och-resurser får åtkomst till den här servern**](../sql-database-networkaccess-overview.md)eller lägga till en [privat slut punkt](../../private-link/private-endpoint-overview.md).

   ![Fliken nätverk](../media/sql-database-get-started-portal/create-database-networking.png)

7. Välj fliken **ytterligare inställningar** . 
8. I avsnittet **data källa** , under **Använd befintliga data**, väljer du `Sample`.

   ![Ytterligare SQL DB-inställningar](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Se till att välja data för **Sample (AdventureWorksLT)** så att du enkelt kan följa den här och andra snabbstarter för Azure SQL-databas som använder dessa data.

9. Lämna resten av värdena som standard och välj **Granska + skapa** längst ned i formuläret.
10. Granska de slutgiltiga inställningarna och välj **skapa**.

11. I formuläret **SQL Database** väljer du **Skapa** för att distribuera och etablera resursgruppen, servern och databasen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Skapa en resurs grupp och en enkel databas med PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

Den här delen av artikeln använder följande PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brand Väggs regel för en logisk server. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en ny Azure SQL Database enskild databas. | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Skapa en resurs grupp och en enkel databas med AZ CLI.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $subscription = "<subscriptionID>"
   $randomIdentifier = $(Get-Random)

   $resourceGroup = "resource-$randomIdentifier"
   $location = "East US"
   
   $login = "sampleLogin"
   $password = "samplePassword123!"

   $server = "server-$randomIdentifier"
   $database = "database-$randomIdentifier"
  
   az login # connect to Azure
   az account set -s $subscription # set subscription context for the Azure account

   echo "Creating resource group..."
   az group create --name $resourceGroup --location $location

   echo "Creating primary logical server..."
   az sql server create --name $server --resource-group $resourceGroup --location $location --admin-user $login --admin-password $password

   echo "Creating a gen5 2 vCore database..."
   az sql db create --resource-group $resourceGroup --server $server --name $database --sample-name AdventureWorksLT --edition GeneralPurpose --family Gen5 --capacity 2
   ```

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [AZ-konto uppsättning](/cli/azure/account?view=azure-cli-latest#az-account-set) | Anger att en prenumeration är den aktuella aktiva prenumerationen. | 
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [AZ SQL Server-brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule) | Skapar en servers brand Väggs regler. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Skapar en databas. | 


---
