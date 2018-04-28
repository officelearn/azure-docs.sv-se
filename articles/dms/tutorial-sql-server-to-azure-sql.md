---
title: Använda tjänsten Azure Database migrering för att migrera SQL Server till Azure SQL Database | Microsoft Docs
description: Lär dig hur du migrerar från SQL Server lokalt till SQL Azure med tjänsten Azure Database migrering.
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/29/2018
ms.openlocfilehash: 48f1b3715f300fea7bfc0590e6d2e6c6622e83aa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>Migrera SQLServer till Azure SQL-databas
Du kan använda tjänsten Azure Database migrering för att migrera databaser från en lokal SQL Server-instans till Azure SQL Database. I kursen får du migrerar den **Adventureworks2012** databasen återställs till en lokal instans av SQL Server 2016 (eller högre) till en Azure SQL Database med hjälp av tjänsten Azure Database migrering.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utvärdera din lokala-databasen med hjälp av Data Migration Assistant.
> * Migrera exempel schemat genom att använda Data Migration Assistant.
> * Skapa en instans av tjänsten Azure Database migrering.
> * Skapa ett migreringsprojekt genom att använda tjänsten Azure Database migrering.
> * Kör migreringen.
> * Övervaka migreringen.

## <a name="prerequisites"></a>Förutsättningar
Den här kursen behöver du:

- Hämta och installera [SQL Server 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (någon utgåva).
- Aktiverar du TCP/IP-protokollet är inaktiverat som standard under installationen av SQL Server Express av följa anvisningarna i artikeln [aktivera eller inaktivera nätverksprotokoll Server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Skapa en instans av Azure SQL Database-instans som du kan göra med följande information i artikeln [skapa en Azure SQL database i Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Hämta och installera den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 eller senare.
- Skapa ett virtuellt nätverk för tjänsten Azure Database migrering med hjälp av Azure Resource Manager distributionsmodell, som ger plats-till-plats-anslutning till din lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Se till att din Azure virtuella nätverk (VNET) Nätverkssäkerhetsgruppen regler gör inte blockera följande meddelande portarna 443, 53, 9354, 445, 12000. Mer information om Azure VNET NSG trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandväggen för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att tjänsten Azure databas migrering att få åtkomst till källan SQL Server som är TCP-port 1433 som standard.
- Om du använder flera namngivna SQL Server-instanser som använder dynamiska portar, kan du aktivera tjänsten SQL Browser och ge åtkomst till UDP-port 1434 genom dina brandväggar så att tjänsten Azure Database migrering kan ansluta till en namngiven instans på datakällan Server.
- När du använder en brandväggsinstallation framför käll-databaserna, kanske du måste lägga till brandväggsregler som tillåter tjänsten Azure Database migrering till databaserna källa för migrering.
- Skapa en servernivå [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för Azure SQL Database-server för att ge åtkomst till måldatabaserna Azure databastjänst för migrering. Ange undernätets intervall för det VNET som används för tjänsten Azure Database migrering.
- Se till att de autentiseringsuppgifter som används för att ansluta till datakällan SQL Server-instansen har [KONTROLLSERVERN](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) behörigheter.
- Se till att de autentiseringsuppgifter som används för att ansluta till Azure SQL Database målinstansen CONTROL DATABASE-behörighet på mål Azure SQL-databaser.

## <a name="assess-your-on-premises-database"></a>Utvärdera din lokala-databasen
Innan du kan migrera data från en lokal SQL Server-instans till Azure SQL Database, måste du utvärdera SQL Server-databasen för eventuella blockeringsproblem som hindrar migrering. Data Migration Assistant v3.3 eller senare, Följ stegen som beskrivs i artikel [utför en utvärdering av SQL Server-migreringen](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) för att slutföra lokalt databasen assessment. En sammanfattning av de nödvändiga stegen följande:
1.  Klicka på ikonen Ny (+) i Data migrering-assistenten och välj sedan den **Assessment** projekttyp.
2.  Ange ett projektnamn i den **servertyp av datakälla** text markerar **SQL Server**, och klicka sedan på den **mål servertyp** text markerar **Azure SQL Databasen**.
3.  Välj **skapa** att skapa projektet.

    När du bedömer SQL Server källdatabasen migrera till Azure SQL Database, kan du välja en eller båda av följande typer av assessment rapporten:
    - Databasen är kompatibel
    - Kontrollera funktionsparitet

    Båda rapporttyperna är markerade som standard.
4.  I Data migrering-assistenten på den **alternativ** väljer **nästa**.
5.  På den **Välj källor** skärmen i den **Anslut till en server** dialogrutan Ange anslutningsinformationen för SQL Server och välj sedan **Anslut**.
6.  I den **lägger till källor** dialogrutan **AdventureWorks2012**väljer **Lägg till**, och välj sedan **starta Assessment**.

    När bedömningen är klar visas resultaten som visas i följande bild:

    ![Utvärdera datamigrering](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    För Azure SQL Database identifiera bedömningarna migrering blockerande problem med och funktionen paritet.

7.  Granska resultaten för migrering blockerande problem och funktionen paritet problem genom att välja specifika alternativ.
    - Den **SQL Server-funktionsparitet** kategori innehåller en omfattande uppsättning rekommendationer alternativa metoder som är tillgängliga i Azure, och minimera stegen när du planerar den mängd arbete i ditt projekt för migrering.
    - Den **kompatibilitetsproblem** kategori identifierar delvis stöds eller funktioner som inte stöds som avspeglar kompatibilitetsproblem som kan blockera migrera lokala SQL Server-databaser till Azure SQL Database. Det finns också rekommendationer som hjälper dig att lösa dessa problem.


## <a name="migrate-the-sample-schema"></a>Migrera exempel schemat
När du är nöjd med bedömning och finner att den valda databasen är en bra kandidat för migrering till Azure SQL Database använder Data Migration Assistant för att migrera schemat till Azure SQL Database.

> [!NOTE]
> Innan du skapar ett migreringsprojekt i Data Migration Assistant Glöm inte att du redan har etablerat en Azure SQL database enligt kraven. För den här kursen är namnet på Azure SQL Database antas vara **AdventureWorksAzure**, men du kan ge den namnet på olika sätt om du vill.

Så här migrerar du den **AdventureWorks2012** schemat till Azure SQL Database, utför följande steg:

1.  I Data migrering assistenten, klicka på ikonen Ny (+) och sedan under **projekttyp**väljer **migrering**.
3.  Ange ett projektnamn i den **servertyp av datakälla** text markerar **SQL Server**, och klicka sedan på den **mål servertyp** text markerar **Azure SQL Databasen**.
4.  Under **migrering Scope**väljer **Schema only**.

    När du utför de här stegen, visas gränssnittet Data Migration Assistant som visas i följande bild:
    
    ![Skapa Data migrering Assistant-projekt](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  Välj **skapa** att skapa projektet.
6.  Ange källa anslutningsinformationen för SQL Server, väljer i Data migrering-assistenten **Anslut**, och välj sedan den **AdventureWorks2012** databas.

    ![Information om migrering Assistant datakälla anslutning](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  Välj **nästa**under **Anslut till målservern**, ange mål-anslutningsinformationen för Azure SQL-databasen, väljer **Anslut**, och välj sedan **AdventureWorksAzure** databasen som du hade etableras i förväg i Azure SQL-databas.

    ![Information om migrering Assistant mål dataanslutningen](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  Välj **nästa** att gå vidare till den **Välj objekt** skärmen där du kan ange schemaobjekt i den **AdventureWorks2012** databas som måste distribueras till Azure SQL-databas.

    Som standard markeras alla objekt.

    ![Generera SQL-skript](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  Välj **generera SQL-skript** att skapa SQL-skript och granska sedan skript för eventuella fel.

    ![Schema-skript](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. Välj **distribuera schemat** att distribuera schemat till Azure SQL Database och sedan efter schemat har distribuerats, kontrollera målservern efter eventuella avvikelser.

    ![Distribuera Schema](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registerresursleverantören Microsoft.DataMigration
1. Logga in på Azure portal, Välj **alla tjänster**, och välj sedan **prenumerationer**.
 
   ![Visa portal prenumerationer](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.
 
    ![Visa resursprovidrar](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  Sök för migreringen och sedan till höger om **Microsoft.DataMigration**väljer **registrera**.
 
    ![Registrera resursprovider](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>Skapa en instans
1.  I Azure portal, väljer **+ skapa en resurs**, söka efter Migreringstjänst för Azure-databasen och välj sedan **Azure migrering databastjänsten** från den nedrullningsbara listan.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  På den **Migreringstjänst för Azure-databasen (förhandsgranskning)** väljer **skapa**.
 
    ![Skapa en instans av Azure databastjänst för migrering](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  På den **migrering databastjänsten** skärmen, ange ett namn för tjänsten, prenumerationen, ett virtuellt nätverk och prisnivå.

    Mer information om kostnader och prisnivåer finns i [sida med priser](https://aka.ms/dms-pricing).

     ![Konfigurera inställningar för Azure-databas migreringstjänsten instans](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  Välj **skapa** att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt
När tjänsten har skapats kan hitta den i Azure-portalen och sedan skapa ett migreringsprojekt.
1. Välj i Azure-portalen **alla tjänster**, söka efter Migreringstjänst för Azure-databasen och välj sedan **Azure databastjänster migrering**.
 
      ![Leta upp alla instanser av tjänsten Azure Database migrering](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. På den **Azure migrering databastjänster** skärmen, Sök efter namnet på Azure-DMS-instans som du skapade och välj sedan instansen.
 
     ![Leta upp din instans av tjänsten Azure Database migrering](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. Välj **+ nytt migreringsprojekt**.
4. På den **nytt migreringsprojekt** skärmen, ange ett namn för projektet i den **servertyp av datakälla** text markerar **SQL Server**, och klicka sedan på den **mål servertyp** text markerar **Azure SQL Database**.

    ![Skapa databas migrering Service-projekt](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  Välj **skapa** att skapa projektet.


## <a name="specify-source-details"></a>Ange information om datakälla
1. På den **datakällan information** skärmen, ange anslutningsinformationen för SQL Server.

    ![Välj källa](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. Välj **spara**, och välj sedan den **AdventureWorks2012** databas för migrering.

    ![Välj källa DB](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>Ange information om mål
1. Välj **spara**, och klicka sedan på den **mål information** skärmen, ange anslutningsinformationen för mål, vilket är företablerad Azure SQL Database som den **AdventureWorks2012**  schema har distribuerats med hjälp av Data Migration Assistant.

    ![Välj mål](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. Välj **spara** spara projektet.
3. På den **migrering sammanfattning** skärmen, granska och kontrollera informationen som är kopplade till projektet migrering.

    ![DMS-sammanfattning](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. Välj **Spara**.

## <a name="run-the-migration"></a>Kör migreringen
1.  Välj senast sparade projektet, Välj **+ ny aktivitet**, och välj sedan **kör datamigrering**.

    ![Ny aktivitet](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  När du uppmanas att ange autentiseringsuppgifterna för käll- och målservrarna och välj sedan **spara**.
3.  På den **karta till måldatabaserna** skärmen, mappa käll- och måldatabasen för migrering.

    Om måldatabasen innehåller namnet på databas som källdatabasen, väljs Azure DMS måldatabasen som standard.

    ![Mappa till måldatabaserna](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. Välj **spara**på den **Välj tabeller** skärmen, expandera listan tabell och granska listan med fält som påverkas.

    ![Välj tabeller](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  Välj **spara**på den **migrering sammanfattning** skärmen i den **aktivitetsnamn** text anger du ett namn för aktiviteten migrering.

    På den här sidan kan du också expandera den **Välj verifieringsalternativ** skärmen där du kan ange om du vill validera migrerade databasen för:
    - Schema
    - Datakonsekvens
    - Frågan är korrekt och prestanda

    ![Välj verifieringsalternativ](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  Välj **spara**, Granska sammanfattningen för att säkerställa att informationen för källan och målet matchar vad du tidigare angav.

    ![Sammanfattning av migrering](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  Välj **kör migrering** att starta aktiviteten migrering och välj sedan **uppdatera** att granska aktuella status.

    ![Aktivitetsstatus](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen
1. Välj aktiviteten migrering för att granska statusen för aktiviteten.
2. Verifiera Azure SQL måldatabasen när migreringen är klar.

    ![Slutförd](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
