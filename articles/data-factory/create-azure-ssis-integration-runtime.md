---
title: "Skapa automatisk värdbaserade integration körning i Azure Data Factory | Microsoft Docs"
description: "Lär dig hur du kan använda lagrade Proceduraktiviteten för SQL Server för att anropa en lagrad procedur i en Azure SQL Database eller Azure SQL Data Warehouse från Data Factory-pipelinen."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.openlocfilehash: 8cc4d44bff6284f2c52423f04e463e324a932abd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Skapa en Azure-SSIS-integrering körning i Azure Data Factory
Den här artikeln innehåller steg för att etablera en Azure-SSIS-integrering körning i Azure Data Factory. Sedan kan du använda SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera SQL Server Integration Services-paket (SSIS) till den här körningen i Azure.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig (GA), se [dokumentationen för Data Factory version 1](v1/data-factory-introduction.md).

Självstudier: [Självstudier: distribuera paket för SQL Server Integration Services (SSIS) till Azure](tutorial-deploy-ssis-packages-azure.md) visar hur du skapar Azure SSIS Integration Runtime (IR) med hjälp av Azure SQL Database som Arkiv för SSIS-katalogen. Den här artikeln kan utökas med kursen och visar hur du gör följande: 

- Använd Azure SQL hanteras-instans (privat förhandsvisning) som värd för en SSIS-katalog (SSIDB-databasen).
- Anslut Azure SSIS IR till Azure-nätverk (VNet). 

Konceptuell information om att ansluta en Azure-SSIS-IR till ett virtuellt nätverk och konfigurera ett virtuellt nätverk i Azure-portalen finns [ansluta till Azure-SSIS-IR till VNet](join-azure-ssis-integration-runtime-virtual-network.md). 

## <a name="prerequisites"></a>Krav

- **Azure-prenumeration**. Om du inte har en prenumeration kan du skapa ett [kostnadsfritt utvärderingskonto](http://azure.microsoft.com/pricing/free-trial/).
- **Azure SQL Database-server** eller **SQL Server-hanterad instans (privat förhandsversion) (utökad privat förhandsversion)**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Den här servern är värd för SSISDB (SSIS Catalog Database). Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. Notera prisnivån för din Azure SQL-server. En lista över stöds prisnivåer för Azure SQL Database, se [gränserna för SQL-databas](../sql-database/sql-database-resource-limits.md).
- **Klassiskt virtuellt nätverk (VNet) (tillval)**. Du måste ha ett klassiskt virtuellt Azure-nätverk (VNet) om minst ett av följande villkor stämmer:
    - Du är värd för SSIS-katalogdatabasen på en SQL Server-hanterad instans (privat förhandsversion) som är en del av ett VNet.
    - Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime.
- **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps). Du använder PowerShell för att köra ett skript för att etablera en Azure-SSIS Integration Runtime som kör SSIS-paket i molnet. 

> [!NOTE]
> En lista över regioner som stöds av Azure Data Factory V2 och Azure-SSIS-integrering körning, se [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/). Expandera **Data + analys** att se **Data Factory V2** och **SSIS-integrering Runtime**.


## <a name="create-variables"></a>Skapa variabler
Definiera variabler för användning i skripten i den här självstudien:

```powershell
# Azure Data Factory version 2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## Remove these two variables if you are using Azure SQL Database. 
## These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNet) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

```

## <a name="validate-the-connection-to-database"></a>Verifiera anslutningen till databasen
Lägg till följande skript för att verifiera din Azure SQL Database server server.database.windows.net eller Azure SQL-instans som hanterade (privat förhandsvisning) Serverslutpunkten. 

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
Lägg till följande kod skriptet för att logga in och välj din Azure-prenumeration: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Lägg till följande skript för att automatiskt konfigurera behörigheter/inställningar för virtuella nätverk som din Azure-SSIS Integration Runtime kan anslutas till.

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

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

Om du använder **Azure SQL Database** som värd för SSIDB-databasen (SSIS-katalog): 


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

Du behöver inte ange värden för VNetId och undernät, om du inte behöver åtkomst till lokala data, det vill säga du har lokala data källor/mål i SSIS-paket. Du måste skicka värde för parametern CatalogPricingTier. En lista över stöds prisnivåer för Azure SQL Database, se [gränserna för SQL-databas](../sql-database/sql-database-resource-limits.md).

Om du använder **Azure SQL hanteras-instans (privat förhandsvisning)** som värd för SSIDB-databasen:

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

Du måste ange värden för parametrarna VnetId och undernät med Azure SQL hanteras-instans (privat förhandsvisning) som ansluter till ett virtuellt nätverk. Parametern CatalogPricingTier gäller inte för Azure SQL-instans som hanteras. 

## <a name="start-integration-runtime"></a>Starta Integration Runtime
Kör följande kommando för att starta Azure-SSIS Integration Runtime: 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Det här kommandot tar mellan **20 till 30 minuter** att slutföra. 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera dina SSIS-paket till Azure. Anslut till Azure SQL-servern som är värd för SSIS-katalogen (SSISDB). Namnet på Azure SQL-servern är i formatet: &lt;servername&gt;.database.windows.net (för Azure SQL Database). I artikeln om att [distribuera paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) finns det instruktioner. 

## <a name="next-steps"></a>Nästa steg
Finns i andra Azure-SSIS-IR-avsnitt i den här dokumentationen:

- [Azure-SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar i allmänhet inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-deploy-ssis-packages-azure.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk (VNet). Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 