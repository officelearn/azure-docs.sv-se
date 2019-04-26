---
title: 'Självstudier: Använd Azure Database Migration Service för en online RDS SQL Server till Azure SQL Database eller till en Azure SQL Database managed instance-migrering | Microsoft Docs'
description: Lär dig hur du utför en online-migrering från RDS SQL Server till Azure SQL Database eller till en Azure SQL Database-hanterad instans med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/20/2019
ms.openlocfilehash: 7294236a7b79ad093480e9063d886dd30ccf7fc1
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998982"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-database-managed-instance-online-using-dms"></a>Självstudier: Migrera Fjärrskrivbordstjänster SQL Server till Azure SQL Database eller en Azure SQL Database hanterad instans online med DMS
Du kan använda Azure Database Migration Service för att migrera databaserna från en RDS SQL Server-instans till [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) eller en [Azure SQL Database-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) med minimal avbrottstid. I den här självstudien får du migrera den **Adventureworks2012** databasen återställdes till en RDS SQL Server instans av SQL Server 2012 (eller senare) till Azure SQL Database eller en Azure SQL Database-hanterad instans med hjälp av Azure Database Migration Tjänsten.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en instans av Azure SQL Database eller en hanterad Azure SQL Database-instans. 
> * Migrera exempelschemat med hjälp av Data Migration Assistant.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.
> * Ladda ned en migreringsrapport.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserad på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Den här artikeln beskrivs en online-migrering från RDS SQL Server till Azure SQL Database eller en hanterad Azure SQL Database-instans.

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här kursen behöver du:

- Skapa en [RDS SQL Server-databas](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
- Skapa en Azure SQL Database-instans, vilket du kan göra genom att följa anvisningarna i artikeln [Skapa en Azure SQL-databas i Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).

    > [!NOTE]
    > Om du migrerar till en Azure SQL Database managed instance, följer du detaljerat i artikeln [skapa en hanterad Azure SQL Database-instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started), och sedan skapa en tom databas med namnet **AdventureWorks2012**. 
 
- Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) version 3.3 eller senare.
- Skapa ett Azure Virtual Network (VNET) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen. Om du migrerar till en Azure SQL Database managed instance, se till att skapa DMS-instans i samma virtuella nätverk som används för den hanterade Azure SQL Database-instansen, men i ett annat undernät.  Om du använder ett annat virtuellt nätverk för DMS kan du behöva skapa en VNET-peering mellan två virtuella nätverk.
 
    > [!NOTE]
    > Under installationen av virtuellt nätverk, om du använder ExpressRoute med nätverks-peering till Microsoft, lägger du till följande tjänst [slutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) till undernätet där tjänsten ska etableras:
    > - Mål-database-slutpunkten (till exempel SQL-slutpunkten och Cosmos DB-slutpunkt)
    > - Slutpunkt för lagring
    > - Service bus-slutpunkt
    >
    > Den här konfigurationen är nödvändigt eftersom Azure Database Migration Service saknar Internetanslutning. 
 
- Se till att dina regler för Nätverkssäkerhetsgrupp kopplad till virtuella nätverk inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om trafikfiltrering för Azure VNET NSG finns i artikeln om att [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
- Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure SQL Database-servern för att tillåta åtkomst för Azure Database Migration Service till måldatabaserna. Ange undernätsintervallet för det virtuella nätverk som används för Azure Database Migration Service.
- Se till att de autentiseringsuppgifter som används för att ansluta till RDS SQL Server-källinstansen är associerade med ett konto som är medlem i serverrollen för ”Processadmin” och medlem av databasrollerna ”db_owner” på alla databaser som ska migreras.
- Kontrollera att de autentiseringsuppgifter som används för att ansluta till Azure SQL Database målinstansen har KONTROLLDATABAS-behörighet på target Azure SQL-databaser och en medlem i sysadmin-rollen om migreringen till en Azure SQL Database-hanterad instans.
- Källversionen för RDS SQL Server måste vara SQL Server 2012 och senare. För att fastställa vilken version av SQL Server-instansen som körs kan du läsa artikeln [Hur man fastställer version, utgåva, och uppdateringsnivå för SQL Server och dess komponenter](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Aktivera sammanställning av ändringsdata (CDC) på RDS SQL Server-databasen och alla användartabeller som valts för migrering.
    > [!NOTE]
    > Du kan använda skriptet nedan för att aktivera CDC på en RDS SQL Server-databas.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Du kan använda skriptet nedan för att aktivera CDC på alla tabeller.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
- Inaktivera databasutlösare i målets Azure SQL Database.
    > [!NOTE]
    > Du hittar databasutlösarna i målets Azure SQL Database med följande fråga:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Mer information finns i artikeln [om att inaktivera utlösare (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat
Använd DMA för att migrera schemat till Azure SQL Database.

> [!NOTE]
> Innan du skapar ett migreringsprojekt i DMA ska du redan ha etablerat en Azure SQL-databas så som anges i förutsättningarna. För den här självstudien antas namnet på Azure SQL Database vara **AdventureWorks2012** men du kan ange ett valfritt namn.

För att migrera **AdventureWorks2012**-schemat till Azure SQL Database gör du följande:

1.  I Data Migration Assistant väljer du ikonen Nytt (+) och sedan under **Projekttyp** väljer du **Migrering**.
2.  Ange ett projektnamn i textrutan **Typ av källserver**, välj **SQL Server**. I textrutan **Målservertyp** väljer du sedan **Azure SQL Database**.
3.  Under **Migreringsomfång** väljer du **Endast schema**.

    När du har utfört de föregående stegen bör DMA-gränssnittet visas i följande bild:
    
    ![Skapa Data Migration Assistant-projekt](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4.  Välj **Skapa** för att skapa projektet.
5.  I DMA anger du källanslutningsinformationen för SQL Server. Välj **Anslut** och välj databasen **AdventureWorks2012**.

    ![Källanslutningsinformation för Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6.  Välj **Next**, under **Anslut till målserver**, ange målanslutningsinformationen för Azure SQL-databasen, välj **Anslut** och välj sedan databasen **AdventureWorksAzure** som du har företablerat i Azure SQL Database.

    ![Målanslutningsinformation för Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7.  Välj **Nästa** för att komma till sidan **Välj objekt**, där du kan ange schemaobjekten i databasen **AdventureWorks2012** som måste distribueras till Azure SQL Database.

    Som standard markeras alla objekt.

    ![Skapa SQL-skript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8.  Välj **Skapa SQL-skript** för att skapa SQL-skripten och granska sedan om det finns fel i skripten.

    ![Schemaskript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9.  Välj **Distribuera schema** för att distribuera schemat till Azure SQL Database, och när schemat har distribuerats kontrollerar du om det finns avvikelser på målservern.

    ![Distribuera schema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration
1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.
 
   ![Visa portalprenumerationer](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)
       
2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.
 
    ![Visa resursprovidrar](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)
    
3.  Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.
 
    ![Registrera resursprovider](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans
1.  I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2.  På sidan **Azure Database Migration Service** väljer du **Skapa**.
 
    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3.  På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll-SQL Server och målinstansen av Azure SQL Database.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [Skapa ett virtuellt nätverk med hjälp av Azure-portalen](https://aka.ms/DMSVnet).

6. Välj en prisnivå. För den här onlinemigreringen måste du markera Premium-prisnivån.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

     ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7.  Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt
När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.
 
      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.
 
     ![Hitta din instans av Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)
 
3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, i textrutan **Typ av källserver** väljer du **AWS Fjärrskrivbordstjänster för SQLServer** och i textrutan **Målservertyp** väljer du **Azure SQL Database**.

    > [!NOTE]
    > Typ av målserver, Välj **Azure SQL Database** för att migrera till både en Azure SQL Database-singleton-databas och även om en Azure SQL Database-hanterad instans.

5. I avsnittet **Välj aktivitetstyp** väljer du **Online-datamigrering**.

    > [!IMPORTANT]
    > Se till att välja **Online datamigrering**; offline migreringar stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Du kan även välja **Skapa endast projekt** för att skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    ![Skapa och köra en Azure Database Migration Service-aktivitet](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)
 
## <a name="specify-source-details"></a>Ange källinformation
1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källinstansen.
 
    Se till att använda ett fullständigt domännamn (FQDN) för SQL Server-källinstansens namn.

2. Om du inte har installerat ett betrott certifikat på källservern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som har krypterats med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte lita på SSL som använder självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till internet.

   ![Källinformation](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Ange målinformation
1. Välj **Spara** och ange på sidan **Information om migreringsmål** anslutningsinformationen för Azure SQL Database-målservern, som är den företablerade Azure SQL Database som **AdventureWorks2012**-schemat distribuerades till med DMA.

    ![Välja mål](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Välj **Spara**. Expandera tabellistan på sidan **Välj tabeller** och granska sedan listan över berörda fält.

    Azure Database Migration Service väljer automatiskt alla tomma källtabeller som finns på Azure SQL Database-målinstansen. Om du vill migrera tabeller på nytt som redan innehåller data måste du uttryckligen välja tabellerna på den här skärmen.

    ![Välj tabeller](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4.  Välj **Spara** när du har angett följande **Avancerade inställningar för onlinemigrering**.
    
    | Inställning | Beskrivning |
    | ------------- | ------------- |
    | **Maximalt antal tabeller som kan läsas in parallellt** | Anger antalet tabeller som DMS kör parallellt under migreringen. Standardvärdet är 5, men det kan ställas in på ett optimalt värde för att uppfylla specifika migreringsbehov baserat på alla POC-migreringar. |
    | **När en källtabell trunkeras** | Anger om DMS trunkerar måltabellen under migreringen. Den här inställningen kan vara användbart om en eller flera tabeller trunkeras som en del av migreringsprocessen. |
    | **Konfigurera inställningar för data för stora objekt (LOB)** | Anger om DMS migrerar obegränsade LOB-data eller begränsar LOB-data som migrerats till en viss storlek.  När det finns en gräns LOB-data som har migrerats, alla LOB-data utöver denna gräns trunkeras. För produktionsmigrering rekommenderar vi att du väljer **Tillåt obegränsad LOB-storlek** för att förhindra dataförlust. När du anger att du vill tillåta obegränsad LOB-storlek markerar du kryssrutan **Migrate LOB data in a single block when the LOB size is less than (KB)** (Migrera LOB-data i ett enda block när LOB-storleken är mindre än (KB)) för att förbättra prestanda. |
    
    ![Ställa in avancerade inställningar för onlinemigrering](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5.  Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen
- Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

    ![Aktivitetsstatus – initieras](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen
1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

2. Klicka på en specifik databas för att komma till migreringsstatus för åtgärderna **Fullständig datainläsning** och **Inkrementell datasynkronisering**.

    ![Aktivitetsstatus – Pågår](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering
När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Starta snabb](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)
 
2.  Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3.  Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya Azure SQL-måldatabasen.
 
    ![Aktivitetsstatus – slutförd](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Nästa steg
- Information om kända problem och begränsningar när du utför onlinemigreringar till Azure SQL DatabaseL finns i artikeln [Known issues and workarounds with Azure SQL Database online migrations](known-issues-azure-sql-online.md) (Kända problem och lösningar med Azure SQL Database-onlinemigreringar).
- Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
- Information om Azure SQL Database finns i artikeln [Vad är tjänsten SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
- Information om Azure SQL Database hanterade instanser finns på sidan [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index).
