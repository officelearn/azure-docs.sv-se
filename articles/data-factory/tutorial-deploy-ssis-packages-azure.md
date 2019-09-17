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
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009629"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Etablera en Azure-SSIS-integreringskörning i Azure Data Factory

Den här självstudien innehåller steg för att använda Azure Portal för att etablera en Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) i Azure Data Factory (ADF). Azure-SSIS IR stöder körning av paket som distribuerats till SSIS-katalogen (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans (projekt distributions modell) och de som distribueras i fil system/fil resurser/Azure Files (paket distributions modell). När Azure-SSIS IR har tillhandahållits kan du sedan använda välbekanta verktyg, till exempel SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) och kommando rads verktyg, `dtinstall`till exempel / `dtutil` / `dtexec`för att distribuera och kör dina paket i Azure. Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Etablering av en Azure-SSIS-integreringskörning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- **Azure SQL Database Server (valfritt)** . Om du inte redan har en databas server skapar du en i Azure Portal innan du börjar. ADF skapar SSISDB på den här databas servern. Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Med den här konfigurationen kan integrerings körningens Skriv körnings loggar i SSISDB utan att korsa Azure-regioner. 
    - Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämför Azure SQL Database enskild databas/elastisk pool/hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Om du använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB eller behöver åtkomst till lokala data utan att konfigurera egen IR-anslutning måste du ansluta till Azure-SSIS IR till ett virtuellt nätverk, Se [skapa Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Detta gäller inte när du använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Lägg till IP-adressen för klient datorn eller ett intervall med IP-adresser som innehåller IP-adressen för klient datorn till klientens IP-adress lista i brand Väggs inställningarna för databas servern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).
    - Du kan ansluta till databas servern med hjälp av SQL-autentisering med autentiseringsuppgifterna för Server administratör eller Azure Active Directory (AAD) med den hanterade identiteten för din ADF. I det senare måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med behörigheter för åtkomst till databasservern. Se [skapa Azure-SSIS-IR med AAD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Bekräfta att din databas server inte har någon SSISDB redan. Etableringen av en Azure-SSIS IR stöder inte användning av en befintlig SSISDB.

> [!NOTE]
> - För en lista över Azure-regioner där ADF och Azure-SSIS IR för närvarande är tillgängliga, se [ADF + SSIS IR-tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Om du vill skapa en ADF via Azure Portal följer du anvisningarna i [skapa ADF via användar gränssnitts](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) artikel och väljer **Fäst på instrument panelen** när du gör det för att få snabb åtkomst efter att den har skapats. 

När din ADF har skapats öppnar du dess översikts sida på Azure Portal och klickar på panelen **författare & Monitor** för att öppna sidan för att **komma igång** på en separat flik där du kan fortsätta att skapa din Azure-SSIS IR.   

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Översikt från Data Factory

1. På sidan **nu sätter vi igång** klickar du på panelen **Konfigurera SSIS integration runtime** . 

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

   ![Almänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

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

   a. I kryss rutan **skapa SSIS Catalog...** väljer du distributions modell för paket som ska köras på din Azure-SSIS IR: Projekt distributions modell där paket distribueras till SSISDB som hanteras av din databas server eller paket distributions modell där paket distribueras till fil systemen/fil resurserna/Azure Files. Om du markerar det måste du ta med din egen databas server som värd för SSISDB som vi ska skapa och hantera för din räkning.
   
   b. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

   c. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning.

   d. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. Baserat på vald databasserver kan SSISDB skapas för din räkning som en enskild databas, en del av en elastisk pool eller i en hanterad instans och kan nås i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämför Azure SQL Database enskild databas/elastisk pool/hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Om du väljer Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB eller behöver åtkomst till lokala data utan att konfigurera egen IR för egen värd, måste du ansluta till Azure-SSIS IR till ett virtuellt nätverk , se [skapa Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. För kryssrutan **Använd AAD-autentisering** väljer du den autentiseringsmetod för databasservern som ska vara värd för SSISDB: SQL-autentisering eller AAD-autentisering med den hanterade identiteten för din ADF. Om du markerar det måste du lägga till den hanterade identiteten för din ADF i en AAD-grupp med åtkomst behörighet till din databas server, se [skapa Azure-SSIS IR med AAD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. För **Admin Username**, ange användarnamn för SQL-autentisering för databasservern som är värd för SSISDB. 

   g. För **Admin-lösenord**, ange ett lösenord för SQL-autentisering för databasservern som är värd för SSISDB. 

   h. För **tjänstnivån för katalogdatabaser** väljer du tjänstnivån för databasservern som ska vara värd för SSISDB: Basic-, Standard- eller Premium-nivån eller namnet på den elastiska poolen.

   i. Klicka på **Testa anslutning** och om det lyckas, klickar du på **Nästa**. 

1. Gör följande på sidan **Avancerade inställningar**: 

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. För **maximala parallella körningar per nod** väljer du det maximala antalet paket som ska köras samtidigt per nod i ditt integreringskörningskluster. Endast paketnummer som stöds visas. Välj ett lågt värde om du vill använda flera kärnor för att köra ett enda stort/tungt paket som är beräknings-/minnesintensivt. Välj ett stort antal om du vill köra ett eller flera små/lätta paket på en enda kärna. 

   b. För **anpassad inställningscontainer för SAS URI** kan du också ange åtkomst med delad signatur (SAS) URI (identifierare för uniform resurs) för Azure Storage Blob-containern där ditt installationsskript och dess associerade filer lagras. Se [Anpassad installation för Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Med kryssrutan **Välj ett virtuellt nätverk...**  väljer du om du vill ansluta din integreringskörning till ett virtuellt nätverk. Du bör kontrol lera det om du använder Azure SQL Database Server med tjänst slut punkter för virtuella nätverk/hanterade instanser med en privat slut punkt som värd för SSISDB eller behöver åtkomst till lokala data utan att konfigurera egen IR-värd, se [skapa Azure-SSIS IR i en virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. I kryss rutan **Konfigurera egen värd...** väljer du om du vill konfigurera den lokala IR-installationen som en proxy för din Azure-SSIS IR, se [Konfigurera egen värd-IR som proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

1. Klicka på **Nästa**. 

1. På sidan **Sammanfattning** granskar du alla inställningar för etablering, bok märken rekommenderade dokumentations länkar och klickar på **Slutför** för att starta skapandet av integration Runtime. 

   > [!NOTE]
   > Den här processen bör slutföras inom 5 minuter förutom eventuella anpassade konfigurations tider.
   >
   > Om du använder SSISDB kommer ADF-tjänsten att ansluta till din databas server för att förbereda SSISDB. 
   > 
   > När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel/Access-filer och olika Azure-datakällor, utöver de data källor som redan stöds av inbyggda komponenter. Du kan också installera ytterligare komponenter, se [anpassad installation för Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen. 

   ![Skapandestatus med knappen ”Uppdatera”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Länkar i kolumnen Åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket till den och köra dem på Azure-SSIS IR med hjälp av SSDT/SSMS-verktyg som ansluter till din databas server via dess server slut punkt. För Azure SQL Database Server/hanterad instans med en offentlig slut punkt är `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`Server slut punktens format. Om du inte använder SSISDB kan du distribuera dina paket i fil system/fil resurser/Azure Files och köra dem på Azure-SSIS IR med hjälp av `dtinstall` / / `dtutil` `dtexec` kommando rads verktyg. Mer information finns i [distribuera SSIS-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med hjälp av aktiviteten kör SSIS-paket i ADF-pipeline, se [anropa SSIS-paket körning som en första omsluten ADF-aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity). 

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