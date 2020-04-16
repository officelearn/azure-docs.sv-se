---
title: Etablera Azure-SSIS-integreringskörningen
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
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418634"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Etablera Azure-SSIS-integreringskörningen i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här självstudien innehåller steg för att använda Azure-portalen för att etablera en SSIS-programkörning (Azure-SQL Server Integration Services) i Azure Data Factory. En Azure-SSIS IR stöder:

- Köra paket som distribueras till SSIS-katalogen (SSISDB) som finns på en Azure SQL Database-server eller en hanterad instans (Project Deployment Model).
- Köra paket som distribueras till filsystem, filresurser eller Azure-filer (Package Deployment Model). 

När en Azure-SSIS IR har etablerats kan du använda välbekanta verktyg för att distribuera och köra dina paket i Azure. Dessa verktyg inkluderar SQL Server Data Tools (SSDT), SQL Server Management Studio `dtinstall` `dtutil`(SSMS) och kommandoradsverktyg som , och `dtexec`.

Begreppsrelaterad information om Azure-SSIS IR finns i [översikten över Azure SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Etablering av en Azure-SSIS-integreringskörning.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- **Azure SQL Database server (valfritt)**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Data Factory skapar i sin tur en SSISDB-instans på den här databasservern. 

  Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Med den här konfigurationen kan körningskörningsloggarna för integrering skriva till SSISDB utan att korsa Azure-regioner.

  Tänk på följande:

  - Baserat på den valda databasservern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Den kan vara tillgänglig i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databasserver som ska vara värd för SSISDB finns i [Jämför en enda Azure SQL-databas, elastisk pool och hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Om du använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en självvärd IR, måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [Skapa en Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Den här inställningen är inte tillämplig när du använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Om du vill aktivera den här inställningen med PowerShell finns i [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Lägg till IP-adressen för klientdatorn, eller ett intervall av IP-adresser som innehåller KLIENTDATOR:s IP-adress, i klient-IP-adresslistan i brandväggsinställningarna för databasservern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).

  - Du kan ansluta till databasservern med hjälp av SQL-autentisering med serveradministratörsuppgifterna eller genom att använda Azure AD-autentisering med den hanterade identiteten för din datafabrik. För det senare måste du lägga till den hanterade identiteten för din datafabrik i en Azure AD-grupp med åtkomstbehörigheter till databasservern. Mer information finns i [Skapa en Azure-SSIS IR med Azure AD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Bekräfta att databasservern inte redan har någon SSISDB-instans. Etableringen av en Azure-SSIS IR stöder inte användning av en befintlig SSISDB-instans.


> [!NOTE]
> En lista över Azure-regioner där Data Factory och en Azure-SSIS IR är tillgängliga finns i [Data Factory- och SSIS IR-tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

Om du vill skapa din datafabrik via Azure-portalen följer du steg-för-steg-instruktionerna i [Skapa en datafabrik via användargränssnittet](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Välj **Fäst på instrumentpanelen** när du gör det, så att snabb åtkomst skapas när den har skapats. 

När din datafabrik har skapats öppnar du översiktssidan i Azure-portalen. Välj panelen **Författare & Övervakare** för att öppna sidan **Låt oss komma igång** på en separat flik. Där kan du fortsätta att skapa din Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Översikt från Data Factory

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**. 

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

### <a name="from-the-authoring-ui"></a>Från det författande användargränssnittet

1. I Azure Data Factory-användargränssnittet växlar du till fliken **Redigera** och väljer **Anslutningar**. Växla sedan till fliken **Integrationskörningar** för att visa befintliga integrationskörningar i datafabriken. 

   ![Alternativ för att visa befintliga integreringskörningar](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Välj **Ny** för att skapa en Azure-SSIS IR. 

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. På **panelen Inställningar för integrationskörning** väljer du de **Lyft-och-flytta befintliga SSIS-paket som ska köras i Azure-panelen** och väljer sedan **Nästa**. 

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Avsnittet om att [etablera en Azure SSIS-integreringskörning](#provision-an-azure-ssis-integration-runtime) innehåller de återstående stegen för att konfigurera en Azure-SSIS-integreringskörning. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

1. Gör följande steg i avsnittet **Allmänna inställningar** på **inställningspanelen för integrationskörning.** 

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. För **Namn**, ange namnet på din integreringsruntime. 

   1. För **beskrivning** anger du en beskrivning av integrationskörningen. 

   1. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB. 

   1. För **Nodstorlek** välj storleken på noderna i ditt integreringskörningskluster. Endast nodstorlekar som stöds visas. Välj en stor nodstorlek (skala upp) om du vill köra många beräkningsintensiva eller minnesintensiva paket. 

   1. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med många noder (skala ut) om du vill köra många paket parallellt. 

   1. För **Edition/License**väljer du SQL Server-utgåvan för din integrationskörning: Standard eller Enterprise. Välj Företag om du vill använda avancerade funktioner på din integrationskörning. 

   1. För **Spara pengar**väljer du alternativet Azure Hybrid Benefit för din integrationskörning: **Ja** eller **Nej**. Välj **Ja** om du vill ta med din egen SQL Server-licens med Software Assurance för att dra nytta av kostnadsbesparingar vid hybridanvändning. 

   1. Välj **Nästa**. 

1. Gör så här i avsnittet **SQL Settings.** 

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Markera kryssrutan **Skapa SSIS-katalog (SSISDB) som finns i Azure SQL Database server/Managed Instance för att lagra dina projekt/paket/miljöer/körningsloggar** för att välja distributionsmodellen för paket som ska köras på din Azure-SSIS IR. Du väljer antingen projektdistributionsmodellen där paket distribueras till SSISDB som finns på databasservern eller paketdistributionsmodellen där paketdistributionsmodell där paket distribueras till filsystem, filresurser eller Azure-filer.
   
      Om du markerar kryssrutan måste du ta med din egen databasserver till SSISDB som vi skapar och hanterar för din räkning.
   
      1. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

      1. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning.

      1. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. 
   
         Baserat på den valda databasservern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Den kan vara tillgänglig i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databasserver som ska vara värd för SSISDB finns i [Jämför en enda Azure SQL-databas, elastisk pool och hanterad instans](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

         Om du väljer en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en självvärd IR, måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [Skapa en Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

      1. Markera kryssrutan **Använd AAD-autentisering med den hanterade identiteten för ADF för** att välja autentiseringsmetoden för databasservern som värd för SSISDB. Du väljer antingen SQL-autentisering eller Azure AD-autentisering med den hanterade identiteten för din datafabrik.

         Om du markerar kryssrutan måste du lägga till den hanterade identiteten för din datafabrik i en Azure AD-grupp med åtkomstbehörigheter till databasservern. Mer information finns i [Skapa en Azure-SSIS IR med Azure AD-autentisering](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
      1. Om **du vill ha administratörsanvändarnamn**anger du användarnamnet för SQL-autentisering för att databasservern ska vara värd för SSISDB. 

      1. För **admin-lösenord**anger du SQL-autentiseringslösenordet för databasservern som värd för SSISDB. 

      1. För **servicenivå för katalogdatabas**väljer du tjänstnivån för databasservern som värd för SSISDB. Välj nivån Basic, Standard eller Premium eller välj ett elastiskt poolnamn.

      1. Välj **Testanslutning**. Om testet lyckas väljer du **Nästa**. 

1. Gör följande i avsnittet **Avancerade inställningar.** 

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. För **maximala parallella körningar per nod**väljer du det maximala antalet paket som ska köras samtidigt per nod i integrationskörningsklustret. Endast paketnummer som stöds visas. Välj ett lågt tal om du vill använda mer än en kärna för att köra ett enda stort paket som är beräknings- eller minnesintensivt. Välj ett högt tal om du vill köra ett eller flera små paket i en enda kärna. 

   1. Markera kryssrutan **Anpassa din Azure-SSIS-integrationskörningstid med ytterligare systemkonfigurationer/komponentinstallationer** för att välja om du vill lägga till anpassade standard-/express-inställningar på din Azure-SSIS IR. Mer information finns i [Anpassad installation för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Markera kryssrutan **Välj ett virtuellt nätverk för din Azure-SSIS-integrationskörningstid, tillåt ADF att skapa vissa nätverksresurser och ta med egna statiska offentliga IP-adresser** för att välja om du vill ansluta till din Azure-SSIS IR till ett virtuellt nätverk.

      Välj den om du använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en självvärd IR. Mer information finns i [Skapa en Azure-SSIS IR i ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Markera kryssrutan **Konfigurera självvärdförd integrationskörning som en proxy för din Azure-SSIS Integration Runtime för** att välja om du vill konfigurera en självvärd IR som proxy för din Azure-SSIS IR. Mer information finns i [Konfigurera en självvärd IR som proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Välj **Fortsätt**. 

1. I avsnittet **Sammanfattning** går du igenom alla etableringsinställningar, bokmärker de rekommenderade dokumentationslänkarna och väljer **Slutför** för att starta skapandet av integrationskörningen. 

   > [!NOTE]
   > Exklusive en anpassad inställningstid bör den här processen avslutas inom 5 minuter.
   >
   > Om du använder SSISDB ansluter datafabrikstjänsten till databasservern för att förbereda SSISDB. 
   > 
   > När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, utöver de datakällor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [Anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen. 

   ![Skapandestatus med knappen ”Uppdatera”](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats. 

   ![Länkar i kolumnen Åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket till det och köra dem på Azure-SSIS IR med hjälp av verktygen FÖR SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS). Dessa verktyg ansluter till databasservern via serverslutpunkten: 

- För en Azure SQL Database-server är `<server name>.database.windows.net`serverslutpunktsformatet .
- För en hanterad instans med privat slutpunkt `<server name>.<dns prefix>.database.windows.net`är serverslutpunktsformatet .
- För en hanterad instans med offentlig slutpunkt `<server name>.public.<dns prefix>.database.windows.net,3342`är serverslutpunktsformatet . 

Om du inte använder SSISDB kan du distribuera dina paket till filsystem, filresurser eller Azure-filer och köra `dtinstall` `dtutil`dem `dtexec` på Azure-SSIS IR med hjälp av verktygen , och kommandoraden. Mer information finns i [Distribuera SSIS-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med hjälp av aktiviteten Kör SSIS-paket i Data Factory-pipelines. Mer information finns i [Anropa SSIS-paketkörning som en förstklassig datafabriksaktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Se även följande SSIS-dokumentation: 

- [Distribuera, köra och övervaka SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Ansluta till SSISDB i Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Schemalägga paketkörningar i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Nästa steg

Läs om hur du anpassar din Azure-SSIS-integreringskörning i följande artikel: 

> [!div class="nextstepaction"]
> [Anpassa en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)