---
title: Skapa Azure-SSIS-integrering körning i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar en Azure-SSIS-integrering körning i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33b24544373bc778f27ef3da18da8d62407a639
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751645"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Skapa Azure-SSIS-integrering körning i Azure Data Factory
Den här artikeln innehåller steg för att etablera en Azure-SSIS-integrering körning i Azure Data Factory. Du kan sedan använda SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera och köra SQL Server Integration Services (SSIS) paket i den här körning i Azure. 

Kursen [genomgång: distribuera paket för SQL Server Integration Services (SSIS) till Azure](tutorial-create-azure-ssis-runtime-portal.md) visar hur du skapar Azure SSIS Integration Runtime (IR) med hjälp av Azure SQL Database som värd för SSIS-katalogen. Den här artikeln kan utökas med kursen och visar hur du göra följande: 

- Du kan också använda Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsversion) som databasserver värd SSIS katalogen (SSIDB-databasen). En förutsättning, behöver du ansluta till Azure-SSIS-IR till ett virtuellt nätverk och konfigurera behörigheter för virtuella nätverk och inställningar som behövs finns [anslutning till Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Du kan också använda Azure Active Directory (AAD) autentisering med din Azure Data Factory hanterade tjänsten identitet (MSI) för Azure-SSIS IR för att ansluta till databasservern. En förutsättning, måste du lägga till din Data Factory MSI i en AAD-grupp med behörigheter för åtkomst till databasservern, se [aktivera AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

> [!NOTE]
> Den här artikeln gäller för version 2 av Data Factory, som för närvarande är en förhandsversion. Om du använder version 1 av Data Factory-tjänsten, som är allmänt tillgänglig, läser du [dokumentationen för Data Factory version 1](v1/data-factory-introduction.md). 

## <a name="overview"></a>Översikt
Den här artikeln beskrivs olika sätt att etablera ett Azure-SSIS-IR: 

- [Azure Portal](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Azure Resource Manager-mall](#azure-resource-manager-template) 

När du skapar en Azure-SSIS-IR ansluter Data Factory till din Azure SQL Database för att förbereda katalog SSIS-databasen (SSISDB). Skriptet också konfigurerar behörigheter och inställningar för det virtuella nätverket om anges och ansluter till den nya instansen av Azure-SSIS-integrering runtime till det virtuella nätverket. 

När du etablerar en instans av en Azure-SSIS IR installeras också Azure Feature Pack för SSIS och Access Redistributable. Med dessa komponenter upprättar du en anslutning till Excel- och Access-filer och till olika Azure-datakällor, utöver de datakällor som stöds av de inbyggda komponenterna. Du kan också installera ytterligare komponenter. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). 

## <a name="prerequisites"></a>Förutsättningar 
- **Azure-prenumeration**. Om du inte har en prenumeration kan du skapa ett [kostnadsfritt utvärderingskonto](http://azure.microsoft.com/pricing/free-trial/). 

- **Azure SQL Database server/hanterad instansen (förhandsgranskning)**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Den här servern är värd för SSISDB (SSIS Catalog Database). Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. Baserat på valda databasservern SSISDB kan skapas för din räkning som en enskild databas, en del av en elastisk Pool eller i en hanterad-instans (förhandsversion) och kan nås i offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. En lista över stöds prisnivåer för Azure SQL Database, se [gränserna för SQL-databas](../sql-database/sql-database-resource-limits.md). 

    Kontrollera att din Azure SQL Database-server eller hanteras instansen (förhandsgranskning) inte redan har en SSIS-katalogen (SSIDB-databasen). När du ska etablera Azure-SSIS IR kan du inte ha någon befintlig SSIS-katalog. 

- **Klassisk eller virtuella nätverk i Azure Resource Manager (valfritt)**. Du måste ha ett virtuellt Azure-nätverk om minst en av följande villkor föreligger: 
    - Du är värd för databasen SSIS-katalog i Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsversion) som är i ett virtuellt nätverk. 
    - Du vill ansluta till lokala datalager från SSIS-paket som körs på Azure-SSIS Integration Runtime. 

- **Azure PowerShell**. Följ instruktionerna i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-azurerm-ps), om du använder PowerShell för att köra ett skript för att etablera Azure SSIS-integrering körningen som körs SSIS-paket i molnet. 

### <a name="region-support"></a>Region-support
Du kan skapa en datafabrik inom följande områden: östra USA, östra USA 2, Sydostasien och västra Europa. 

Du kan skapa en integreringskörning för Azure-SSIS i följande regioner: USA, östra; USA, östra 2; USA, centrala; USA, västra 2; Europa, norra; Europa, västra; Storbritannien, södra och Australien, östra. 

### <a name="compare-sql-database-and-managed-instance-preview"></a>Jämför SQL Database och hanterade instansen (förhandsgranskning)

I följande tabell jämförs vissa funktioner i SQL-databasen och hanteras instansen (förhandsgranskning) i samband med Azure-SSIR IR:

| Funktion | SQL Database | Managed Instance |
|---------|--------------|------------------|
| **Schemaläggning** | SQL Server Agent är inte tillgänglig.<br/><br/>Se [schemalägga ett paket som en del av en Azure Data Factory-pipelinen](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages.md#activity).| SQL Server Agent är tillgänglig. |
| **Autentisering** | Du kan skapa en databas med ett användarkonto för innesluten databas som representerar alla Azure Active Directory-användare i den **dbmanager** roll.<br/><br/>Se [aktivera Azure AD i Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Du kan inte skapa en databas med ett användarkonto för innesluten databas som representerar alla Azure Active Directory-användare än en Azure AD-administratör. <br/><br/>Se [aktivera Azure AD på Azure SQL-hanterade databasinstans](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Tjänstenivå** | När du skapar Azure SSIS-IR på SQL-databas kan du välja tjänstnivån för SSISDB. Det finns flera servicenivåerna. | När du skapar Azure SSIS-IR på en hanterad instans kan välja du inte tjänstnivån för SSISDB. Alla databaser på samma instans som hanterade delar samma resurs tilldelas den instansen. |
| **Virtuellt nätverk** | Stöder både Azure Resource Manager och klassiska virtuella nätverk. | Stöder endast virtuella nätverk i Azure Resource Manager. Virtuellt nätverk måste anges.<br/><br/>Om du ansluter din Azure-SSIS-IR till samma virtuella nätverk som hanteras-instans, se till att Azure-SSIS-IR är i ett annat undernät än hanteras-instans. Om du ansluter Azure SSIS-IR till ett annat virtuellt nätverk än hanteras-instans, rekommenderar vi virtuellt nätverk peering (som är begränsad till samma region) eller ett virtuellt nätverk till virtuellt nätverk. Se [ansluta programmet till Azure SQL-hanterade databasinstans](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuerade transaktioner** | Stöds via elastisk transaktioner. Microsoft Distributed Transaction Coordinator (MSDTC) transaktioner stöds inte. Om paketen använder MSDTC för att koordinera distribuerade transaktioner, bör du du migrera elastisk transaktion för SQL-databas. Mer information finns i [distribuerade transaktioner över moln databaser](../sql-database/sql-database-elastic-transactions-overview.md). | Stöds ej. |
| | | |

## <a name="azure-portal"></a>Azure Portal
I det här avsnittet använder Azure-portalen specifikt Data Factory Gränssnittet för att skapa en Azure-SSIS-IR. 

### <a name="create-a-data-factory"></a>Skapa en datafabrik 
1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome. 
2. Logga in på [Azure-portalen](https://portal.azure.com/). 
3. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 

   ![Nytt->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

4. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** som **namn**. 

   ![Sida för ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnMyAzureSsisDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

5. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
6. För **resursgruppen** utför du något av följande steg: 

   - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan. 
   - Välj **Skapa ny** och ange namnet på en resursgrupp. 

   Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md). 

7. Välj **V2 (förhandsgranskning)** för **versionen**. 
8. Välj **plats** för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan. 
9. Välj **fäst till instrumentpanelen**. 
10. Klicka på **Skapa**. 
11. På instrumentpanelen visas följande panel med statusen: **Distribuerar datafabrik**. 

    ![panelen distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

12. När datafabriken har skapats visas sidan **Datafabrik** som på bilden. 

    ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

13. Klicka på **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Etablera en Azure SSIS-integreringskörning 
1. Klicka på ikonen för att **konfigurera en SSIS-integreringskörning** på sidan för att komma igång. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Gör följande på sidan **Allmänna inställningar** i **installationsprogrammet för Integration Runtime**: 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. För **namn**, ange namnet på din integrering runtime. 

    b. För **beskrivning**, anger du en beskrivning av integration-körning. 

    c. För **plats**, välj platsen för integrering-körning. Endast platser som stöds visas. Vi rekommenderar att du väljer databasservern till värd SSISDB samma plats. 

    d. För **nodstorlek**, Välj storleken på noden i klustret integration runtime. Endast stöds nod visas. Välj en stor nodstorlek (skalas upp) om du vill köra många beräknings-minne – beräkningsintensiva paket. 

    e. För **noden**, Välj antalet noder i klustret integration runtime. Endast stöds nod tal visas. Välj ett stora kluster med flera noder (skalbar), om du vill köra många paket parallellt. 

    f. För **Edition-licens**, Välj SQL Server-version/licens för integrering-runtime: Standard eller Enterprise. Välj Enterprise, om du vill använda avancerade/premium-funktioner på dina integration runtime. 

    g. För **spara pengar**, Azure Hybrid förmånen (AHB) med alternativet för integrering-runtime: Ja eller Nej. Välj Ja om du vill ta med din egen SQL Server-licens med Software Assurance att dra nytta av kostnadsbesparingar med hybrid användning. 

    h. Klicka på **Nästa**. 

3. Gör följande på sidan **SQL-inställningar**: 

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. För **prenumeration**, Välj den Azure-prenumeration som har databasservern till värd SSISDB. 

    b. För **plats**, välj platsen för databasservern till värd SSISDB. Vi rekommenderar att du väljer på samma plats för integrering-körningen. 

    c. För **katalog databasen Server Endpoint**, Välj slutpunkten för databasservern till värd SSISDB. Baserat på valda databasservern SSISDB kan skapas för din räkning som en enskild databas, en del av en elastisk Pool eller i en hanterad-instans (förhandsversion) och kan nås i offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. 

    d. På **Använd AAD-autentisering...**  kryssrutan, Välj autentiseringsmetoden för databasservern till värd SSISDB: SQL- eller Azure Active Directory (AAD) med din Azure Data Factory hanterade tjänsten identitet (MSI). Om du har checkat måste du lägga till din Data Factory MSI i en AAD-grupp med behörigheter för åtkomst till databasservern, se [aktivera AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. För **Admin Username**, ange användarnamn för SQL-autentisering för databasservern till värd SSISDB. 

    f. För **adminlösenord**, ange lösenord för SQL-autentisering för databasservern till värd SSISDB. 

    g. För **katalog databasen tjänstnivån**, Välj tjänstnivå för databasservern till värd SSISDB: Basic/Standard/Premium-nivån eller namn på elastisk Pool. 

    h. Klicka på **Testanslutningen** och om det lyckas, klickar du på **nästa**. 

4.  På den **avancerade inställningar** utför du följande steg: 

    ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. För **maximala parallella körningar Per nod**, Välj det maximala antalet paket som ska köra samtidigt per nod i klustret integration runtime. Stöds endast paketet tal visas. Välj ett lågt värde om du vill använda mer än en kärna för att köra en enda stor/aktiverad beräkning/minne-intensiv. Välj ett stort antal om du vill köra ett eller flera små/inaktiverad paket i en enda kärna. 

    b. För **anpassade installationsprogrammet behållaren SAS URI**, du kan också ange delad signatur åtkomst (SAS) identifierare URI (Uniform Resource) för Azure Storage Blob-behållare där din installationsskriptet och dess associerade filer lagras, se [Anpassad installation för Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

5. På **väljer ett virtuellt nätverk...**  kryssrutan, väljer du om du vill ansluta till din integrering runtime till ett virtuellt nätverk. Kontrollera den om du använder Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsgranskning) att vara värd för SSISDB eller kräver åtkomst till lokala data. det vill säga du har lokala data källor/mål i SSIS-paket, se [ansluta till ett virtuellt nätverk i Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Om du markerar det gör du följande: 

   ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. För **prenumeration**, Välj den Azure-prenumeration som har det virtuella nätverket. 

    b. För **plats**, på samma plats för integrering-körningen är markerad. 

    c. För **typen**, Välj typ av det virtuella nätverket: klassisk eller Azure Resource Manager. Vi rekommenderar att du väljer Azure Resource Manager virtuellt nätverk, eftersom klassiskt virtuellt nätverk att bli inaktuell snart. 

    d. För **VNet namn**, Välj namnet på det virtuella nätverket. Det här virtuella nätverket bör vara samma virtuella nätverk som används för Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsversion) som värd för SSISDB och eller ett som är anslutet till det lokala nätverket. 

    e. För **Undernätsnamn**, Välj namnet på undernät för det virtuella nätverket. Detta bör vara ett annat undernät än den som används för hanterade instansen (förhandsgranskning) till värden SSISDB. 

6. Klicka på **VNet validering** och om det lyckas, klickar du på **Slutför** att starta skapandet av din Azure-SSIS-integrering runtime. 

    > [!IMPORTANT]
    > - Den här processen tar cirka 20 till 30 minuter att slutföra
    > - Data Factory-tjänsten ansluts till din Azure SQL Database för att förbereda SSISDB. Skriptet också konfigurerar behörigheter och inställningar för det virtuella nätverket om anges och ansluter till den nya instansen av Azure-SSIS-integrering runtime till det virtuella nätverket. 

7. I fönstret **Anslutningar** växlar du till **integreringskörningar** om det behövs. Om du vill uppdatera statusen klickar du på **Uppdatera**. 

   ![Skapandestatus](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Använd länkarna under **åtgärder** kolumnen stopp/start, redigera eller ta bort integration körningsmiljön. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Azure SSIS IR – åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integreringskörningar i portalen
1. I användargränssnittet för Azure Data Factory växlar du till fliken **Redigera**, klickar på **Anslutningar**, sedan växlar du till fliken **integreringskörningar** för att se befintliga integreringskörningar i din datafabrik. 

   ![Visa befintliga IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Klicka på **Ny** för att skapa en ny Azure-SSIS IR. 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. För att skapa en Azure-SSIS-integreringskörning klickar du på **Ny**, som du kan se i bilden. 
4. I installationsfönstret för Integration Runtime väljer du **Lift-and-shift existing SSIS packages to execute in Azure** (Lift and Shift-hantera befintliga SSIS-paket för att köra i Azure) och klicka sedan på **Nästa**. 

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure SSIS-integreringskörning. 

## <a name="azure-powershell"></a>Azure PowerShell
I det här avsnittet använda du Azure PowerShell för att skapa en Azure-SSIS-IR.

### <a name="create-variables"></a>Skapa variabler
Definiera variabler för användning i skripten i den här självstudien:

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### <a name="log-in-and-select-subscription"></a>Logga in och välj en prenumeration
Lägg till följande kod skriptet för att logga in och välj din Azure-prenumeration: 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Verifiera anslutningen till databasen
Lägg till följande skript för att verifiera din Azure SQL Database serverslutpunkt. 

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
# Make sure to run this script against the subscription to which the virtual network belongs.
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
Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras som en grupp. 

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
Kör följande kommandon för att skapa en Azure-SSIS-integrering körning som körs SSIS-paket i Azure. 

Om du inte använder Azure SQL Database med virtuella nätverk service slutpunkter/hanterad instansen (förhandsgranskning) att vara värd för SSISDB eller kräver åtkomst till lokala data, kan du utelämna parametrarna VNetId och undernät eller skicka tomma värden för dessa. Annars kan du kan inte utelämna dem och måste skicka giltiga värden från din konfiguration av virtuellt nätverk, se [ansluta till ett virtuellt nätverk i Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Om du använder hanteras instansen (förhandsgranskning) till värden SSISDB du utelämnar CatalogPricingTier parameter eller ange ett tomt värde för den. I annat fall du kan inte utelämna den och skicka ett giltigt värde i listan över prisnivåer för Azure SQL Database som stöds, finns [gränserna för SQL-databas](../sql-database/sql-database-resource-limits.md). 

Om du använder Azure Active Directory (AAD)-autentisering med din Azure Data Factory hanterade tjänsten identitet (MSI) för att ansluta till databasservern, kan du utelämna CatalogAdminCredential parameter, men du måste lägga till din Data Factory MSI i en AAD-grupp med åtkomst behörigheter till databasservern, se [aktivera AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Annars kan inte utelämna den och måste skicka ett giltigt objekt format från servern administratörsanvändarnamnet och lösenordet för SQL-autentisering.

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

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
Här är fullständig skriptet som skapar en Azure-SSIS-integrering körning. 

```powershell
### Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# You can create an Azure-SSIS IR in the following regions: East US, East US 2, Central US, West US 2, North Europe, West Europe, UK South, and Australia East.
$AzureSSISLocation = "EastUS" 
# In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

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
# Make sure to run this script against the subscription to which the virtual network belongs.
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
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

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
I det här avsnittet använder du Azure Resource Manager-mall för att skapa Azure-SSIS-integrering runtime. Här är en exempel-genomgång: 

1. Skapa en JSON-fil med hjälp av följande Azure Resource Manager-mallen. Ersätt värdena i vinkelparenteser (platshållare) med egna värden. 

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
                            "nodeSize": "Standard_D4_v2",
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
    
2. Distribuera Azure Resource Manager-mallen genom att köra New-AzureRmResourceGroupDeployment kommando som visas i följande exempel, innehåller där ADFTutorialResourceGroup är namnet på resursgruppen och ADFTutorialARM.json fil som JSON definitionen för din data factory och Azure-SSIS IR. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Det här kommandot skapar din data factory och Azure-SSIS IR i den, men den startar inte IR. 

3. För att starta Azure-SSIS-IR kör du kommandot Start-AzureRmDataFactoryV2IntegrationRuntime: 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera dina SSIS-paket till Azure. Ansluta till databasservern som är värd för SSIS-katalogen (SSISDB). Namnet på databasservern är i formatet: &lt;Azure SQL Database-servernamn&gt;. database.windows.net eller &lt;hanteras (förhandsgranskning) instansnamn. DNS-prefix&gt;. database.windows.net. I artikeln om att [distribuera paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) finns det instruktioner. 

## <a name="next-steps"></a>Nästa steg
Finns i andra Azure-SSIS-IR-avsnitt i den här dokumentationen: 

- [Azure-SSIS-integrering Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integration körningar i allmänhet inklusive Azure SSIS-IR. 
- [Självstudie: distribuera SSIS-paket till Azure](tutorial-create-azure-ssis-runtime-portal.md). Den här artikeln innehåller stegvisa instruktioner för att skapa en Azure-SSIS IR och använder en Azure SQL-databas som värd för SSIS-katalogen. 
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar information om en Azure-SSIS IR och innehåller beskrivningar av statusar i den returnerade informationen. 
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort en Azure-SSIS IR. Den också visar hur du skalar ut Azure-SSIS IR genom att lägga till fler noder i IR. 
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller information om att ansluta till Azure-SSIS-IR till ett Azure virtual network. Den innehåller också steg för att använda Azure-portalen för att konfigurera ett virtuellt nätverk så att Azure-SSIS IR kan ansluta till ett virtuellt nätverk. 
