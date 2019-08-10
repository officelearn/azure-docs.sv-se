---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: ffa48d55703347883edf96a0a27ee52d19189976
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935013"
---
I det här steget ska du skapa en resurs grupp och en Azure SQL Database enskild databas.

> [!IMPORTANT]
> Se till att konfigurera brand Väggs regler för att använda den offentliga IP-adressen för den dator där du utför stegen i den här artikeln. 
>
> Mer information finns i skapa en brand [Väggs regel på databas nivå](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) eller för att fastställa vilken IP-adress som används för brand Väggs regeln på server nivå för datorn se [skapa en brand vägg på server nivå](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Skapa en resurs grupp och en enkel databas med hjälp av Azure Portal.

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Välj **databaser** och välj sedan **SQL Database** för att öppna sidan **skapa SQL Database** .

   ![Skapa en enkel databas](../media/sql-database-get-started-portal/create-database-1.png)

3. På fliken **grundläggande** i avsnittet **projekt information** skriver eller väljer du följande värden:

   - **Prenumeration**: Välj rätt prenumeration i den nedrullningsbara listrutan om den inte visas.
   - **Resursgrupp**: Välj **Skapa ny**, Skriv `myResourceGroup`och välj **OK**.

     ![Ny SQL-databas – fliken grundläggande](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. I avsnittet **databas information** skriver eller väljer du följande värden:

   - **Databasnamn**: Ange `mySampleDatabase`.
   - **Server**: Välj **Skapa ny**, ange följande värden och välj sedan **Välj**.
       - **Servernamn**: Skriv `mysqlserver`; tillsammans med vissa siffror för unikhet.
       - **Inloggning för serveradministratör**: Skriv `azureuser`.
       - **Lösenord**: Ange ett komplext lösen ord som uppfyller lösen ords kraven.
       - **Plats**: Välj en plats i list rutan, till exempel `West US 2`.

         ![Ny server](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Skriv ned serveradministratörens inloggning och lösenord så att du kan logga in på servern och databaserna till denna och andra snabbstarter. Om du glömmer din inloggning eller ditt lösenord kan du hämta inloggningsnamnet eller återställa lösenordet på **SQLServer**-sidan. För att öppna **SQLServer**-sidan väljer du servernamnet i databasens **översiktssida** när databasen har skapats.

   - **Vill du använda elastisk SQL-pool**: Välj alternativet **Nej** .
   - **Compute + Storage**: Välj **Konfigurera databas**. 

     ![SQL Database information](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - Välj **etablerade** och **Gen5**.

     ![Etablerade Gen4](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - Granska inställningarna för **Max virtuella kärnor**, **min virtuella kärnor**, AutoPause **Delay**och **data Max storlek**. Ändra dem efter behov.
   - Godkänn villkoren för för hands versionen och klicka på **OK**.
   - Välj **Använd**.

5. Välj fliken **ytterligare inställningar** . 
6. I avsnittet **data källa** , under **Använd befintliga data**, väljer `Sample`du.

   ![Ytterligare SQL DB-inställningar](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Se till att välja data för **Sample (AdventureWorksLT)** så att du enkelt kan följa den här och andra snabbstarter för Azure SQL-databas som använder dessa data.

7. Lämna resten av värdena som standard och välj **Granska + skapa** längst ned i formuläret.
8. Granska de slutgiltiga inställningarna och välj **skapa**.

9. I formuläret **SQL Database** väljer du **Skapa** för att distribuera och etablera resursgruppen, servern och databasen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Skapa en resurs grupp och en enkel databas med PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US 2"
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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Skapa en resurs grupp och en enkel databas med AZ CLI.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

---
