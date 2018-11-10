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
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ed14dc45af3f47032e54c946486c4de70aeae11a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214961"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Etablera en Azure-SSIS-integreringskörning i Azure Data Factory
Den här självstudien innehåller steg för att använda Azure Portal för att distribuera en Azure SSIS-integreringskörning (IR) i Azure Data Factory. Sedan kan du använda SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera och köra SQL Server Integration Services-paket (SSIS) till den här körningen i Azure. Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Etablering av en Azure-SSIS-integreringskörning.

## <a name="prerequisites"></a>Nödvändiga komponenter
- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 
- **Azure SQL Database-server**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Azure Data Factory skapar SSIS-katalogen (SSISDB-databasen) på den här databasservern. Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB-databasen utan att korsa Azure-regioner. 
- Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Om du använder Azure SQL Database med virtuella nätverkstjänstslutpunkter/hanterade instanser som värd för SSISDB eller kräver åtkomst till lokala data måste du ansluta till Azure-SSIS-IR till ett virtuellt nätverk. Se[skapa Azure-SSIS-IR på ett virtuell nätverket](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Den här inställningen gäller inte när du använder Azure SQL Database med virtuella nätverkstjänstslutpunkter/hanterade instanser som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
- Lägg till IP-adressen för klientdatorn eller ett intervall med IP-adresser som innehåller IP-adressen för klientdatorn till klientens IP-adresslista i brandväggsinställningarna för databasservern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md). 
- Du kan ansluta till databasservern med hjälp av SQL-autentisering med administratörsautentiseringsuppgifter eller Azure Active Directory (AAD)-autentisering med den hanterade identiteten för din Azure Data Factory (ADF).  I det senare måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med behörigheter för åtkomst till databasservern. Se [skapa Azure-SSIS-IR med AAD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Bekräfta att din Azure SQL Database-server inte har någon SSIS-katalog (SSISDB-databas). När du ska etablera en Azure-SSIS IR kan du inte ha någon befintlig SSIS-katalog. 

> [!NOTE]
> - En lista över Azure-regioner som Data Factory och Azure-SSIS Integration Runtime för närvarande är tillgängliga för finns i [tillgänglighet för ADF + SSIS efter region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome. 
1. Logga in på [Azure-portalen](https://portal.azure.com/). 
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
1. För **Version** väljer du **V2 (förhandsversion)**. 
1. För **Plats** väljer du en plats för datafabriken. Endast de platser som har stöd för att skapa datafabriker visas i listan. 
1. Välj **fäst till instrumentpanelen**. 
1. Välj **Skapa**. 
1. Du ser följande panel på instrumentpanelen med statusen **Distribuerar datafabrik**: 

   ![Panelen Distribuerar datafabrik](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. När datafabriken har skapats visas sidan **Datafabrik**. 

   ![Datafabrikens startsida](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Välj **Författa och övervaka** för att öppna användargränssnittet för Data Factory på en separat flik. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Gör följande på sidan **Allmänna inställningar** i **installationsprogrammet för Integration Runtime**: 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. För **Namn**, ange namnet på din integreringsruntime. 

   b. För **beskrivning** anger du en beskrivning av integrationskörningen. 

   c. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB. 

   d. För **Nodstorlek** välj storleken på noderna i ditt integreringskörningskluster. Endast nodstorlekar som stöds visas. Välj en stor nodstorlek (skalas upp) om du vill köra många beräknings/minnesintensiva paket. 

   e. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med flera noder (skalbart), om du vill köra många paket parallellt. 

   f. För **Utgåva/licens** välj SQL Server-version/licens för din integreringskörning: Standard eller Enterprise. Välj Enterprise, om du vill använda avancerade/premiumfunktioner på din integrationskörning. 

   g. För **Spara pengar**, välj alternativet Azure Hybrid-förmån (AHB) för din integreringskörning: Ja eller Nej. Välj Ja om du vill ta med din egen SQL Server-licens med programvarugaranti för att dra nytta av kostnadsbesparingar med hybridanvändning. 

   h. Klicka på **Nästa**. 

1. Gör följande på sidan **SQL-inställningar**: 

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

   b. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning. 

   c. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. Baserat på den valda databasservern kan SSISDB skapas för din räkning som en fristående databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Anvisningar för att välja vilken typ av databasserver du vill använda som värd för SSISDB finns i [Jämför SQL Database och hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Om du använder Azure SQL Database med virtuella nätverkstjänstslutpunkter/hanterade instanser som värd för SSISDB eller kräver åtkomst till lokala data måste du ansluta till Azure-SSIS-IR till ett virtuellt nätverk. Se [Skapa Azure-SSIS-IR på ett virtuell nätverket](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Med kryssrutan **Använd AAD-autentisering...**  väljer du autentiseringsmetod för databasservern som är värd för SSISDB: SQL eller Azure Active Directory (AAD) med den hanterade identiteten för din Azure Data Factory. Om du markerar den måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med behörigheter för åtkomst till databasservern. Se [skapa Azure-SSIS-IR med AAD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. För **Admin Username**, ange användarnamn för SQL-autentisering för databasservern som är värd för SSISDB. 

   f. För **Admin-lösenord**, ange ett lösenord för SQL-autentisering för databasservern som är värd för SSISDB. 

   g. För **tjänstnivå för katalogdatabasen**, välj tjänstnivå för databasservern som värd för SSISDB: Basic/Standard/Premium eller namn på elastisk pool. 

   h. Klicka på **Testa anslutning** och om det lyckas, klickar du på **Nästa**. 

1. Gör följande på sidan **Avancerade inställningar**: 

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. För **maximala parallella körningar per nod** väljer du det maximala antalet paket som ska köras samtidigt per nod i ditt integreringskörningskluster. Endast paketnummer som stöds visas. Välj ett lågt värde om du vill använda flera kärnor för att köra ett enda stort/tungt paket som är beräknings-/minnesintensivt. Välj ett stort antal om du vill köra ett eller flera små/lätta paket på en enda kärna. 

   b. För **anpassad inställningscontainer för SAS URI** kan du också ange åtkomst med delad signatur (SAS) URI (identifierare för uniform resurs) för Azure Storage Blob-containern där ditt installationsskript och dess associerade filer lagras. Se [Anpassad installation för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Med kryssrutan **Välj ett virtuellt nätverk...**  väljer du om du vill ansluta din integreringskörning till ett virtuellt nätverk. Om du använder Azure SQL Database med virtuella nätverkstjänstslutpunkter/hanterade instanser som värd för SSISDB eller kräver åtkomst till lokala data måste du ansluta till Azure-SSIS-IR till ett virtuellt nätverk. Se[skapa Azure-SSIS-IR på ett virtuell nätverket](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Klicka på **Slutför** för att börja skapa Azure SSIS-integreringskörningen. 

   > [!IMPORTANT]
   > Den här processen tar cirka 20–30 minuter att slutföra.
   >
   > Data Factory-tjänsten ansluts till din Azure SQL-databas för att förbereda SSIS-katalogen (SSISDB-databasen). 
   > 
   > När du etablerar en instans av en Azure-SSIS IR installeras också Azure Feature Pack för SSIS och Access Redistributable. Med dessa komponenter upprättar du en anslutning till Excel- och Access-filer och till olika Azure-datakällor, utöver de datakällor som stöds av de inbyggda komponenterna. Du kan också installera ytterligare komponenter. Mer information finns i [Anpassad konfiguration för Azure-SSIS integreringskörning](how-to-configure-azure-ssis-ir-custom-setup.md). 

1. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen. 

   ![Skapandestatus med knappen ”Uppdatera”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Länkar i kolumnen Åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime

1. I användargränssnittet för Azure Data Factory växlar du till fliken **Redigera**, väljer **Anslutningar** och växlar sedan till fliken **Integreringskörningar** för att se befintliga integreringskörningar i din datafabrik. 

   ![Alternativ för att visa befintliga integreringskörningar](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Välj **Ny** för att skapa en Azure-SSIS IR. 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. I **installationsfönstret för Integration Runtime** väljer du **Lift-and-shift existing SSIS packages to execute in Azure** (Lift and Shift-hantera befintliga SSIS-paket för att köra i Azure) och väljer sedan **Nästa**. 

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket
Använd nu SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS) för att distribuera dina SSIS-paket till Azure. Anslut till Azure SQL-databasservern som är värd för SSIS-katalogen (SSISDB-databasen). Namnet på Azure SQL-databasservern har formatet `<servername>.database.windows.net`. 

Se följande artiklar från SSIS-dokumentationen: 

- [Distribuera, köra och övervaka ett SSIS-paket på Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ansluta till SSIS-katalogen på Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Schemalägga paketetkörning på Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Etablering av en Azure-SSIS-integreringskörning.

Läs om hur du anpassar din Azure-SSIS-integreringskörning i följande artikel: 

> [!div class="nextstepaction"]
> [Anpassa Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
