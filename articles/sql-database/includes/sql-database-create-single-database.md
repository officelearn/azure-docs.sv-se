---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: mathoma
ms.reviewer: vanto
ms.openlocfilehash: 563ac8748e9d4f8a254a151814491113b9c816e6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685142"
---
I det här steget skapar du en Azure SQL Database-server och en enda databas som använder AdventureWorksLT-exempeldata. Du kan skapa databasen med hjälp av Azure portal menyer och skärmar, eller med hjälp av ett Azure CLI eller PowerShell-skript i Azure Cloud Shell.

Alla metoder är att ställa in en brandväggsregel på servernivå så att den offentliga IP-adressen för den dator som du använder för att komma åt servern. Mer information om hur du skapar serverbrandväggsregler finns i [Skapa en brandvägg på servernivå](../sql-database-server-level-firewall-rule.md). Du kan också ange brandväggsregler på databasnivå. Se [Skapa en brandväggsregel på databasnivå](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). 

# <a name="portal"></a>[Portalen](#tab/azure-portal)

Så här skapar du en resursgrupp, SQL-server och en enda databas i Azure-portalen:

1. Logga in på [portalen](https://portal.azure.com).
1. Sök efter och välj **Azure SQL**i sökfältet .
1. På **sidan Azure SQL** väljer du **Lägg till**. 
   
   ![Lägg till i Azure SQL](../media/sql-database-single-database-get-started/sqldbportal.png)
   
1. På **sidan Välj SQL-distribution väljer** du panelen **SQL-databaser** med **Enkel databas** under **Resurstyp**. Du kan visa mer information om de olika databaserna genom att välja **Visa information**.
1. Välj **Skapa**.
   
   ![Skapa en enkel databas](../media/sql-database-single-database-get-started/create-single-database.png)
   
1. Välj rätt **Azure-prenumeration** under **Projektinformation**på fliken **Grunderna** i formuläret **Skapa SQL-databas** om den inte redan är markerad.
1. Under **Resursgrupp**väljer du **Skapa ny,** anger *myResourceGroup*och väljer **OK**.
1. Ange *mySampleDatabase*för **Databasinformation**. **Database name**
1. För **Server**väljer du **Skapa ny**och fyller i formuläret Ny **server** enligt följande:
   - **Servernamn**: Ange *mysqlserver*och några tecken för unikhet.
   - **Server admin inloggning:** Ange *azureuser*.
   - **Lösenord**: Ange ett lösenord som uppfyller kraven och ange det igen i fältet **Bekräfta lösenord.**
   - **Plats**: Släpp ned och välj en plats, till exempel **(USA) östra USA.**
   
   Välj **OK**.
   
   ![Ny server](../media/sql-database-single-database-get-started/new-server.png)
   
   Registrera servern admin inloggning och lösenord så att du kan logga in på servern och databaser. Om du glömmer din inloggning eller lösenord kan du få inloggningsnamnet eller återställa lösenordet på **SQL-serversidan** när databasen har skapats. Om du vill öppna **SQL-serversidan** markerar du servernamnet på sidan **Översikt för** databasen.
   
1. Om du vill konfigurera om standardinställningarna under **Beräkna + lagring**väljer du Konfigurera **databas**.
   
   På sidan **Konfigurera** kan du också:
   - Ändra **beräkningsnivån** från **etablerad** till **Serverlös**.
   - Granska och ändra inställningarna för **virtuella kärnor** och **data maxstorlek**.
   - Välj **Ändra konfiguration** om du vill ändra maskinvarugenereringen.
   
   När du har gjort några ändringar väljer du **Använd**.
   
1. Välj **Nästa: Nätverk** längst ned på sidan.
   
   ![Ny SQL-databas - fliken Grundläggande](../media/sql-database-single-database-get-started/new-sql-database-basics.png)
   
1. Välj **Offentlig slutpunkt**under **Anslutningsmetod**på fliken **Nätverk** . 
1. Under **Brandväggsregler**anger du **Lägg till aktuell klient-IP-adress** i **Ja**.
1. Välj **Nästa: Ytterligare inställningar** längst ned på sidan.
   
   ![Fliken Nätverk](../media/sql-database-single-database-get-started/networking.png)
   
   Mer information om brandväggsinställningar finns i [Tillåt Azure-tjänster och resurser för åtkomst till den här servern](../sql-database-networkaccess-overview.md) och Lägg till en privat [slutpunkt](../../private-link/private-endpoint-overview.md).
   
1. Välj **Exempel**i avsnittet **Datakälla** på **Use existing data**fliken **Ytterligare inställningar** .
1. Välj **Granska + skapa** längst ned på sidan.
   
   ![Fliken Ytterligare inställningar](../media/sql-database-single-database-get-started/additional-settings.png)
   
1. När du har granskat inställningarna väljer du **Skapa**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan skapa en Azure-resursgrupp, SQL-server och en enda databas med hjälp av Azure command-line interface (Azure CLI). Om du inte vill använda Azure Cloud Shell [installerar du Azure CLI](/cli/azure/install-azure-cli) på din dator.

Om du vill köra följande kodexempl i Azure Cloud Shell väljer du **Prova det** i kodexemplets namnlist. När Cloud Shell öppnas väljer du **Kopiera** i kodexemplets namnlist och klistrar in kodexemplet i Cloud Shell-fönstret. `<Subscription ID>` Ersätt med ditt Azure-prenumerations-ID `$startip` i `$endip`koden `0.0.0.0` och för och ersätt med den offentliga IP-adressen för den dator du använder.

Följ anvisningarna på skärmen för att logga in på Azure och köra koden. 

Du kan också använda Azure Cloud Shell från Azure-portalen genom att välja Cloud Shell-ikonen i det övre fältet. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Första gången du använder Cloud Shell i portalen väljer du **Bash** i dialogrutan **Välkommen.** Efterföljande sessioner använder Azure CLI i en Bash-miljö eller så kan du välja **Bash** från Cloud Shell-kontrollfältet. 

Följande Azure CLI-kod skapar en Azure-resursgrupp, SQL-server, en databas och brandväggsregel för åtkomst till servern. Se till att registrera den genererade resursgruppen och servernamnen så att du kan hantera dessa resurser senare.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a logical server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a logical server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

Den föregående koden använder dessa Azure CLI-kommandon:

| Kommando | Beskrivning |
|---|---|
| [az-kontouppsättning](/cli/azure/account?view=azure-cli-latest#az-account-set) | Anger att en prenumeration ska vara den aktuella aktiva prenumerationen. | 
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [az sql server brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Skapar en servers brandväggsregler. | 
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest) | Skapar en databas. | 

Mer Azure SQL Database Azure CLI-exempel finns i [Azure CLI-exempel](../sql-database-cli-samples.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan skapa en Azure-resursgrupp, SQL-server och en enda databas med Windows PowerShell. Om du inte vill använda Azure Cloud Shell [installerar du Azure PowerShell-modulen](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill köra följande kodexempl i Azure Cloud Shell väljer du Prova det i **kodnamnsfältet.** När Cloud Shell öppnas väljer du **Kopiera** i kodexemplets namnlist och klistrar in kodexemplet i Cloud Shell-fönstret. `<Subscription ID>` Ersätt med ditt Azure-prenumerations-ID `$startIp` i `$endIp`koden `0.0.0.0` och för och ersätt med den offentliga IP-adressen för den dator du använder. 

Följ anvisningarna på skärmen för att logga in på Azure och köra koden. 

Du kan också använda Azure Cloud Shell från Azure-portalen genom att välja Cloud Shell-ikonen i det övre fältet. 
   
   ![Azure Cloud Shell](../media/sql-database-single-database-get-started/cloudshell.png)
   
Första gången du använder Cloud Shell från portalen väljer du **PowerShell** i dialogrutan **Välkommen.** Efterföljande sessioner använder PowerShell eller så kan du välja det i Kontrollfältet i Cloud Shell. 

Följande PowerShell-kod skapar en Azure-resursgrupp, SQL-server, en databas och brandväggsregel för åtkomst till servern. Se till att registrera den genererade resursgruppen och servernamnen så att du kan hantera dessa resurser senare.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
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

I koden ovan används dessa PowerShell-cmdlets:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Ny-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en SQL Database-server som är värd för enkla databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en logisk server. | 
| [Ny-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en enda Azure SQL Database-databas. | 

Mer Azure SQL Database PowerShell-exempel finns i [Azure PowerShell-exempel](../sql-database-powershell-samples.md).

---
