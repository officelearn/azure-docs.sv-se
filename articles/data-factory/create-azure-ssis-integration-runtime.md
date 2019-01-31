---
title: Skapa Azure-SSIS Integration Runtime i Azure Data Factory | Microsoft Docs
description: Lär dig mer om att skapa Azure-SSIS Integration Runtime i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 5b6bef8194123ed6c83a48dd5e819085d4bc5424
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453475"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Skapa Azure-SSIS Integration Runtime i Azure Data Factory
Den här artikeln innehåller steg för etablering Azure-SSIS Integration Runtime (IR) i Azure Data Factory (ADF). Du kan sedan använda SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera och köra SQL Server Integration Services (SSIS)-paket på den här integration runtime i Azure. 

Den [självstudien: Distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md) visar hur du skapar Azure-SSIS IR med Azure SQL Database-server till värd SSIS-katalogdatabasen (SSISDB). Den här artikeln utökas med självstudien och visar hur du gör du följande: 

- Du kan också använda Azure SQL Database-server med virtuellt nätverk tjänstens slutpunkter/hanterad instans som värd för SSISDB. Vägledning i att välja vilken typ av database-server som värd för SSISDB finns i [jämför Azure SQL Database enkel databaser/elastiska pooler och Managed Instance](create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Som ett krav måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk och konfigurera behörigheter/inställningar för virtuella nätverk efter behov. Se [ansluta till Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Du kan också använda Azure Active Directory (AAD)-autentisering med den hanterade identitet för din ADF för att ansluta till databasservern. Som ett krav, måste du lägga till den hanterade identitet för din ADF som en oberoende databasanvändare som kan skapa SSISDB i din Azure SQL Database-server/hanterad instans [aktiverar AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

## <a name="overview"></a>Översikt
Den här artikeln beskrivs olika sätt att etablering Azure-SSIS IR: 

- [Azure Portal](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Azure Resource Manager-mall](#azure-resource-manager-template) 

När du skapar Azure-SSIS IR ansluts ADF-tjänsten till din Azure SQL Database-server/hanterad instans att förbereda SSISDB. Dessutom konfigurerar behörigheter/inställningar för det virtuella nätverket om anges, och ansluter till din Azure-SSIS IR till det virtuella nätverket. 

När du etablerar Azure-SSIS IR installeras också Azure Feature Pack för SSIS och Access Redistributable. Dessa komponenter upprättar anslutning till Excel/Access-filer och olika Azure-datakällor, utöver de datakällor som stöds av inbyggda komponenterna. Du kan också installera ytterligare komponenter. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Förutsättningar 
- **Azure-prenumeration**. Om du inte redan har en prenumeration kan du skapa en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/) konto. 

- **Azure SQL Database-server eller hanterad instans**. Om du inte redan har en databasserver kan du skapa en Azure-portalen innan du sätter igång. Den här servern ska vara värd för SSISDB. Vi rekommenderar att du skapar databasservern i samma Azure-region som din integration runtime. Den här konfigurationen gör din integration runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. Baserat på valda databasservern, kan SSISDB skapas för din räkning som en enkel databas, en del av en elastisk pool eller i din hanterade instans och kan nås i offentliga nätverk eller genom att koppla ett virtuellt nätverk. En lista över stöds prisnivåer för Azure SQL Database finns i [SQL Database-resursgränser](../sql-database/sql-database-resource-limits.md). 

    Se till att din Azure SQL Database-server/hanterad instans inte redan har en SSISDB. Etablera Azure-SSIS IR stöder inte med hjälp av en befintlig SSISDB. 

- **Azure Resource Manager-nätverk (valfritt)**. Du måste ha en Azure Resource Manager-nätverk om minst en av följande villkor föreligger: 
    - Du är värd för SSISDB i Azure SQL Database-server med virtuella nätverksslutpunkter eller hanterad instans som är i ett virtuellt nätverk. 
    - Du vill ansluta till lokala data datalager från SSIS-paket som körs på Azure-SSIS IR. 

- **Azure PowerShell**. Följ anvisningarna på [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps)om du vill köra ett PowerShell-skript för att etablera Azure-SSIS IR. 

### <a name="region-support"></a>Regionsstöd
En lista över Azure-regioner, där ADF och Azure-SSIS IR är för närvarande tillgängligt i [ADF + SSIS IR tillgänglighet efter region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Jämför SQL Database enkel databas/elastisk pool och SQL Database Managed Instance

I följande tabell jämförs vissa funktioner i Azure SQL Database-server och hanterad instans som är relaterade till Azure-SSIR IR:

| Funktion | enkel databas/elastisk pool| Managed Instance |
|---------|--------------|------------------|
| **Scheduling** | SQL Server Agent är inte tillgänglig.<br/><br/>Se [schemalägga en körning av paket i ADF pipeline](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Hanterad instans-Agent är tillgänglig. |
| **Autentisering** | Du kan skapa SSISDB med en oberoende databasanvändare som representerar alla AAD-grupp med den hanterade identitet för din ADF som en medlem i den **db_owner** roll.<br/><br/>Se [aktivera Azure AD-autentisering för att skapa SSISDB i Azure SQL Database-server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Du kan skapa SSISDB med en oberoende databasanvändare som representerar din ADF hanterad identitet. <br/><br/>Se [aktivera Azure AD-autentisering för att skapa SSISDB i Azure SQL Database Managed Instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Tjänstenivå** | När du skapar Azure-SSIS IR med din Azure SQL Database-server kan välja du tjänstnivå för SSISDB. Det finns flera tjänstnivåer. | När du skapar Azure-SSIS IR med din hanterade instans kan välja du inte tjänstnivå för SSISDB. Alla databaser i din hanterade instans delar samma resurs allokeras till instansen. |
| **Virtuellt nätverk** | Stöder endast Azure Resource Manager virtuellt nätverk för din Azure-SSIS IR att ansluta till om du använder Azure SQL Database-server med virtuella nätverksslutpunkter eller kräver åtkomst till lokala datalager. | Stöder endast Azure Resource Manager virtuellt nätverk för din Azure-SSIS IR att ansluta till. Det virtuella nätverket krävs alltid.<br/><br/>Om du ansluter din Azure-SSIS IR till samma virtuella nätverk som din hanterade instans, se till att din Azure-SSIS IR är i ett annat undernät än din hanterade instans. Om du ansluter din Azure-SSIS IR till ett annat virtuellt nätverk än din hanterade instans, rekommenderar vi en virtuell nätverkspeering eller i virtuella nätverk till virtuell nätverksanslutning. Se [Anslut ditt program till Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuerade transaktioner** | Stöds via elastiska transaktioner. Microsoft Distributed Transaction Coordinator (MSDTC) transaktioner stöds inte. Om SSIS-paketen använder MSDTC koordinera distribuerade transaktioner, Överväg att migrera till elastiska transaktioner för Azure SQL Database. Mer information finns i [distribuerade transaktioner över molndatabaser](../sql-database/sql-database-elastic-transactions-overview.md). | Stöds ej. |
| | | |

## <a name="azure-portal"></a>Azure Portal

I det här avsnittet använder Azure-portalen, särskilt ADF User Interface (UI) / appen att skapa Azure-SSIS IR. 

### <a name="create-a-data-factory"></a>Skapa en datafabrik 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome. 
1. Logga in på [Azure-portalen](https://portal.azure.com/). 
1. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 

   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** som **namn**. 

   ![Sida för ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnMyAzureSsisDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
1. För **resursgruppen** utför du något av följande steg: 

   - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
   - Välj **Skapa ny** och ange namnet på en resursgrupp. 

   Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md). 

1. Välj **V2** för **versionen**. 
1. Välj **plats** för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan. 
1. Välj **fäst till instrumentpanelen**. 
1. Klicka på **Skapa**. 
1. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. När datafabriken har skapats visas sidan **Datafabrik** som på bilden. 

    ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Klicka på **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Etablera en Azure SSIS-integreringskörning 
1. Klicka på ikonen för att **konfigurera en SSIS-integreringskörning** på sidan för att komma igång. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Gör följande på sidan **Allmänna inställningar** i **installationsprogrammet för Integration Runtime**: 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. För **Namn**, ange namnet på din integreringsruntime. 

    b. För **beskrivning** anger du en beskrivning av integrationskörningen. 

    c. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB. 

    d. För **Nodstorlek** välj storleken på noderna i ditt integreringskörningskluster. Endast nodstorlekar som stöds visas. Välj en stor nodstorlek (skalas upp) om du vill köra många beräknings/minnesintensiva paket. 

    e. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med flera noder (skalbart), om du vill köra många paket parallellt. 

    f. I rutan för **version/licens** väljer du SQL Server-versionen eller -licensen för din integreringskörning: Standard eller Enterprise. Välj Enterprise, om du vill använda avancerade/premiumfunktioner på din integrationskörning. 

    g. I rutan **Spara pengar** väljer du alternativet Azure Hybrid-förmån (AHB) för din integreringskörning: Ja eller Nej. Välj Ja om du vill ta med din egen SQL Server-licens med programvarugaranti för att dra nytta av kostnadsbesparingar med hybridanvändning. 

    h. Klicka på **Nästa**. 

1. Gör följande på sidan **SQL-inställningar**: 

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

    b. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning. 

    c. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. 

    d. För kryssrutan **Använd AAD-autentisering** väljer du den autentiseringsmetod för databasservern som ska vara värd för SSISDB: SQL- eller Azure Active Directory (AAD) med den hanterade identitet för din Azure Data Factory. Om du markerar det, du måste lägga till den hanterade identitet för din ADF i en AAD-grupp med behörigheter för åtkomst till databasservern, se [aktiverar AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. För **Admin Username**, ange användarnamn för SQL-autentisering för databasservern som är värd för SSISDB. 

    f. För **Admin-lösenord**, ange ett lösenord för SQL-autentisering för databasservern som är värd för SSISDB. 

    g. För **tjänstnivån för katalogdatabaser** väljer du tjänstnivån för databasservern som ska vara värd för SSISDB: Basic-, Standard- eller Premium-nivån eller namnet på den elastiska poolen. 

    h. Klicka på **Testa anslutning** och om det lyckas, klickar du på **Nästa**. 

1.  Gör följande på sidan **Avancerade inställningar**: 

    ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. För **maximala parallella körningar per nod** väljer du det maximala antalet paket som ska köras samtidigt per nod i ditt integreringskörningskluster. Endast paketnummer som stöds visas. Välj ett låga tal, om du vill använda mer än en kärna för att köra en enda stor/aktiverad paket som är beräkning/minne-intensiva. Välj ett stort antal om du vill köra ett eller flera små/lätta paket på en enda kärna. 

    b. För **anpassad inställningscontainer för SAS URI** kan du också ange åtkomst med delad signatur (SAS) URI (identifierare för uniform resurs) för Azure Storage Blob-containern där ditt installationsskript och dess associerade filer lagras. Se [Anpassad installation för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

1. På **välja ett virtuellt nätverk...**  kryssrutan väljer du om du vill ansluta till din integration runtime till ett virtuellt nätverk. Kolla om du använder Azure SQL Database med virtuellt nätverk tjänstens slutpunkter/hanterad instans som värd för SSISDB eller kräver åtkomst till lokala data. det vill säga du har lokala datakällor /-mål i dina SSIS-paket, se [ansluta till Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Om du markerar den utför du följande steg: 

   ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. För **prenumeration**, Välj den prenumeration som har det virtuella nätverket. 

    b. För **plats**, på samma plats på din integration runtime har valts. 

    c. För **typ**, Välj typ av det virtuella nätverket: Klassisk eller Azure Resource Manager. Vi rekommenderar att du väljer Azure Resource Manager virtuellt nätverk, sedan klassiska virtuella nätverket upphör att gälla snart. 

    d. För **namn på virtuellt nätverk**, Välj namnet på det virtuella nätverket. Den här virtuella nätverket måste finnas i samma virtuella nätverk som används för Azure SQL Database med virtuellt nätverk tjänstens slutpunkter/hanterad instans som värd för SSISDB och eller ett som är anslutet till ditt lokala nätverk. 

    e. För **Undernätsnamn**, Välj namnet på undernätet för det virtuella nätverket. Det bör vara ett annat undernät än den som används för hanterad instans som värd för SSISDB. 

1. Klicka på **VNet verifiering** och om det lyckas klickar du på **Slutför** att starta skapandet av din Azure-SSIS integration runtime. 

    > [!IMPORTANT]
    > - Den här processen tar cirka 20 till 30 minuter att slutföra
    > - Data Factory-tjänsten ansluts till din Azure SQL Database för att förbereda SSISDB. Dessutom konfigurerar behörigheter och inställningar för det virtuella nätverket, om anges, och ansluter den nya instansen av Azure-SSIS integration runtime till det virtuella nätverket. 

1. I fönstret **Anslutningar** växlar du till **integreringskörningar** om det behövs. Om du vill uppdatera statusen klickar du på **Uppdatera**. 

   ![Skapandestatus](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna under **åtgärder** kolumnen för att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Azure SSIS IR – åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integreringskörningar i portalen
1. I användargränssnittet för Azure Data Factory växlar du till fliken **Redigera**, klickar på **Anslutningar**, sedan växlar du till fliken **integreringskörningar** för att se befintliga integreringskörningar i din datafabrik. 

   ![Visa befintliga IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Klicka på **Ny** för att skapa en ny Azure-SSIS IR. 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. För att skapa en Azure-SSIS-integreringskörning klickar du på **Ny**, som du kan se i bilden. 
1. I installationsfönstret för Integration Runtime väljer du **Lift-and-shift existing SSIS packages to execute in Azure** (Lift and Shift-hantera befintliga SSIS-paket för att köra i Azure) och klicka sedan på **Nästa**. 

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure SSIS-integreringskörning. 

## <a name="azure-powershell"></a>Azure PowerShell
I det här avsnittet använder du Azure PowerShell till att skapa en Azure-SSIS IR.

### <a name="create-variables"></a>Skapa variabler
Definiera variabler för användning i skripten i den här självstudien:

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="log-in-and-select-subscription"></a>Logga in och välj en prenumeration
Lägg till följande kod till skriptet för att logga in och välj din Azure-prenumeration: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Verifiera anslutningen till databasen
Lägg till följande skript för att verifiera din slutpunkt för Azure SQL Database-server. 

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
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
}
```

### <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Lägg till följande skript för att automatiskt konfigurera behörigheter/inställningar för virtuella nätverk som din Azure-SSIS Integration Runtime kan anslutas till.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Skapa en datafabrik genom att köra följande kommandon.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Skapa Integration Runtime
Kör följande kommandon för att skapa en Azure-SSIS integration runtime som kör SSIS-paket i Azure. 

Om du inte använder Azure SQL Database med virtuellt nätverk tjänstens slutpunkter/hanterad instans som värd för SSISDB inte kräver åtkomst till lokala data, kan du utelämna parametrarna VNetId och undernät eller skicka tomma värden för dessa. Annars kan du kan inte utelämna dem och skicka giltiga värden från din konfiguration av virtuellt nätverk, se [ansluta till Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Om du använder hanterad instans som värd för SSISDB du utelämnar CatalogPricingTier parametern eller skicka ett tomt värde för den. I annat fall du kan inte utelämna den och skicka ett giltigt värde i listan med prisnivåer för Azure SQL Database som stöds, se [SQL Database-resursgränser](../sql-database/sql-database-resource-limits.md). 

Om du använder Azure Active Directory (AAD)-autentisering med den hanterade identitet för din Azure Data Factory för att ansluta till databasservern, du kan utelämna CatalogAdminCredential parametern, men du måste lägga till den hanterade identitet för din ADF i en AAD-grupp med åtkomstbehörigheter till databasservern, se [aktiverar AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Annars kan inte utelämna den och måste ange ett giltigt objekt som skapats på rätt sätt från dina server administratörens användarnamn och lösenord för SQL-autentisering.

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

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
Här är fullständigt skript som skapar en Azure-SSIS integration runtime. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
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
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall
I det här avsnittet använder du Azure Resource Manager-mallen för att skapa Azure-SSIS integration runtime. Här är en genomgång av exemplet: 

1. Skapa en JSON-fil med följande Azure Resource Manager-mall. Ersätt värdena i vinkelparenteser (platshållare) med egna värden. 

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
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
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
    
1. För att distribuera Azure Resource Manager-mallen, kör New-AzureRmResourceGroupDeployment kommando som du ser i följande exempel, där ADFTutorialResourceGroup är namnet på resursgruppen och ADFTutorialARM.json är den fil som innehåller JSON definitionen för din data factory och Azure-SSIS IR. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Det här kommandot skapar din data factory och Azure-SSIS IR i den, men den startar inte i IR. 

1. Starta din Azure-SSIS IR genom att köra Start-AzureRmDataFactoryV2IntegrationRuntime kommando: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera dina SSIS-paket till Azure. Ansluta till databasservern som är värd för SSIS-katalogen (SSISDB). Namnet på databasservern är i formatet: &lt;Azure SQL Database-servernamn&gt;. database.windows.net eller &lt;namn på hanterad instans. DNS-prefix&gt;. database.windows.net. I artikeln om att [distribuera paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) finns det instruktioner. 

## <a name="next-steps"></a>Nästa steg
Finns i andra Azure-SSIS IR-avsnitt i den här dokumentationen: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integreringskörningar i allmänhet, inklusive Azure-SSIS IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller information om att ansluta till din Azure-SSIS IR till ett Azure-nätverk. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 
