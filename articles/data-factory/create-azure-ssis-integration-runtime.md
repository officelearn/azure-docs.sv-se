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
ms.openlocfilehash: a5ed3cbfac0b86cedde5718cef4231a7fcc36f2e
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Skapa en Azure-SSIS-integrering körning i Azure Data Factory
Den här artikeln innehåller steg för att etablera en Azure-SSIS-integrering körning i Azure Data Factory. Sedan kan du använda SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera SQL Server Integration Services-paket (SSIS) till den här körningen i Azure.

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-introduction.md).

Självstudier: [Självstudier: distribuera paket för SQL Server Integration Services (SSIS) till Azure](tutorial-deploy-ssis-packages-azure.md) visar hur du skapar Azure SSIS Integration Runtime (IR) med hjälp av Azure SQL Database som Arkiv för SSIS-katalogen. Den här artikeln kan utökas med kursen och visar hur du gör följande: 

- Använd Azure SQL hanteras-instans (privat förhandsvisning) som värd för en SSIS-katalog (SSIDB-databasen).
- Anslut Azure SSIS IR till Azure-nätverk (VNet). 

Konceptuell information om att ansluta en Azure-SSIS-IR till ett virtuellt nätverk och konfigurera ett virtuellt nätverk i Azure-portalen finns [ansluta till Azure-SSIS-IR till VNet](join-azure-ssis-integration-runtime-virtual-network.md). 

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**. Om du inte har en prenumeration kan du skapa ett [kostnadsfritt utvärderingskonto](http://azure.microsoft.com/pricing/free-trial/).
- **Azure SQL Database-server** eller **SQL Server-hanterad instans (privat förhandsversion) (utökad privat förhandsversion)**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Den här servern är värd för SSISDB (SSIS Catalog Database). Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. Notera prisnivån för din Azure SQL-server. En lista över stöds prisnivåer för Azure SQL Database, se [gränserna för SQL-databas](../sql-database/sql-database-resource-limits.md).
- **Klassiskt virtuellt nätverk (VNet) (tillval)**. Du måste ha ett klassiskt virtuellt Azure-nätverk (VNet) om minst ett av följande villkor stämmer:
    - Du är värd för SSIS-katalogdatabasen på en SQL Server-hanterad instans (privat förhandsversion) som är en del av ett VNet.
    - Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime.
- **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps). Du använder PowerShell för att köra ett skript för att etablera en Azure-SSIS Integration Runtime som kör SSIS-paket i molnet. 

> [!NOTE]
> En lista över regioner som stöds av Azure Data Factory V2 och Azure-SSIS Integration Runtime finns i [Tillgängliga produkter efter region](https://azure.microsoft.com/regions/services/). Expandera **Data och analys** för att se **Data Factory V2** och **SSIS Integration Runtime**.

## <a name="azure-portal"></a>Azure Portal

### <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Logga in på [Azure-portalen](https://portal.azure.com/).    
2. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. I den **nya datafabriken** anger **MyAzureSsisDataFactory** för den **namn**. 
      
     ![Ny data factory-sida](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om du får följande fel kan ändra namnet på data factory (till exempel yournameMyAzureSsisDataFactory) och försök skapa igen. Se [Data Factory - namnregler](naming-rules.md) artikel för namnregler för Data Factory-artefakter.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
      - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
      - Välj **Skapa ny** och ange namnet på en resursgrupp.   
         
      Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast de platser som stöds för att skapa datafabriker visas i listan.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. När den har skapats visas den **Data Factory** sidan som visas i bilden.
   
   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. Klicka på **författare & övervakaren** att starta Data Factory användaren användargränssnittet (UI) i en separat flik. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Etablera en integration Azure SSIS-körning

1. Klicka på sidan komma igång **konfigurera SSIS-integrering Runtime** panelen. 

   ![Konfigurera SSIS-integrering Runtime sida vid sida](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. I den **allmänna inställningar** sidan **integrering Runtime installationsprogrammet**, gör du följande: 

   ![Allmänna ändringar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    1. Ange en **namn** för integrering av körningsmiljön.
    2. Välj den **plats** för integrering av körningsmiljön. Endast stöds platser visas.
    3. Välj den **storlek för noden** som ska konfigureras med SSIS-körningsmiljön.
    4. Ange den **antal noder** i klustret.
    5. Klicka på **Nästa**. 
1. I den **SQL-inställningar**, gör du följande: 

    ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    1. Ange Azure **prenumeration** som har Azure SQL-servern. 
    2. Välj din Azure SQL-server för den **katalog databasen Server Endpoint**.
    3. Ange den **administratör** användarnamn.
    4. Ange den **lösenord** för administratören.  
    5. Välj den **tjänstnivån** för SSIDB-databasen. Standardvärdet är Basic.
    6. Klicka på **Nästa**. 
1.  I den **avancerade inställningar** väljer du ett värde för den **maximala parallella körningar Per nod**.   

    ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. Det här steget är **valfria**. Om du har ett klassiskt virtuellt nätverk (VNet) som du vill integration runtime vill delta väljer du den **väljer ett virtuellt nätverk för din Azure-SSIS-integrering runtime att ansluta och ge Azure-tjänster så här konfigurerar du VNet behörighetsinställningar** alternativet och gör följande: 

    ![Avancerade inställningar med VNet](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

    1. Ange den **prenumeration** som har det klassiska VNet. 
    2. Välj den **VNet**. <br/>
    4. Välj den **undernät**.<br/> 
1. Klicka på **Slutför** att starta skapandet av Azure-SSIS-integrering runtime. 

    > [!IMPORTANT]
    > - Den här processen tar cirka 20 minuter att slutföra
    > - Data Factory-tjänsten ansluter till din Azure SQL Database för att förbereda katalog SSIS-databasen (SSISDB). Skriptet konfigurerar också behörigheter och inställningar för ditt VNet, om det anges, och ansluter den nya instansen för Azure-SSIS Integration Runtime till VNet.
7. I den **anslutningar** och växla till **integrering körningar** om det behövs. Klicka på **uppdatera** att uppdatera statusen. 

    ![Skapandestatus](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. Använd länkarna under **åtgärder** kolumnen övervaka, Stopp/start, redigera eller ta bort integration körningsmiljön. Använd den sista länken om du vill visa JSON-kod för integrering av körningsmiljön. Redigera och ta bort knapparna är endast aktiverat när IR har stoppats. 

    ![Azure SSIS-IR - åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        
9. Klicka på **övervakaren** länken under **åtgärder**.  

    ![Azure SSIS-IR - information](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-details.png)
10. Om det fanns en **fel** som är associerade med Azure SSIS-IR, visas antalet fel på den här sidan och länken om du vill visa detaljer om felet. Till exempel om SSIS-katalogen finns redan på servern, visas ett felmeddelande som anger förekomsten av SSIDB-databasen.  
11. Klicka på **integrering körningar** överst till navigera för att säkerhetskopiera den föregående sidan om du vill se alla integration körningar som är kopplade till datafabriken.  

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integrering körningar i portalen

1. I Användargränssnittet för Azure Data Factory växla till den **redigera** klickar du på **anslutningar**, och sedan växla till **integrering körningar** att visa befintliga integration körningar i din data factory. 
    ![Visa befintliga IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
1. Klicka på **ny** att skapa en ny Azure-SSIS-IR. 

    ![Integration runtime via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
2. Klicka för att skapa en Azure-SSIS-integrering körning **ny** som visas i bilden. 
3. I fönstret Integration Runtime för Välj **Lift och SKIFT befintliga SSIS-paket i Azure för att köra**, och klicka sedan på **nästa**.

    ![Ange vilken typ av integrering runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Finns det [etablera ett Azure SSIS-integrering runtime](#provision-an-azure-ssis-integration-runtime) för de återstående steg om du vill konfigurera en Azure-SSIS-IR.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="create-variables"></a>Skapa variabler
Definiera variabler för användning i skripten i den här självstudien:

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_A4_v2" 
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

# Remove these the following two OPTIONAL variables if you are using Azure SQL Database. 
# These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
# Get the following information from the properties page for your Classic Virtual Network in the Azure portal
# It should be in the format: $VnetId = "/subscriptions/<Azure Subscription ID>/resourceGroups/<Azure Resource Group>/providers/Microsoft.ClassicNetwork/virtualNetworks/<Class Virtual Network Name>"

# OPTIONAL: In public preview, only classic virtual network (VNet) is supported.
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

```
### <a name="log-in-and-select-subscription"></a>Logga in och välj en prenumeration
Lägg till följande kod skriptet för att logga in och välj din Azure-prenumeration: 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Verifiera anslutningen till databasen
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

### <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
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

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Skapa en datafabrik genom att köra följande kommandon:

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Skapa Integration Runtime
Kör följande kommando för att skapa en Azure-SSIS-integrering körning som körs SSIS-paket i Azure: Använd skriptet från avsnittet baserat på vilken typ av databas (Azure SQL Database kontra Azure SQL hanteras-instans (privat förhandsvisning)) som du använder. 

#### <a name="azure-sql-database-to-host-the-ssisdb-database-ssis-catalog"></a>Azure SQL-databas som värd för SSIDB-databasen (SSIS-katalog) 

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

#### <a name="azure-sql-managed-instance-private-preview-to-host-the-ssisdb-database"></a>Azure SQL hanteras-instans (privat förhandsvisning) som värd för SSIDB-databasen

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

### <a name="start-integration-runtime"></a>Starta Integration Runtime
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


### <a name="full-script"></a>Fullständigt skript
Här är det fullständiga skript som skapar en Azure-SSIS-IR och ansluter till ett virtuellt nätverk. Det här skriptet förutsätter att du använder Azure SQL hanteras instansen (MI) som värd för SSIS-katalogen. 

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_A4_v2" 
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# In public preview, only 1-8 parallel executions per node are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"

# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "[your Azure SQL Database pricing tier. Examples: Basic, S0, S1, S2, S3, etc.]"

## Remove these two OPTIONAL variables if you are using Azure SQL Database. 
## These two parameters apply if you are using VNet and Azure SQL Managed Instance (private preview). 
# In public preview, only classic virtual network (VNet) is supported.
$VnetId = "[your VNet resource ID or leave it empty]" 
$SubnetName = "[your subnet name or leave it empty]" 

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

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
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName

write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
Du kan använda en Azure Resource Manager-mall för att skapa en Azure-SSIS-integrering körning. Här är en exempel-genomgång: 

1. Skapa en JSON-fil med hjälp av följande Resource Manager-mallen. Ersätt värdena i vinkelparenteser (platshållare) med egna värden. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for the Azure SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D1_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 1
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL server>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL user",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
2. Om du vill distribuera Resource Manager-mallen, kör du kommandot New AzureRmResourceGroupDeployment enligt följande exmaple. I det här exemplet är ADFTutorialResourceGroup namnet på resursgruppen. ADFTutorialARM.json är den fil som innehåller en JSON-definition för data factory och Azure-SSIS-IR. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Det här kommandot skapar datafabriken och skapar en Azure-SSIS-IR i den, men den startar inte IR. 
3. Om du vill starta Azure-SSIS-IR, kör du kommandot Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name> `
                                             -DataFactoryName <Data Factory Name> `
                                             -Name <Azure SSIS IR Name> `
                                             -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera dina SSIS-paket till Azure. Anslut till Azure SQL-servern som är värd för SSIS-katalogen (SSISDB). Namnet på Azure SQL-servern är i formatet: &lt;servername&gt;.database.windows.net (för Azure SQL Database). I artikeln om att [distribuera paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) finns det instruktioner. 

## <a name="next-steps"></a>Nästa steg
Finns i andra Azure-SSIS-IR-avsnitt i den här dokumentationen:

- [Azure-SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar i allmänhet inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-deploy-ssis-packages-azure.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller begreppsrelaterad information om att ansluta Azure-SSIS IR till ett virtuellt Azure-nätverk (VNet). Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 