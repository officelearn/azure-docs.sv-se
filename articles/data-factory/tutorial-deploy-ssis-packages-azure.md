---
title: Etablera en Azure SSIS-integreringskörning | Microsoft Docs
description: Lär dig hur du etablerar Azure SSIS-integreringskörning i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484800"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Etablera en Azure-SSIS-integreringskörning i Azure Data Factory

Den här självstudien innehåller steg för att använda Azure-portalen för att etablera en Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) i Azure Data Factory (ADF). Azure-SSIS IR stöder paket distribueras till SSIS-katalogen (SSISDB) som värd för Azure SQL Database-server/hanterad instans (projekt-distributionsmodell) och de distribueras till system/fil resurser/Azure Files (paket-Distributionsmodellen). När Azure-SSIS IR har etablerats kan du sedan använda välbekanta verktyg, till exempel SQL Server Data Tools (SSDT) / SQL Server Management Studio (SSMS) och kommandot rad verktyg, till exempel `dtinstall` / `dtutil` / `dtexec`till distribuera och köra dina paket i Azure. Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Etablering av en Azure-SSIS-integreringskörning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- **Azure SQL Database-server (valfritt)** . Om du inte redan har en databasserver kan du skapa en Azure-portalen innan du sätter igång. ADF skapar i sin tur SSISDB på den här databasservern. Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att integration runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. 
    - Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Vägledning i att välja vilken typ av database-server som värd för SSISDB finns i [jämför Azure SQL Database enkel databas/elastisk pool/hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Om du använder Azure SQL Database-server med virtuellt nätverk tjänstens slutpunkter/hanterad instans i ett virtuellt nätverk som värd för SSISDB, eller kräver åtkomst till lokala data, måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk finns i [skapa Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Detta gäller inte när du använder Azure SQL Database-server med virtuellt nätverk tjänstens slutpunkter/hanterad instans i ett virtuellt nätverk som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). För att aktivera den här inställningen med hjälp av PowerShell, se [New AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Lägg till IP-adressen för klientdatorn eller ett intervall med IP-adresser som innehåller IP-adressen för klientdatorn till klientens IP-adresslista i brandväggsinställningarna för databasservern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).
    - Du kan ansluta till databasservern med SQL-autentisering med dina autentiseringsuppgifter som serveradministratör eller Azure Active Directory (AAD)-autentisering med den hanterade identitet för din ADF. I det senare måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med behörigheter för åtkomst till databasservern. Se [skapa Azure-SSIS-IR med AAD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Bekräfta att din database-server inte har en SSISDB redan. Etablering av en Azure-SSIS IR stöder inte med hjälp av en befintlig SSISDB.

> [!NOTE]
> - En lista över Azure-regioner där ADF och Azure-SSIS IR är tillgängliga för närvarande finns i [ADF + SSIS IR tillgänglighet efter region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome. 
1. Logga in på [Azure Portal](https://portal.azure.com/). 
1. Välj **Nytt** på den vänstra menyn, välj **Data och analys** och välj sedan **Data Factory**. 

   ![Valet Data Factory i fönstret Nytt](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. På sidan **Ny datafabrik** anger du **MyAzureSsisDataFactory** under **Namn**. 

   ![Sidan Ny datafabrik](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Namnet på Azure Data Factory måste vara *globalt unikt*. Om följande fel returneras ändrar du namnet på datafabriken (till exempel **&lt;dittnamn&gt;MyAzureSsisDataFactory**) och provar att skapa fabriken igen. Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](naming-rules.md). 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**. 
1. Gör något av följande för **Resursgrupp**: 

   - Välj **Använd befintlig** och välj en befintlig resursgrupp från listan. 
   - Välj **Skapa ny** och ange namnet på en resursgrupp. 

   Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/resource-group-overview.md). 
1. För **Version** väljer du **V2 (förhandsversion)** . 
1. För **Plats** väljer du en plats för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan. 
1. Välj **fäst till instrumentpanelen**. 
1. Välj **Skapa**. 
1. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**: 

   ![Panelen Distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. När datafabriken har skapats visas sidan **Datafabrik**. 

   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Välj **Författa och övervaka** för att öppna användargränssnittet för Data Factory på en separat flik. 

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Översikt från Data Factory

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

### <a name="from-the-authoring-ui"></a>Från författargränssnittet

1. I användargränssnittet för Azure Data Factory växlar du till fliken **Redigera**, väljer **Anslutningar** och växlar sedan till fliken **Integreringskörningar** för att se befintliga integreringskörningar i din datafabrik. 

   ![Alternativ för att visa befintliga integreringskörningar](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Välj **Ny** för att skapa en Azure-SSIS IR. 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. I **installationsfönstret för Integration Runtime** väljer du **Lift-and-shift existing SSIS packages to execute in Azure** (Lift and Shift-hantera befintliga SSIS-paket för att köra i Azure) och väljer sedan **Nästa**. 

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

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

   a. På **skapa SSIS-katalog...**  markerar du kryssrutan väljer distributionen modell för paket som ska köras på din Azure-SSIS IR: Distributionsmodell för projekt där paket distribueras till SSISDB som värd för din databasserver eller paket-modellen där paket distribueras till din fil system/fil resurser/Azure-filer. Om du markerar det behöver du ta med din egen database-server som värd för SSISDB som vi ska skapa och hantera å dina vägnar.
   
   b. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

   c. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning. 

   d. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Vägledning i att välja vilken typ av database-server som värd för SSISDB finns i [jämför Azure SQL Database enkel databas/elastisk pool/hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Om du väljer Azure SQL Database-server med virtuellt nätverk tjänstens slutpunkter/hanterad instans i ett virtuellt nätverk som värd för SSISDB, eller kräver åtkomst till lokala data, måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk finns i [skapa Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. För kryssrutan **Använd AAD-autentisering** väljer du den autentiseringsmetod för databasservern som ska vara värd för SSISDB: SQL-autentisering eller AAD-autentisering med den hanterade identitet för din ADF. Om du markerar det, du måste lägga till den hanterade identitet för din ADF i en AAD-grupp med behörigheter för åtkomst till din databasserver, se [skapa Azure-SSIS IR med AAD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. För **Admin Username**, ange användarnamn för SQL-autentisering för databasservern som är värd för SSISDB. 

   g. För **Admin-lösenord**, ange ett lösenord för SQL-autentisering för databasservern som är värd för SSISDB. 

   h. För **tjänstnivån för katalogdatabaser** väljer du tjänstnivån för databasservern som ska vara värd för SSISDB: Basic-, Standard- eller Premium-nivån eller namnet på den elastiska poolen. 

   i. Klicka på **Testa anslutning** och om det lyckas, klickar du på **Nästa**. 

1. Gör följande på sidan **Avancerade inställningar**: 

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. För **maximala parallella körningar per nod** väljer du det maximala antalet paket som ska köras samtidigt per nod i ditt integreringskörningskluster. Endast paketnummer som stöds visas. Välj ett lågt värde om du vill använda flera kärnor för att köra ett enda stort/tungt paket som är beräknings-/minnesintensivt. Välj ett stort antal om du vill köra ett eller flera små/lätta paket på en enda kärna. 

   b. För **anpassad inställningscontainer för SAS URI** kan du också ange åtkomst med delad signatur (SAS) URI (identifierare för uniform resurs) för Azure Storage Blob-containern där ditt installationsskript och dess associerade filer lagras. Se [Anpassad installation för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Med kryssrutan **Välj ett virtuellt nätverk...**  väljer du om du vill ansluta din integreringskörning till ett virtuellt nätverk. Du bör kontrollera det om du använder Azure SQL Database-server med virtuellt nätverk tjänstens slutpunkter/hanterad instans i ett virtuellt nätverk som värd för SSISDB eller kräver åtkomst till lokala data i [skapa Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Klicka på **Slutför** för att börja skapa Azure SSIS-integreringskörningen. 

   > [!NOTE]
   > Den här processen ska slutföras inom 5 minuter exklusive helst anpassad installation.
   >
   > Om du använder SSISDB ansluter ADF service till din databasserver för att förbereda SSISDB. 
   > 
   > När du etablerar en Azure-SSIS IR installeras också Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter upprättar en anslutning till Excel/Access-filer och olika Azure-datakällor, utöver de datakällor som redan stöds av inbyggda komponenterna. Du kan också installera ytterligare komponenter, finns i [anpassad konfiguration för Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen. 

   ![Skapandestatus med knappen ”Uppdatera”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Länkar i kolumnen Åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

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
> * Etablering av en Azure-SSIS-integreringskörning.
> * Distribuera SSIS-paket

Läs om hur du anpassar din Azure-SSIS-integreringskörning i följande artikel: 

> [!div class="nextstepaction"]
> [Anpassa Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)