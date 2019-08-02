---
title: 'Självstudier: Lägga till en Azure SQL Database enkel databas i en failover-grupp | Microsoft Docs'
description: Lägg till en Azure SQL Database enkel databas i en failover-grupp med hjälp av Azure Portal, PowerShell eller Azure CLI.
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
ms.openlocfilehash: d11dd72c65ea32fb5a262f325bdcad0b5a8ab863
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566660"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Självstudier: Lägga till en Azure SQL Database enkel databas i en failover-grupp

Konfigurera en failover-grupp för en Azure SQL Database enkel databas och testa redundans med antingen Azure Portal, PowerShell eller Azure CLI.  I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> - Skapa en Azure SQL Database enskild databas.
> - Skapa en [failover-grupp](sql-database-auto-failover-group.md) för en enskild databas mellan två logiska SQL-servrar.
> - Redundanstest.

## <a name="prerequisites"></a>Förutsättningar

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
För att kunna följa den här självstudien måste du ha: 

- En Azure-prenumeration. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Kontrol lera att du har följande objekt för att slutföra självstudien:

- En Azure-prenumeration. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Kontrol lera att du har följande objekt för att slutföra självstudien:

- En Azure-prenumeration. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.
- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 – Skapa en enskild databas 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 – Skapa redundans gruppen 
I det här steget ska du skapa en [redundans grupp](sql-database-auto-failover-group.md) mellan en befintlig Azure SQL-Server och en ny Azure SQL-Server i en annan region. Lägg sedan till exempel databasen i gruppen för växling vid fel. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Skapa din grupp för redundans och Lägg till din enda databas i den med hjälp av Azure Portal. 


1. Välj **alla tjänster** i det övre vänstra hörnet av [Azure Portal](https://portal.azure.com). 
1. Skriv `sql servers` i sökrutan. 
1. Valfritt Välj stjärn ikonen bredvid SQL-servrar för att skapa **SQL-servrar** och lägga till dem i det vänstra navigerings fönstret. 
    
    ![Hitta SQL-servrar](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Välj **SQL-servrar** och välj den server som du skapade i avsnitt 1, `mysqlserver`till exempel.
1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan **Lägg till grupp** för att skapa en ny grupp för redundans. 

    ![Lägg till ny redundans grupp](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. På sidan **redundans** anger eller väljer du följande värden och väljer sedan **skapa**:
    - **Namn på redundans grupp**: Ange ett unikt namn för redundans, till exempel `failovergrouptutorial`. 
    - **Sekundär server**: Välj alternativet för att *Konfigurera nödvändiga inställningar* och välj sedan att **skapa en ny server**. Alternativt kan du välja en redan befintlig server som den sekundära servern. När du har angett följande värden väljer du **Välj**. 
        - **Servernamn**: Ange ett unikt namn på den sekundära servern, till exempel `mysqlsecondary`. 
        - **Inloggning för serveradministratör**: Bastyp`azureuser`
        - **Lösenord**: Ange ett komplext lösen ord som uppfyller lösen ords kraven.
        - **Plats**: Välj en plats i list rutan, till exempel USA, östra 2. Den här platsen kan inte vara samma plats som den primära servern.

    > [!NOTE]
    > Inställningarna för Server inloggning och brand vägg måste matcha den primära servern. 
    
      ![Skapa en sekundär server för redundans gruppen](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **Databaser i gruppen**: När du har valt en sekundär server blir det här alternativet olåst. Välj den för att **välja databaser som ska läggas till** och välj sedan den databas som du skapade i avsnitt 1. Om du lägger till databasen i gruppen för växling vid fel startas automatiskt processen för geo-replikering. 
        
    ![Lägg till SQL DB i redundans gruppen](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Skapa din grupp för redundans och Lägg till din databas i den med hjälp av PowerShell. 

   > [!NOTE]
   > Inställningarna för Server inloggning och brand vägg måste matcha den primära servern. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

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

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Skapa din grupp för redundans och Lägg till din enda databas i den med hjälp av AZ CLI. 

   > [!NOTE]
   > Inställningarna för Server inloggning och brand vägg måste matcha den primära servern. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3-testa redundans 
I det här steget kommer du inte att kunna redundansväxla gruppen till den sekundära servern och sedan växla tillbaka med Azure Portal. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Testa redundans med Azure Portal. 

1. Gå till **SQL** Server-servern inom [Azure Portal](https://portal.azure.com). 
1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan den grupp för växling vid fel som du skapade i avsnitt 2. 
  
   ![Välj gruppen redundans från portalen](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär. 
1. Välj **redundans** från åtgärds fönstret för att Redundansväxla din redundanskonfiguration som innehåller din exempel enkla databas. 
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas bort. 

   ![Redundansväxla din failover-grupp som innehåller din SQL-databas](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Granska den server som nu är primär och vilken server som är sekundär. Om redundans har slutförts ska de två servrarna ha växlade roller. 
1. Välj **redundans** igen för att återställa servrarna till deras ursprungliga roller. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Testa redundans med PowerShell. 


Kontrol lera den sekundära replikens roll: 

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


Redundansväxla till den sekundära servern: 

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
   Write-host "Failed failover group to sucessfully to" $drServerName 
   ```

Återställ redundansväxlingen tillbaka till den primära servern:

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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Testa redundans med AZ CLI. 


Verifiera vilken server som är sekundär:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Redundansväxla till den sekundära servern: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Återställ redundansväxlingen tillbaka till den primära servern:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>Rensa resurser 
Rensa resurser genom att ta bort resurs gruppen. 

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Ta bort resurs gruppen med hjälp av Azure Portal. 


1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com).
1. Välj **ta bort resurs grupp** för att ta bort alla resurser i gruppen, samt själva resurs gruppen. 
1. Skriv namnet på resurs gruppen `myResourceGroup`, i text rutan och välj sedan **ta bort** för att ta bort resurs gruppen.  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Ta bort resurs gruppen med hjälp av PowerShell. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Ta bort resurs gruppen med AZ CLI. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>Fullständiga skript

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="azure-portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)
Det finns inga tillgängliga skript för Azure Portal.
 
---

Du kan hitta andra Azure SQL Database skript här: [Azure PowerShell](sql-database-powershell-samples.md) och [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en Azure SQL Database enkel databas i en failover-grupp och testat redundansväxlingen. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa en Azure SQL Database enskild databas. 
> - Skapa en [failover-grupp](sql-database-auto-failover-group.md) för en enskild databas mellan två logiska SQL-servrar.
> - Redundanstest.

Gå vidare till nästa självstudie om hur du lägger till en elastisk pool i en failover-grupp. 

> [!div class="nextstepaction"]
> [Självstudier: Lägga till en Azure SQL Database elastisk pool i en failover-grupp](sql-database-elastic-pool-failover-group-tutorial.md)
