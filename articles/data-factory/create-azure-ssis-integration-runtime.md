---
title: Skapa Azure-SSIS Integration Runtime i Azure Data Factory | Microsoft Docs
description: Lär dig hur du skapar Azure-SSIS Integration Runtime i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010041"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Skapa Azure-SSIS Integration Runtime i Azure Data Factory

Den här självstudien innehåller steg för att tillhandahålla en Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) i Azure Data Factory (ADF). Azure-SSIS IR stöder körning av paket som distribuerats till SSIS-katalogen (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans (projekt distributions modell) och de som distribueras i fil system/fil resurser/Azure Files (paket distributions modell). När Azure-SSIS IR har tillhandahållits kan du sedan använda välbekanta verktyg, till exempel SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) och kommando rads verktyg, `dtinstall`till exempel / `dtutil` / `dtexec`för att distribuera och kör dina paket i Azure.

[Självstudien: Etablering Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) visar hur du skapar ett Azure-SSIS IR via Azure Portal/ADF-appen och om du vill kan du använda Azure SQL Database Server/hanterad instans som värd för SSISDB. Den här artikeln expanderar i självstudien och visar hur du gör följande saker:

- Du kan också använda Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB. Som en förutsättning måste du konfigurera behörigheter för virtuella nätverk/inställningar för din Azure-SSIS IR att ansluta till ett virtuellt nätverk.

- Du kan också använda Azure Active Directory (AAD)-autentisering med den hanterade identiteten för din ADF för att ansluta till Azure SQL Database Server/hanterad instans. Som en förutsättning måste du lägga till den hanterade identiteten för din ADF som en databas användare som kan skapa SSISDB.

- Du kan också ansluta Azure-SSIS IR till ett virtuellt nätverk/konfigurera egen IR som en proxy för din Azure-SSIS IR för att komma åt data lokalt.

## <a name="overview"></a>Översikt

Den här artikeln visar olika sätt att tillhandahålla Azure-SSIS IR:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager-mall](#azure-resource-manager-template)

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte redan har en prenumeration kan du skapa ett [kostnads fritt utvärderings](https://azure.microsoft.com/pricing/free-trial/) konto.
- **Azure SQL Database Server/hanterad instans (valfritt)** . Om du inte redan har en databas server skapar du en i Azure Portal innan du börjar. ADF skapar SSISDB på den här databas servern. Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Med den här konfigurationen kan integrerings körningens Skriv körnings loggar i SSISDB utan att korsa Azure-regioner. 
    - Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämför Azure SQL Database enskild databas/elastisk pool/hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Om du använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB eller behöver åtkomst till lokala data utan att konfigurera egen IR-anslutning måste du ansluta till Azure-SSIS IR till ett virtuellt nätverk, Se [anslut Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Detta gäller inte när du använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Lägg till IP-adressen för klient datorn eller ett intervall med IP-adresser som innehåller IP-adressen för klient datorn till klientens IP-adress lista i brand Väggs inställningarna för databas servern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).
    - Du kan ansluta till databas servern med hjälp av SQL-autentisering med autentiseringsuppgifterna för Server administratör eller Azure Active Directory (AAD) med den hanterade identiteten för din ADF.  För den senare måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med åtkomst behörighet till databas servern, se [Aktivera AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Bekräfta att din databas server inte har någon SSISDB redan. Etableringen av en Azure-SSIS IR stöder inte användning av en befintlig SSISDB.
- **Azure Resource Manager virtuellt nätverk (valfritt)** . Du måste ha ett Azure Resource Manager virtuellt nätverk om minst ett av följande villkor är uppfyllt:
    - Du är värd för SSISDB på Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterad instans med en privat slut punkt.
    - Du vill ansluta till lokala data lager från SSIS-paket som körs på din Azure-SSIS IR utan att konfigurera IR med egen värd.
- **Azure PowerShell (valfritt)** . Följ anvisningarna för [hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/install-az-ps), om du vill köra ett PowerShell-skript för att etablera Azure-SSIS IR.

### <a name="region-support"></a>Regions stöd

För en lista över Azure-regioner där ADF och Azure-SSIS IR för närvarande är tillgängliga, se [ADF + SSIS IR-tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Jämför SQL Database enskild databas/elastisk pool och SQL Database Hanterad instans

I följande tabell jämförs vissa funktioner i Azure SQL Database Server och hanterad instans som de är relaterade till Azure-SSIR IR:

| Funktion | enkel databas/elastisk pool| Managed Instance |
|---------|--------------|------------------|
| **Tids** | SQL Server Agent är inte tillgängligt.<br/><br/>Se [Schemalägga en paket körning i ADF-pipeline](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agenten för hanterade instanser är tillgänglig. |
| **Autentisering** | Du kan skapa SSISDB med en innesluten databas användare som representerar en AAD-grupp med den hanterade identiteten för din ADF som medlem i rollen **db_owner** .<br/><br/>Se [Aktivera Azure AD-autentisering för att skapa SSISDB i Azure SQL Database Server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Du kan skapa SSISDB med en innesluten databas användare som representerar den hanterade identiteten för din ADF. <br/><br/>Se [Aktivera Azure AD-autentisering för att skapa SSISDB i Azure SQL Database hanterade instansen](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Tjänstenivå** | När du skapar Azure-SSIS IR med din Azure SQL Database-Server kan du välja tjänst nivå för SSISDB. Det finns flera tjänst nivåer. | När du skapar Azure-SSIS IR med din hanterade instans kan du inte välja tjänst nivå för SSISDB. Alla databaser i den hanterade instansen delar samma resurs som allokeras till den instansen. |
| **Virtuellt nätverk** | Har endast stöd för Azure Resource Manager virtuella nätverk som Azure-SSIS IR att ansluta till om du använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk eller behöver åtkomst till lokala data lager utan att konfigurera egen IR med egen värd. | Har endast stöd för Azure Resource Manager virtuella nätverk som Azure-SSIS IR att ansluta till. Det virtuella nätverket krävs om du inte aktiverar en offentlig slut punkt för din hanterade instans.<br/><br/>Om du ansluter din Azure-SSIS IR till samma virtuella nätverk som din hanterade instans kontrollerar du att Azure-SSIS IR finns i ett annat undernät än din hanterade instans. Om du ansluter din Azure-SSIS IR till ett annat virtuellt nätverk än din hanterade instans, rekommenderar vi antingen en virtuell nätverks-peering eller ett virtuellt nätverk till en virtuell nätverks anslutning. Se [ansluta ditt program till Azure SQL Database Hanterad instans](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuerade transaktioner** | Stöds via elastiska transaktioner. Microsoft koordinator för distribuerad transaktion-transaktioner (MSDTC) stöds inte. Om dina SSIS-paket använder MSDTC för att koordinera distribuerade transaktioner bör du överväga att migrera till elastiska transaktioner för Azure SQL Database. Mer information finns i [distribuerade transaktioner i moln databaser](../sql-database/sql-database-elastic-transactions-overview.md). | Stöds ej. |
| | | |

## <a name="azure-portal"></a>Azure Portal

I det här avsnittet använder du Azure Portal, särskilt användar gränssnitts/app för ADF, för att skapa Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Om du vill skapa en ADF via Azure Portal följer du anvisningarna i [skapa ADF via användar gränssnitts](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) artikel och väljer **Fäst på instrument panelen** när du gör det för att få snabb åtkomst efter att den har skapats. 

När din ADF har skapats öppnar du dess översikts sida på Azure Portal och klickar på panelen **författare & Monitor** för att öppna sidan för att **komma igång** på en separat flik där du kan fortsätta att skapa din Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Etablera en Azure SSIS-integreringskörning

1. På sidan **nu sätter vi igång** klickar du på panelen **Konfigurera SSIS integration runtime** .

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Gör följande på sidan **Allmänna inställningar** i **installationsprogrammet för Integration Runtime**:

   ![Almänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. För **Namn**, ange namnet på din integreringsruntime.

    b. För **beskrivning** anger du en beskrivning av integrationskörningen.

    c. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB.

    d. För **Nodstorlek** välj storleken på noderna i ditt integreringskörningskluster. Endast nodstorlekar som stöds visas. Välj en stor nodstorlek (skalas upp) om du vill köra många beräknings/minnesintensiva paket.

    e. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med flera noder (skalbart), om du vill köra många paket parallellt.

    f. I rutan för **version/licens** väljer du SQL Server-versionen eller -licensen för din integreringskörning: Standard eller Enterprise. Välj Enterprise, om du vill använda avancerade/premiumfunktioner på din integrationskörning.

    g. I rutan **Spara pengar** väljer du alternativet Azure Hybrid-förmån (AHB) för din integreringskörning: Ja eller Nej. Välj Ja om du vill ta med din egen SQL Server-licens med programvarugaranti för att dra nytta av kostnadsbesparingar med hybridanvändning.

    h. Klicka på **Nästa**.

3. Gör följande på sidan **SQL-inställningar**:

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. I kryss rutan **skapa SSIS Catalog...** väljer du distributions modell för paket som ska köras på din Azure-SSIS IR: Projekt distributions modell där paket distribueras till SSISDB som hanteras av din databas server eller paket distributions modell där paket distribueras till fil systemen/fil resurserna/Azure Files. Om du markerar det måste du ta med din egen databas server som värd för SSISDB som vi ska skapa och hantera för din räkning.
   
    b. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

    c. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning. 

    d. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämför Azure SQL Database enskild databas/elastisk pool/hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Om du väljer Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB eller behöver åtkomst till lokala data utan att konfigurera egen IR för egen värd, måste du ansluta till Azure-SSIS IR till ett virtuellt nätverk , se [anslut Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. För kryssrutan **Använd AAD-autentisering** väljer du den autentiseringsmetod för databasservern som ska vara värd för SSISDB: SQL-autentisering eller AAD-autentisering med den hanterade identiteten för din ADF. Om du markerar det måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med åtkomst behörighet till din databas server, se [Aktivera AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. För **Admin Username**, ange användarnamn för SQL-autentisering för databasservern som är värd för SSISDB. 

    g. För **Admin-lösenord**, ange ett lösenord för SQL-autentisering för databasservern som är värd för SSISDB. 

    h. För **tjänstnivån för katalogdatabaser** väljer du tjänstnivån för databasservern som ska vara värd för SSISDB: Basic-, Standard- eller Premium-nivån eller namnet på den elastiska poolen. 

    i. Klicka på **Testa anslutning** och om det lyckas, klickar du på **Nästa**. 

4. Gör följande på sidan **Avancerade inställningar**:

    ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. För **maximala parallella körningar per nod** väljer du det maximala antalet paket som ska köras samtidigt per nod i ditt integreringskörningskluster. Endast paketnummer som stöds visas. Välj ett lågt nummer om du vill använda mer än en kärna för att köra ett enda stort eller tungt viktat paket som är beräkning/minnes intensiv. Välj ett stort antal om du vill köra ett eller flera små/lätta paket på en enda kärna.

    b. För **anpassad inställningscontainer för SAS URI** kan du också ange åtkomst med delad signatur (SAS) URI (identifierare för uniform resurs) för Azure Storage Blob-containern där ditt installationsskript och dess associerade filer lagras. Se [Anpassad installation för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Välj om du vill ansluta till integrerings körningen till ett virtuellt nätverk i kryss rutan **Välj ett virtuellt nätverk...** . Kontrol lera det om du använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB eller kräver åtkomst till lokala data, d.v.s. du har lokala data källor/mål i dina SSIS-paket, utan Konfigurera en lokal IR-installation, se [anslut Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Om du markerar den utför du följande steg:

   ![Avancerade inställningar med virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. För **prenumeration**väljer du den Azure-prenumeration som har ditt virtuella nätverk.

    b. För **plats**är samma plats för integrerings körningen markerad.

    c. I **typ**väljer du typ av virtuellt nätverk: Klassisk eller Azure Resource Manager. Vi rekommenderar att du väljer Azure Resource Manager virtuellt nätverk, eftersom det klassiska virtuella nätverket kommer att bli inaktuellt snart.

    d. För **VNet-namn**väljer du namnet på ditt virtuella nätverk. Det här virtuella nätverket bör vara samma som används för Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser i ett virtuellt nätverk som värd för SSISDB eller det som är anslutet till ditt lokala nätverk.

    e. För **under näts namn**väljer du namnet på under nätet för ditt virtuella nätverk. Detta bör vara ett annat undernät än det som används för hanterade instanser i ett virtuellt nätverk som värd för SSISDB.

6. I kryss rutan **Konfigurera egen värd...** väljer du om du vill konfigurera den lokala IR-installationen som en proxy för din Azure-SSIS IR, se [Konfigurera egen värd-IR som proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Om du markerar den utför du följande steg:

   ![Avancerade inställningar med IR med egen värd](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. För **integration runtime med egen värd**väljer du din befintliga IR som en proxy för Azure-SSIS IR.

    b. För den **länkade lagrings tjänsten för lagring**väljer du den befintliga Azure Blob Storage-länkade tjänsten eller skapar en ny för mellanlagring.

    c. För **mellanlagringsplatsen**anger du en BLOB-behållare i den valda Azure-Blob Storage eller låter den vara tom om du vill använda en standard för mellanlagring.

7. Klicka på **VNet-verifiering** och eller **Nästa**. 

8. På sidan **Sammanfattning** granskar du alla inställningar för etablering, bok märken rekommenderade dokumentations länkar och klickar på **Slutför** för att starta skapandet av integration Runtime.

    > [!NOTE]
    > Om du exkluderar en anpassad konfigurations tid bör den här processen slutföras inom 5 minuter, men det kan ta cirka 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk.
    >
    > Om du använder SSISDB kommer ADF-tjänsten att ansluta till din databas server för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och kopplar Azure-SSIS IR till det virtuella nätverket.
    > 
    > När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel/Access-filer och olika Azure-datakällor, utöver de data källor som redan stöds av inbyggda komponenter. Du kan också installera ytterligare komponenter, se [anpassad installation för Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. I fönstret **Anslutningar** växlar du till **integreringskörningar** om det behövs. Om du vill uppdatera statusen klickar du på **Uppdatera**.

   ![Skapandestatus](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Använd länkarna under kolumnen **åtgärder** för att stoppa/starta, redigera eller ta bort integrerings körningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats.

   ![Azure SSIS IR – åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integreringskörningar i portalen

1. I användargränssnittet för Azure Data Factory växlar du till fliken **Redigera**, klickar på **Anslutningar**, sedan växlar du till fliken **integreringskörningar** för att se befintliga integreringskörningar i din datafabrik.

   ![Visa befintlig IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Klicka på **Ny** för att skapa en ny Azure-SSIS IR.

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. För att skapa en Azure-SSIS-integreringskörning klickar du på **Ny**, som du kan se i bilden.

4. I installationsfönstret för Integration Runtime väljer du **Lift-and-shift existing SSIS packages to execute in Azure** (Lift and Shift-hantera befintliga SSIS-paket för att köra i Azure) och klicka sedan på **Nästa**.

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure SSIS-integreringskörning.

## <a name="azure-powershell"></a>Azure PowerShell

I det här avsnittet ska du använda Azure PowerShell för att skapa en Azure-SSIS IR.

### <a name="create-variables"></a>Skapa variabler

Kopiera och klistra in följande skript – ange värden för variablerna. 

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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Logga in och välj prenumeration

Lägg till följande kod till skriptet för att logga in och välj din Azure-prenumeration:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Verifiera anslutningen till databas servern

Lägg till följande skript för att verifiera Azure SQL Database Server/hanterad instans.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../azure-resource-manager/resource-group-overview.md) med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

Om din resurs grupp redan finns kopierar du inte den här koden till skriptet. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Skapa en datafabrik genom att köra följande kommandon.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Skapa Integration Runtime

Kör följande kommandon för att skapa en Azure-SSIS integration runtime som kör SSIS-paket i Azure.

Om du inte använder SSISDB kan du utelämna parametrarna CatalogServerEndpoint, CatalogPricingTier och CatalogAdminCredential.

Om du inte använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB eller behöver åtkomst till lokala data, kan du utelämna parametrarna VNetId och Subnet eller skicka tomma värden för dem. Du kan också utelämna dem om du konfigurerar IR med egen värd som en proxy för din Azure-SSIS IR att komma åt data lokalt. Annars kan du inte utelämna dem och måste skicka giltiga värden från konfigurationen av det virtuella nätverket, se [ansluta Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Om du använder hanterad instans som värd för SSISDB kan du utelämna parametern CatalogPricingTier eller skicka ett tomt värde för den. Annars kan du inte utelämna det och måste skicka ett giltigt värde från listan över pris nivåer som stöds för Azure SQL Database, se [SQL Database resurs gränser](../sql-database/sql-database-resource-limits.md).

Om du använder Azure Active Directory (AAD)-autentisering med den hanterade identiteten för din ADF för att ansluta till databas servern kan du utelämna parametern CatalogAdminCredential, men du måste lägga till den hanterade identiteten för din ADF i en AAD-grupp med åtkomst behörigheter till databas servern finns i [Aktivera AAD-autentisering för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Annars kan du inte utelämna det och måste skicka ett giltigt objekt som skapats av Server administratörens användar namn och lösen ord för SQL-autentisering.

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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
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

### <a name="start-integration-runtime"></a>Starta Integration Runtime

Kör följande kommandon för att starta integrerings körningen för Azure-SSIS.

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
> Om du exkluderar en anpassad konfigurations tid bör den här processen slutföras inom 5 minuter, men det kan ta cirka 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk.
>
> Om du använder SSISDB kommer ADF-tjänsten att ansluta till din databas server för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och kopplar Azure-SSIS IR till det virtuella nätverket.
> 
> När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel/Access-filer och olika Azure-datakällor, utöver de data källor som redan stöds av inbyggda komponenter. Du kan också installera ytterligare komponenter, se [anpassad installation för Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Fullständigt skript

Här är det fullständiga skriptet som skapar en Azure-SSIS integration Runtime.

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
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
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
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
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

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

I det här avsnittet använder du Azure Resource Manager-mallen för att skapa Azure-SSIS integration Runtime. Här är ett exempel på en genom gång:

1. Skapa en JSON-fil med följande Azure Resource Manager mall. Ersätt värdena inom vinkelparenteser (plats hållare) med dina egna värden.

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
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

2. Om du vill distribuera Azure Resource Manager-mallen kör du kommandot New-AzResourceGroupDeployment som visas i följande exempel, där ADFTutorialResourceGroup är namnet på din resurs grupp och ADFTutorialARM. JSON är den fil som innehåller JSON-definition för din data fabrik och Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Det här kommandot skapar din data fabrik och Azure-SSIS IR, men den startar inte IR.

3. Starta Azure-SSIS IR genom att köra kommandot start-AzDataFactoryV2IntegrationRuntime:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> Om du exkluderar en anpassad konfigurations tid bör den här processen slutföras inom 5 minuter, men det kan ta cirka 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk.
>
> Om du använder SSISDB kommer ADF-tjänsten att ansluta till din databas server för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och kopplar Azure-SSIS IR till det virtuella nätverket.
> 
> När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel/Access-filer och olika Azure-datakällor, utöver de data källor som redan stöds av inbyggda komponenter. Du kan också installera ytterligare komponenter, se [anpassad installation för Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket till den och köra dem på Azure-SSIS IR med hjälp av SSDT/SSMS-verktyg som ansluter till din databas server via dess server slut punkt.  För Azure SQL Database Server/hanterad instans i med en privat slut punkt/hanterad instans med en offentlig slut `<server name>.database.windows.net`punkt är / `<server name>.<dns prefix>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`Server slut punktens format. Om du inte använder SSISDB kan du distribuera dina paket i fil system/fil resurser/Azure Files och köra dem på Azure-SSIS IR med hjälp av `dtinstall` / / `dtutil` `dtexec` kommando rads verktyg. Mer information finns i [distribuera SSIS-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med hjälp av aktiviteten kör SSIS-paket i ADF-pipeline, se [anropa SSIS-paket körning som en första omsluten ADF-aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Nästa steg

Se även andra Azure-SSIS IR ämnen i den här dokumentationen:

- [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integrerings körningar i allmänhet, inklusive Azure-SSIS IR.
- [Övervaka Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar och förstår information om din Azure-SSIS IR.
- [Hantera Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort din Azure-SSIS IR – det visar också hur du skalar upp Azure-SSIS IR genom att lägga till fler noder.
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller information om hur du ansluter till Azure-SSIS IR till ett virtuellt nätverk.