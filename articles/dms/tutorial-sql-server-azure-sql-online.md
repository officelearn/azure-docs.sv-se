---
title: 'Självstudie: Använda Azure Database Migration Service för att utföra en migrering av SQL Server till Azure SQL Database online | Microsoft Docs'
description: Lär dig hur du gör en onlinemigrering från SQL Server lokalt till Azure SQL Database genom att använda Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: be6a5c444444c9482b921ea7fd68bf26ac10a2a2
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958941"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-online-using-dms"></a>Självstudie: Migrera SQL Server till Azure SQL Database online med DMS
Du kan använda Azure Database Migration Service till att migrera databaserna från en lokal SQL Server-instans till [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) med minimal avbrottstid. I den här självstudiekursen migrerar du databasen **Adventureworks2012** återställt till en lokal instans av SQL Server 2016 (eller senare) till Azure SQL Database genom att använda Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utvärdera din lokala databas med hjälp av Data Migration Assistant.
> * Migrera exempelschemat med hjälp av Data Migration Assistant.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.
> * Ladda ned en migreringsrapport.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den artikeln beskrivs en onlinemigrering från SQL Server till Azure SQL Database. En offlinemigrering beskrivs i [Migrera SQL Server till Azure SQL Database offline med DMS](tutorial-sql-server-to-azure-sql.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
För att slutföra den här kursen behöver du:

- Ladda ned och installera [SQL Server 2012 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads) (valfri version).
- Aktivera TCP/IP-protokollet, som är inaktiverat som standard under SQL Server Express-installation, genom att följa instruktionerna i artikeln om att [aktivera eller inaktivera ett servernätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Skapa en Azure SQL Database-instans, vilket du kan göra genom att följa informationen i artikeln om att [skapa Azure SQL Database i Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) version 3.3 eller senare.
- Skapa virtuellt nätverk för Azure Database Migration Service genom att använda Azure Resource Manager-distributionsmodellen, som ger plats-till-plats-anslutning för dina lokala källservrar genom att använda [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Kontrollera att dina regler för nätverkssäkerhetsgrupp (NSG) för Azure Virtual Network (VNET) inte blockerar följande kommunikationsportar: 443, 53, 9354, 445, 12000. Mer information om trafikfiltrering för Azure VNET NSG finns i artikeln om att [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
- När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
- Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure SQL Database-servern för att tillåta åtkomst för Azure Database Migration Service till måldatabaserna. Ange undernätsintervallet för det virtuella nätverk som används för Azure Database Migration Service.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen har [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)-behörigheter.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta Azure SQL Database-målinstansen har CONTROL DATABASE-behörighet i Azure SQL-måldatabaserna.
- Källversionen för SQL Server måste vara SQL Server 2005 och senare. För att fastställa vilken version av SQL Server-instansen som körs kan du läsa artikeln [Hur man fastställer version, utgåva, och uppdateringsnivå för SQL Server och dess komponenter](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Databaserna måste antingen vara bulkloggade eller i fullständigt återställningsläge. För att fastställa återställningsmodellen som har konfigurerats för din SQL Server-instans kan du läsa artikeln [Visa eller ändra återställningsmodellen för en databas (Microsoft SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Se till att göra fullständiga databassäkerhetskopieringar för databaserna. Om du vill skapa en fullständig säkerhetskopiering av databasen kan du läsa artikeln [How to: Create a Full Database Backup (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)) (Så här skapar du en fullständig säkerhetskopiering av databasen (Transact-SQL)).
- Om någon av tabellerna saknar en primärnyckel kan du aktivera sammanställning av ändringsdata (CDC) på databasen och specifika tabeller.
    > [!NOTE]
    > Du kan använda skriptet nedan för att hitta tabeller som inte har några primärnycklar.
    ```
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
     ```
    >Om resultatet visar en eller flera tabeller med "is_tracked_by_cdc" som "0" aktiverar du ändrad Capture för databasen och för de specifika tabellerna med processen som beskrivs i artikeln [Enable and Disable Change Data Capture (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017) (Aktivera och inaktivera sammanställning av ändringsdata (SQL Server)).

- Konfigurera distributörsrollen för SQL Server-källan.

    >[!NOTE]
    > Du kan fastställa om replikeringskomponenterna har installerats med nedanstående fråga.
    ```
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```
    Om resultatet returnerar ett felmeddelande som förslår att du ska installera replikeringskomponenter ska du installera SQL Server-replikeringskomponenter med hjälp av processen i artikeln [Install SQL Server replication](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017) (Installera SQL Server-replikering).

    Om replikering redan har installerats kontrollerar du om distributionsrollen har konfigurerats på SQL Server-källan med hjälp av T-SQL-kommandot nedan.
    ```
    EXEC sp_get_distributor;
    ```

    Om distributionen inte har konfigurerats konfigurerar du distributionen med vägledningen i artikeln om att [konfigurera distribution](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017) där distributionsservern visar NULL för ovanstående utdata från kommandot.

- Inaktivera databasutlösare i målets Azure SQL Database.
    >[!NOTE]
    > Du hittar databasutlösarna i målets Azure SQL Database med följande fråga:
    ```
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Mer information finns i artikeln [om att inaktivera utlösare (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Utvärdera din lokala databas
Innan du kan migrera data från en lokal SQL Server-instans till Azure SQL Database måste du utvärdera SQL Server-databasen om det finns några blockeringsproblem som kan förhindra migreringen. Använd Data Migration Assistant version 3.3 eller senare och följ stegen som beskrivs i artikeln om att [utföra en SQL Server-migreringsutvärdering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) för att slutföra den lokala databasutvärderingen.

Om du vill utvärdera en lokal databas utför du följande steg:
1.  I DMA väljer du ikonen Nytt (+) och väljer sedan projekttypen **Utvärdering**.
2.  Ange ett projektnamn. I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Azure SQL Database**. Välj sedan **Skapa** för att skapa projektet.

    När du utvärderar SQL Server-källdatabasen som migreras till Azure SQL Database kan du välja en eller båda av följande utvärderingsrapporttyper:
    - Kontrollera databaskompatibilitet
    - Kontrollera funktionsparitet

    Båda rapporttyperna väljs som standard.

3.  På skärmen **Alternativ** i DMA väljer du **Nästa**.
4.  På sidan **Välj källor** i dialogrutan **Anslut till en server**, anger du anslutningsinformationen till SQL Server och väljer sedan **Anslut**.
5.  I dialogrutan **Lägg till källor** väljer du **AdventureWorks2012**. Välj **Lägg till** och välj sedan **Starta utvärderingen**.

    När utvärderingen är klar visas resultatet enligt följande bild:

    ![Utvärdera datamigrering](media\tutorial-sql-server-to-azure-sql-online\dma-assessments.png)

    För Azure SQL Database identifierar utvärderingarna funktionsparitetsproblem och blockeringsproblem för migreringen.

    - Kategorin **SQL Server-funktionsparitet** innehåller en omfattande uppsättning rekommendationer, alternativa metoder tillgängliga i Azure och minimeringsåtgärder för att hjälpa dig att planera insatsen för dina migreringsprojekt.
    - Kategorin **Kompatibilitetsproblem** identifierar funktioner som stöds delvis eller inte alls, som speglar kompatibilitetsproblem som kan blockera migreringen av lokala SQL Server-databaser till Azure SQL Database. Det ges även rekommendationer, så att du kan hantera problemen.

6.  Granska utvärderingsresultaten för blockeringsproblem för migreringen och funktionsparitetsproblem genom att välja de specifika alternativen.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat
När du är nöjd med utvärderingen och finner att den valda databasen är en giltig kandidat för migrering till Azure SQL Database använder du DMA för att migrera schemat till Azure SQL Database.

> [!NOTE]
> Innan du skapar ett migreringsprojekt i DMA ska du redan ha etablerat en Azure SQL-databas så som anges i förutsättningarna. För den här självstudiekursen antas namnet på Azure SQL Database vara **AdventureWorksAzure** men du kan ange ett valfritt namn.

För att migrera **AdventureWorks2012**-schemat till Azure SQL Database gör du följande:

1.  I Data Migration Assistant väljer du ikonen Nytt (+) och sedan under **Projekttyp** väljer du **Migrering**.
2.  Ange ett projektnamn i textrutan **Typ av källserver**, välj **SQL Server**. I textrutan **Målservertyp** väljer du sedan **Azure SQL Database**.
3.  Under **Migreringsomfång** väljer du **Endast schema**.

    När du har utfört de föregående stegen bör DMA-gränssnittet visas i följande bild:
    
    ![Skapa Data Migration Assistant-projekt](media\tutorial-sql-server-to-azure-sql-online\dma-create-project.png)

4.  Välj **Skapa** för att skapa projektet.
5.  I DMA anger du källanslutningsinformationen för SQL Server. Välj **Anslut** och välj databasen **AdventureWorks2012**.

    ![Källanslutningsinformation för Data Migration Assistant](media\tutorial-sql-server-to-azure-sql-online\dma-source-connect.png)

6.  Välj **Next**, under **Anslut till målserver**, ange målanslutningsinformationen för Azure SQL-databasen, välj **Anslut** och välj sedan databasen **AdventureWorksAzure** som du har företablerat i Azure SQL Database.

    ![Målanslutningsinformation för Data Migration Assistant](media\tutorial-sql-server-to-azure-sql-online\dma-target-connect.png)

7.  Välj **Nästa** för att komma till sidan **Välj objekt**, där du kan ange schemaobjekten i databasen **AdventureWorks2012** som måste distribueras till Azure SQL Database.

    Som standard markeras alla objekt.

    ![Skapa SQL-skript](media\tutorial-sql-server-to-azure-sql-online\dma-assessment-source.png)

8.  Välj **Skapa SQL-skript** för att skapa SQL-skripten och granska sedan om det finns fel i skripten.

    ![Schemaskript](media\tutorial-sql-server-to-azure-sql-online\dma-schema-script.png)

9.  Välj **Distribuera schema** för att distribuera schemat till Azure SQL Database, och när schemat har distribuerats kontrollerar du om det finns avvikelser på målservern.

    ![Distribuera schema](media\tutorial-sql-server-to-azure-sql-online\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration
1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.
 
   ![Visa portalprenumerationer](media\tutorial-sql-server-to-azure-sql-online\portal-select-subscription1.png)
       
2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.
 
    ![Visa resursprovidrar](media\tutorial-sql-server-to-azure-sql-online\portal-select-resource-provider.png)
    
3.  Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.
 
    ![Registrera resursprovider](media\tutorial-sql-server-to-azure-sql-online\portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans
1.  I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql-online\portal-marketplace.png)

2.  På sidan **Azure Database Migration Service** väljer du **Skapa**.
 
    ![Skapa Azure Database Migration Service-instans](media\tutorial-sql-server-to-azure-sql-online\dms-create1.png)
  
3.  På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll-SQL Server och målinstansen av Azure SQL Database.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [Skapa ett virtuellt nätverk med hjälp av Azure-portalen](https://aka.ms/DMSVnet).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    Om du behöver hjälp med att välja rätt Azure Database Migration Service-nivå läser du rekommendationerna i inlägget [här](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Konfigurera Azure Database Migration Service-instansinställningar](media\tutorial-sql-server-to-azure-sql-online\dms-settings2.png)

7.  Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt
När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.
 
      ![Leta reda på alla instanser Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql-online\dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.
 
     ![Hitta din instans av Azure Database Migration Service](media\tutorial-sql-server-to-azure-sql-online\dms-instance-search.png)
 
3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Azure SQL Database**.
5. I avsnittet **Välj aktivitetstyp** väljer du **Online-datamigrering**

    ![Skapa Database Migration Service-projekt](media\tutorial-sql-server-to-azure-sql-online\dms-create-project3.png)

    > [!NOTE]
    > Du kan även välja **Skapa endast projekt** för att skapa migreringsprojektet nu och köra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    ![Skapa och köra en Azure Database Migration Service-aktivitet](media\tutorial-sql-server-to-azure-sql-online\dms-create-and-run-activity.png)
 
## <a name="specify-source-details"></a>Ange källinformation
1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källinstansen.
 
    Se till att använda ett fullständigt domännamn (FQDN) för SQL Server-källinstansens namn. Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

2. Om du inte har installerat ett betrott certifikat på källservern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som har krypterats med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte lita på SSL som använder självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till internet.

   ![Källinformation](media\tutorial-sql-server-to-azure-sql-online\dms-source-details3.png)

## <a name="specify-target-details"></a>Ange målinformation
1. Välj **Spara** och ange på sidan **Information om migreringsmål** anslutningsinformationen för Azure SQL Database Server-målet, som är den företablerade Azure SQL Database som **AdventureWorks2012**-schemat distribuerades till med DMA.

    ![Välja mål](media\tutorial-sql-server-to-azure-sql-online\dms-select-target3.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media\tutorial-sql-server-to-azure-sql-online\dms-map-targets-activity3.png)

3. Välj **Spara**. Expandera tabellistan på sidan **Välj tabeller** och granska sedan listan över berörda fält.

    Azure Database Migration Service väljer automatiskt alla tomma källtabeller som finns på Azure SQL Database-målinstansen. Om du vill migrera tabeller på nytt som redan innehåller data måste du uttryckligen välja tabellerna på det här bladet.

    ![Välj tabeller](media\tutorial-sql-server-to-azure-sql-online\dms-configure-setting-activity3.png)

4.  Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media\tutorial-sql-server-to-azure-sql-online\dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen
- Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

    ![Aktivitetsstatus – initieras](media\tutorial-sql-server-to-azure-sql-online\dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen
1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

2. Klicka på en specifik databas för att komma till migreringsstatus för åtgärderna **Fullständig datainläsning** och **Inkrementell datasynkronisering**.

    ![Aktivitetsstatus – Pågår](media\tutorial-sql-server-to-azure-sql-online\dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering
När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Starta snabb](media\tutorial-sql-server-to-azure-sql-online\dms-start-cutover.png)
 
2.  Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3.  Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya Azure SQL-måldatabasen.
 
    ![Aktivitetsstatus – slutförd](media\tutorial-sql-server-to-azure-sql-online\dms-activity-completed.png)

## <a name="next-steps"></a>Nästa steg
- Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for MySQL finns i artikeln [Known issues and workarounds with Azure SQL Database online migrations](known-issues-azure-sql-online.md) (Kända problem och lösningar med Azure SQL Database-onlinemigreringar).
- Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
- Information om Azure SQL Database finns i artikeln [Vad är tjänsten SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
