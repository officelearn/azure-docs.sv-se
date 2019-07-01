---
title: Etablera en Azure SSIS-integreringskörning med hjälp av PowerShell | Microsoft Docs
description: Lär dig hur du etablerar Azure SSIS-integreringskörning i Azure Data Factory med PowerShell så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bbbbc45bd050b8f68499febeed6285ad1aa883c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484768"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Etablera en Azure-SSIS-integreringskörning i Azure Data Factory med PowerShell

Den här självstudien innehåller steg för att etablera en Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) i Azure Data Factory (ADF). Azure-SSIS IR stöder paket distribueras till SSIS-katalogen (SSISDB) som värd för Azure SQL Database-server/hanterad instans (projekt-distributionsmodell) och de distribueras till system/fil resurser/Azure Files (paket-Distributionsmodellen). När Azure-SSIS IR har etablerats kan du sedan använda välbekanta verktyg, till exempel SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS) och kommandot rad verktyg, till exempel `dtinstall` / `dtutil` / `dtexec`till distribuera och köra dina paket i Azure. I den här självstudien gör du följande:

> [!NOTE]
> Den här artikeln används Azure PowerShell för att etablera en Azure-SSIS IR. Om du vill använda Azure portal/ADF-app för att etablera en Azure-SSIS IR, se [självstudien: Etablera Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en Azure-SSIS Integration Runtime
> * Starta en Azure-SSIS Integration Runtime
> * Granska det fullständiga skriptet
> * Distribuera SSIS-paket

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Azure SQL Database-server (valfritt)** . Om du inte redan har en databasserver kan du skapa en Azure-portalen innan du sätter igång. ADF skapar i sin tur SSISDB på den här databasservern. Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att integration runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. 
    - Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Vägledning i att välja vilken typ av database-server som värd för SSISDB finns i [jämför Azure SQL Database enkel databas/elastisk pool/hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Om du använder Azure SQL Database-server med virtuellt nätverk tjänstens slutpunkter/hanterad instans i ett virtuellt nätverk som värd för SSISDB, eller kräver åtkomst till lokala data, måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk finns i [skapa Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Detta gäller inte när du använder Azure SQL Database-server med virtuellt nätverk tjänstens slutpunkter/hanterad instans i ett virtuellt nätverk som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). För att aktivera den här inställningen med hjälp av PowerShell, se [New AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Lägg till IP-adressen för klientdatorn eller ett intervall med IP-adresser som innehåller IP-adressen för klientdatorn till klientens IP-adresslista i brandväggsinställningarna för databasservern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).
    - Du kan ansluta till databasservern med SQL-autentisering med dina autentiseringsuppgifter som serveradministratör eller Azure Active Directory (AAD)-autentisering med den hanterade identitet för din ADF.  I det senare måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med behörigheter för åtkomst till databasservern. Se [skapa Azure-SSIS-IR med AAD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Bekräfta att din database-server inte har en SSISDB redan. Etablering av en Azure-SSIS IR stöder inte med hjälp av en befintlig SSISDB.
- **Azure PowerShell**. Följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps), om du vill köra ett PowerShell-skript för att etablera Azure-SSIS IR.

> [!NOTE]
> - En lista över Azure-regioner där ADF och Azure-SSIS IR är tillgängliga för närvarande finns i [ADF + SSIS IR tillgänglighet efter region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Starta Windows PowerShell ISE

Starta **Windows PowerShell ISE** med administratörsprivilegier. 

## <a name="create-variables"></a>Skapa variabler

Kopiera och klistra in följande skript - ange värden för variablerna. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Logga in och Välj prenumeration

Lägg till följande kod till skriptet för att logga in och välj din Azure-prenumeration.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Verifiera anslutningen till databasservern

Lägg till följande skript för att verifiera Azure SQL Database-servern. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Om du vill skapa en Azure SQL Database som en del av skriptet, se följande exempel: 

Ange värden för de variabler som inte redan har definierats. Exempel: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med hjälp av den [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) kommando. En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

Om resursgruppen redan finns, kopiera inte den här koden i skript. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Skapa en datafabrik genom att köra följande kommandon:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Skapa Integration Runtime

Kör följande kommandon för att skapa en Azure-SSIS integration runtime som kör SSIS-paket i Azure.

Om du inte använder SSISDB kan utelämna du parametrarna CatalogServerEndpoint, CatalogPricingTier och CatalogAdminCredential.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Starta Integration Runtime

Kör följande kommandon för att starta Azure-SSIS integration runtime.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Den här processen ska slutföras inom 5 minuter exklusive helst anpassad installation.
>
> Om du använder SSISDB ansluter ADF service till din databasserver för att förbereda SSISDB. 
> 
> När du etablerar en Azure-SSIS IR installeras också Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter upprättar en anslutning till Excel/Access-filer och olika Azure-datakällor, utöver de datakällor som redan stöds av inbyggda komponenterna. Du kan också installera ytterligare komponenter, finns i [anpassad konfiguration för Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Fullständigt skript

PowerShell-skriptet i det här avsnittet konfigurerar en instans av Azure-SSIS IR som kör SSIS-paket. När du har kört det här skriptet kan du distribuera och köra SSIS-paket i Azure.

1. Starta Windows PowerShell ISE.
2. I ISE kör du följande kommando från kommandotolken.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Kopiera PowerShell-skriptet i det här avsnittet och klistra in det i ISE.
4. Ange lämpliga värden för alla parametrar i början av skriptet.
5. Kör skriptet. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Övervaka och hantera Azure-SSIS IR

I följande artiklar finns information om att övervaka och hantera Azure-SSIS IR. 

- [Övervaka Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Hantera Azure-SSIS IR](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket i den och kör dem på Azure-SSIS IR med hjälp av SSDT/SSMS-verktyg som ansluter till din databasserver via dess serverslutpunkt. För Azure SQL Database-server/hanterad instans med en offentlig slutpunkt, server-slutpunkt-format är `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`respektive. Om du inte använder SSISDB, kan du distribuera dina paket i filen System/fil resurser/Azure filer och kör dem på Azure-SSIS IR med `dtinstall` / `dtutil` / `dtexec` kommandoradsverktyg. Mer information finns i [distribuera SSIS-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med aktiviteten kör SSIS-paket i ADF pipelines finns i [anropa SSIS paketkörning som en första klassens ADF-aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Se även följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSISDB i Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Schemalägga paket körningar i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en Azure-SSIS Integration Runtime
> * Starta en Azure-SSIS Integration Runtime
> * Granska det fullständiga skriptet
> * Distribuera SSIS-paket

Läs om hur du anpassar din Azure-SSIS-integreringskörning i följande artikel:

> [!div class="nextstepaction"]
>[Anpassa Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)