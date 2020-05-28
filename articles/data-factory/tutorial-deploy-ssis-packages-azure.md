---
title: Etablera Azure-SSIS integration runtime
description: Lär dig hur du etablerar Azure SSIS-integreringskörning i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 6c1fa0d8be0146db045b020a20004fee4df7c913
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118235"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Etablera Azure-SSIS integration runtime i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här självstudien innehåller steg för att använda Azure Portal för att etablera en IR (Azure-SQL Server Integration Services (SSIS) integration Runtime (IR) i Azure Data Factory. En Azure-SSIS IR stöder paket som har distribuerats till:

- En SSIS-katalog (SSISDB) som finns i SQL Database-eller SQL-hanterad instans (projekt distributions modellen).
- Fil system, fil resurser eller en Azure Files resurs (paket distributions modellen).

När en Azure-SSIS IR har tillhandahållits kan du använda välbekanta verktyg för att distribuera och köra dina paket i Azure. Dessa verktyg omfattar SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) och kommando rads verktyg som `dtinstall` , `dtutil` och `dtexec` .

Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Etablering av en Azure-SSIS-integreringskörning.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- **SQL Database eller SQL-hanterad instans**: om du inte redan har en måste du skapa en i Azure Portal innan du börjar. Azure Data Factory skapar i sin tur SSISDB på den här SQL Database eller SQL-hanterade instansen. Vi rekommenderar att du skapar SQL Database-eller SQL-hanterad instans i samma Azure-region som integration Runtime. Den här konfigurationen gör att Integration Runtimes skrivkörning loggas till SSISDB utan att korsa Azure-regioner. 

  Tänk på följande saker:

  - Baserat på den valda databas servern kan SSISDB skapas för din räkning som en enskild databas eller en del av en elastisk pool i SQL Database, eller i SQL-hanterad instans och tillgänglig i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämföra en SQL Database och SQL-hanterad instans](create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)
  
    Om du använder SQL Database med en IP-brandvägg eller tjänst slut punkter för virtuella nätverk eller en SQL-hanterad instans med en privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en lokal IR-anslutning, ansluter du Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [skapa en Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktive rad för SQL Database. Den här inställningen gäller inte när du använder SQL Database med IP-brandvägg eller tjänst slut punkter för virtuella nätverk, eller en SQL-hanterad instans med en privat slut punkt som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Lägg till IP-adressen för klient datorn eller ett intervall med IP-adresser som innehåller IP-adressen för klient datorn till klientens IP-lista i brand Väggs inställningarna för SQL Database. Mer information finns i [brand Väggs regler på server nivå och databas nivå](../azure-sql/database/firewall-configure.md).

  - Du kan ansluta till SQL Database-och SQL-hanterad instans genom att använda SQL-autentisering med dina autentiseringsuppgifter för Server administratören eller genom att använda Azure AD-autentisering med den hanterade identiteten för din data fabrik. För den senare måste du lägga till den hanterade identiteten för din data fabrik i en Azure AD-grupp med åtkomst behörighet till SQL Database. Mer information finns i [skapa en Azure-SSIS IR med Azure AD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Bekräfta att SQL Database-eller SQL-hanterad instans inte redan har en SSISDB-instans. Etableringen av en Azure-SSIS IR stöder inte användning av en befintlig SSISDB-instans.


> [!NOTE]
> En lista över Azure-regioner där Data Factory och en Azure-SSIS IR för närvarande är tillgängliga finns i [Data Factory och SSIS IR-tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Om du vill skapa din data fabrik via Azure Portal följer du de stegvisa anvisningarna i [skapa en data fabrik via användar gränssnittet](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Välj **Fäst på instrument panelen** när du gör det, för att tillåta snabb åtkomst efter att den har skapats. 

När data fabriken har skapats öppnar du dess översikts sida i Azure Portal. Välj panelen **författare & Monitor** för att öppna sidan för att **komma igång** på en separat flik. Där kan du fortsätta att skapa Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Översikt från Data Factory

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

### <a name="from-the-authoring-ui"></a>Från redigerings gränssnittet

1. I Azure Data Factory användar gränssnitt växlar du till fliken **Redigera** och väljer **anslutningar**. Växla sedan till fliken **integrerings körningar** för att visa befintliga integrerings körningar i din data fabrik. 

   ![Alternativ för att visa befintliga integreringskörningar](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Välj **Ny** för att skapa en Azure-SSIS IR. 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. I panelen **integration runtime installation** väljer du de **befintliga SSIS-paketen lyft och Shift som ska köras på Azure** -panelen och väljer sedan **Nästa**. 

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

1. I avsnittet **allmänna inställningar** i **integration runtime inställnings** panelen utför du följande steg. 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. För **Namn**, ange namnet på din integreringsruntime. 

   1. För **beskrivning** anger du en beskrivning av integrationskörningen. 

   1. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB. 

   1. För **Nodstorlek** välj storleken på noderna i ditt integreringskörningskluster. Endast nodstorlekar som stöds visas. Välj en stor Node-storlek (skala upp) om du vill köra många beräknings intensiva eller minnes intensiva paket. 

   1. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med många noder (skala ut) om du vill köra många paket parallellt. 

   1. För **utgåva/licens**väljer du SQL Servers versionen för integration Runtime: standard eller Enterprise. Välj företag om du vill använda avancerade funktioner i integrerings körningen. 

   1. För **Spara pengar**väljer du alternativet Azure Hybrid-förmån för integration Runtime: **Ja** eller **Nej**. Välj **Ja** om du vill ta med din egen SQL Server-licens med Software Assurance för att dra nytta av kostnads besparingar med hybrid användning. 

   1. Välj **Nästa**. 

1. Utför följande steg i avsnittet **SQL-inställningar** . 

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Markera kryss rutan **skapa SSIS-katalog (SSISDB) som hanteras av SQL Database/SQL-hanterad instans för att lagra dina projekt/paket/miljöer/körnings loggar** för att välja distributions modell för paket som ska köras på din Azure-SSIS IR. Du väljer antingen projekt distributions modellen där paket distribueras till SSISDB som finns på databas servern, eller distributions modellen för paketet där paket distribueras i fil system, fil resurser eller Azure Files.
   
      Om du markerar kryss rutan måste du ta med din egen databas server som värd för SSISDB som vi ska skapa och hantera för din räkning.
   
      1. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

      1. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning.

      1. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. 
   
      Baserat på den valda databas servern kan SSISDB-instansen skapas för din räkning som en enskild databas eller som en del av en elastisk pool i SQL Database eller i SQL-hanterad instans. Det kan vara tillgängligt i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i [jämför SQL Database och SQL-hanterad instans](create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

      Om du väljer SQL Database med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller SQL-hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en lokal IR-anslutning, måste du ansluta din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [skapa en Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

      1. Markera kryss rutan **Använd AAD-autentisering med den hanterade identiteten för din ADF** för att välja autentiseringsmetod för din databas server som värd för SSISDB. Du väljer antingen SQL-autentisering eller Azure AD-autentisering med den hanterade identiteten för din data fabrik.

         Om du markerar kryss rutan måste du lägga till den hanterade identiteten för din data fabrik i en Azure AD-grupp med åtkomst behörighet till din databas server. Mer information finns i [skapa en Azure-SSIS IR med Azure AD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
      1. För **Administratörs användar namn**anger du användar namnet för SQL-autentisering för din databas server som värd för SSISDB. 

      1. För **Administratörs lösen ord**anger du lösen ordet för SQL-autentisering för din databas server som värd för SSISDB. 

      1. För **tjänst nivån katalog databas**väljer du tjänst nivå för din databas server som värd för SSISDB. Välj nivån Basic, standard eller Premium eller Välj ett namn för elastisk pool.

      1. Välj **test anslutning**. Om testet lyckas väljer du **Nästa**. 

1. I avsnittet **Avancerade inställningar** utför du följande steg. 

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. För **maximala parallella körningar per nod**väljer du det maximala antalet paket som ska köras samtidigt per nod i integration runtime-klustret. Endast paketnummer som stöds visas. Välj ett lågt nummer om du vill använda mer än en kärna för att köra ett enda stort paket som är beräknings-eller minnes krävande. Välj ett högt värde om du vill köra ett eller flera små paket i en enda kärna. 

   1. Markera kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** för att välja om du vill lägga till standard-/Express-anpassade inställningar på din Azure-SSIS IR. Mer information finns i [anpassad installation för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Välj alternativet **Välj ett VNet för Azure-SSIS integration runtime att ansluta till, Tillåt ADF att skapa vissa nätverks resurser och om du vill ta med egna statiska offentliga IP-adresser** väljer du om du vill ansluta till Azure-SSIS IR till ett virtuellt nätverk.

      Välj den om du använder SQL Database med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller SQL-hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en egen IR. Mer information finns i [skapa en Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Markera kryss rutan **Konfigurera egen värd integration runtime som en proxy för din Azure-SSIS integration runtime** för att välja om du vill konfigurera en egen IR as-proxy för din Azure-SSIS IR. Mer information finns i [Konfigurera en egen värd för IR som proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Välj **Fortsätt**. 

1. I avsnittet **Sammanfattning** granskar du alla inställningar för etablering, bok märken rekommenderade dokumentations länkar och väljer **Slutför** för att starta skapandet av integration Runtime. 

   > [!NOTE]
   > Om du exkluderar en anpassad konfigurations tid bör den här processen avslutas inom 5 minuter.
   >
   > Om du använder SSISDB kommer tjänsten Data Factory att ansluta till din databas server för att förbereda SSISDB. 
   > 
   > När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, förutom de data källor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen. 

   ![Skapandestatus med knappen ”Uppdatera”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Länkar i kolumnen Åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket till den och köra dem på Azure-SSIS IR genom att använda SQL Server Data Tools-(SSDT) eller SQL Server Management Studio-verktyg (SSMS). Dessa verktyg ansluter till din databas server via dess server slut punkt: 

- För en logisk SQL Server är Server slut punktens format `<server name>.database.windows.net` .
- För en SQL-hanterad instans med privat slut punkt är Server slut punktens format `<server name>.<dns prefix>.database.windows.net` .
- För en SQL-hanterad instans med offentlig slut punkt är Server slut punktens format `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Om du inte använder SSISDB kan du distribuera dina paket i fil system, fil resurser eller Azure Files och köra dem på Azure-SSIS IR med hjälp av `dtinstall` `dtutil` `dtexec` kommando rads verktygen, och. Mer information finns i [distribuera SSIS-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med hjälp av aktiviteten kör SSIS-paket i Data Factory pipeliner. Mer information finns i [anropa SSIS-paket körning som en första klass Data Factory aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Se även följande SSIS-dokumentation: 

- [Distribuera, köra och övervaka SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ansluta till SSISDB i Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Schemalägga paket körningar i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Nästa steg

Läs om hur du anpassar din Azure-SSIS-integreringskörning i följande artikel: 

> [!div class="nextstepaction"]
> [Anpassa en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)