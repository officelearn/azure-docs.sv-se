---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444687"
---
I det här steget ska du skapa en resurs grupp och en Azure SQL Database enskild databas. 

> [!IMPORTANT]
> Se till att konfigurera brand Väggs regler för att använda den offentliga IP-adressen för den dator där du utför stegen i den här artikeln. Brand Väggs regler på databas nivå kommer att replikeras automatiskt till den sekundära servern.
>
> Mer information finns i skapa en brand [Väggs regel på databas nivå](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) eller för att fastställa vilken IP-adress som används för brand Väggs regeln på server nivå för datorn se [skapa en brand vägg på server nivå](../articles/sql-database/sql-database-server-level-firewall-rule.md).  

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Skapa en resurs grupp och en enkel databas med hjälp av Azure Portal. 

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
2. Välj **databaser** och välj sedan **SQL Database** för att öppna sidan **skapa SQL Database** .

   ![Skapa en enkel databas](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. På fliken **grundläggande** i avsnittet **projekt information** skriver eller väljer du följande värden:

   - **Prenumeration**: Välj rätt prenumeration i den nedrullningsbara listrutan om den inte visas.
   - **Resursgrupp**: Välj **Skapa ny**, Skriv `myResourceGroup`och välj **OK**.

     ![Ny SQL-databas – fliken grundläggande](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. I avsnittet **databas information** skriver eller väljer du följande värden:

   - **Databasnamn**: Ange `mySampleDatabase`.
   - **Server**: Välj **Skapa ny** och ange följande värden och välj sedan **Välj**.
       - **Servernamn**: Skriv `mysqlserver`; tillsammans med vissa siffror för unikhet.
       - **Inloggning för serveradministratör**: Skriv `azureuser`.
       - **Lösenord**: Ange ett komplext lösen ord som uppfyller lösen ords kraven.
       - **Plats**: Välj en plats i list rutan, till exempel `West US 2`.

         ![Ny server](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Skriv ned serveradministratörens inloggning och lösenord så att du kan logga in på servern och databaserna till denna och andra snabbstarter. Om du glömmer din inloggning eller ditt lösenord kan du hämta inloggningsnamnet eller återställa lösenordet på **SQLServer**-sidan. För att öppna **SQLServer**-sidan väljer du servernamnet i databasens **översiktssida** när databasen har skapats.

        ![SQL Database information](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Vill du använda elastisk SQL-pool**: Välj alternativet **Nej** .
   - **Compute + Storage**: Välj **Konfigurera databas** och för den här snabb starten och välj **vCore-baserade inköps alternativ**

     ![Virtuell kärna-baserade inköpsalternativ](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - Välj **etablerade** och **Gen4**.

     ![Server lös beräknings nivå](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - Granska inställningarna för **Max virtuella kärnor**, **min virtuella kärnor**, **Automatisk PAUSE**och **data Max storlek**. Ändra dem efter behov.
   - Godkänn villkoren för för hands versionen och klicka på **OK**.
   - Välj **Använd**.

5. Välj fliken **ytterligare inställningar** . 
6. I avsnittet **data källa** , under **Använd befintliga data**, väljer `Sample`du. 

   ![Ytterligare SQL DB-inställningar](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Se till att välja data för **Sample (AdventureWorksLT)** så att du enkelt kan följa den här och andra snabbstarter för Azure SQL-databas som använder dessa data.

7. Lämna resten av värdena som standard och välj **Granska + skapa** längst ned i formuläret.
8. Granska de slutgiltiga inställningarna och välj **skapa**.

9. I formuläret **SQL Database** väljer du **Skapa** för att distribuera och etablera resursgruppen, servern och databasen.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

Skapa en resurs grupp och en enkel databas med PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Skapa en resurs grupp och en enkel databas med AZ CLI. 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---