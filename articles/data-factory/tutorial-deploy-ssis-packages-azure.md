---
title: Distribuera SSIS-paket till Azure | Microsoft Docs
description: "Den här artikeln förklarar hur du distribuerar SSIS-paket till Azure-SSIS Integration Runtime som tillhandahålls av Azure Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: spelluru
ms.openlocfilehash: 85777e2a4d1dea5d148a543acd068f8aa1a2335c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>Distribuera SQL Server Integration Services-paket i Azure
Azure Data Factory är en molnbaserad dataintegreringstjänst som gör att du kan skapa datadrivna arbetsflöden i molnet för att samordna och automatisera dataförflyttning och dataomvandling. Med Azure Data Factory kan du skapa och schemalägga datadrivna arbetsflöden (kallas pipelines) som kan föra in data från olika datalager, bearbeta/omvandla data med beräkningstjänster som Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics och Azure Machine Learning och publicera utgående data till datalager som Azure SQL Data Warehouse för BI-program (business intelligence) kan använda. 

Den här självstudien innehåller steg för att distribuera en Azure SSIS Integration Runtime (IR) i Azure Data Factory. Sedan kan du använda SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera SQL Server Integration Services-paket (SSIS) till den här körningen i Azure. I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en Azure-SSIS Integration Runtime
> * Starta en Azure-SSIS Integration Runtime
> * Distribuera SSIS-paket
> * Granska det fullständiga skriptet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

## <a name="prerequisites"></a>Krav
- **Azure SQL Database-server**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Den här servern är värd för SSISDB (SSIS Catalog Database). Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. 
- **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps). Du använder PowerShell för att köra ett skript för att etablera en Azure-SSIS Integration Runtime som kör SSIS-paket i molnet. 

## <a name="launch-windows-powershell-ise"></a>Starta Windows PowerShell ISE
Starta **Windows PowerShell ISE** med administratörsprivilegier. 

## <a name="create-variables"></a>Skapa variabler
Kopiera och klistra in följande skript: Ange värden för variablerna. En lista över **prisnivåer** som stöds för Azure SQL Database finns i [Resursgränser för SQL-databas](../sql-database/sql-database-resource-limits.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
# In public preview, only EastUS amd EastUS2 are supported.
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS and NorthEurope are supported.
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>Verifiera anslutningen till databasen
Lägg till följande skript för att verifiera Azure SQL Database-serverns server.database.windows.net. 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

## <a name="log-in-and-select-subscription"></a>Logga in och välj en prenumeration
Lägg till följande kod till skriptet för att logga in och välj din Azure-prenumeration: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

Om resursgruppen redan finns ska du inte kopiera den här koden i skriptet. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik
Skapa en datafabrik genom att köra följande kommandon:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Skapa Integration Runtime
Kör följande kommando för att skapa en Azure-SSIS Integration Runtime som kör SSIS-paket i Azure: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode 
```

## <a name="start-integration-runtime"></a>Starta Integration Runtime
Kör följande kommando för att starta Azure-SSIS Integration Runtime: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Det här kommandot tar mellan **20 till 30 minuter** att slutföra. 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera dina SSIS-paket till Azure. Anslut till Azure SQL-servern som är värd för SSIS-katalogen (SSISDB). Namnet på Azure SQL-servern är i formatet: `<servername>.database.windows.net` (för Azure SQL Database). 

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>Fullständigt skript
I den här versionen måste du använda PowerShell för att etablera en instans för Azure-SSIS Integration Runtime som kör SSIS-paket i molnet. För närvarande går det inte att etablera den här körningen med hjälp av Azure-portalen. 

PowerShell-skriptet i det här avsnittet konfigurerar en instans av Azure-SSIS Integration Runtime i molnet som kör SSIS-paket. När du har kört det här skriptet kan du distribuera och köra SSIS-paket i Microsoft Azure-molnet med SSISDB i Azure SQL Database.

1. Starta Windows PowerShell ISE.
2. I ISE kör du följande kommando från kommandotolken.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Kopiera PowerShell-skriptet i det här avsnittet och klistra in det i ISE.
4. Ange lämpliga värden för skriptparametrarna i avsnittet med SSIS i Azure-specifikationer i början av skriptet. Dessa parametrar beskrivs i nästa avsnitt.
5. Kör skriptet. Kommandot `Start-AzureRmDataFactoryV2IntegrationRuntime` nära skriptets slut körs i **20 till 30 minuter**.

> [!NOTE]
> Skriptet ansluts till din Azure SQL Database eller för att förbereda SSISDB. Skriptet konfigurerar också behörigheter och inställningar för ditt VNet, om det anges, och ansluter den nya instansen för Azure-SSIS Integration Runtime till VNet.

En lista över **prisnivåer** som stöds för Azure SQL Database finns i [Resursgränser för SQL-databas](../sql-database/sql-database-resource-limits.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
# In public preview, only EastUS amd EastUS2 are supported.
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# In public preview, only EastUS and NorthEurope are supported.
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_A4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>Anslut Azure-SSIS IR till ett virtuellt nätverk
Om du använder en Azure SQL-hanterad instans (privat förhandsversion) som värd för SQL Server Integration Services-katalogen (SSIS) i ett virtuellt nätverk (VNet) måste du även ansluta Azure-SSIS Integration Runtime till samma virtuella nätverk. Med Azure Data Factory version 2 (förhandsversion) kan du ansluta Azure-SSIS Integration Runtime till ett klassiskt virtuellt nätverk. Mer information finns i [Join Azure-SSIS runtime to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Ansluta Azure-SSIS-körning till ett virtuellt nätverk).

Ett fullständigt skript för att skapa en Azure-SSIS-körning som ansluter till ett virtuellt nätverk finns i [Skapa en Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Övervaka och hantera Azure-SSIS IR
I följande artiklar finns information om att övervaka och hantera Azure-SSIS IR. 

- [Övervaka en Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Hantera en Azure-SSIS Integration Runtime](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en Azure-SSIS Integration Runtime
> * Starta en Azure-SSIS Integration Runtime
> * Distribuera SSIS-paket
> * Granska det fullständiga skriptet

Fortsätt till följande självstudie och lär dig att kopiera data från en lokal plats till molnet: 

> [!div class="nextstepaction"]
>[Kopiera data i molnet](tutorial-copy-data-dot-net.md)
