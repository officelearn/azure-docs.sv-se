---
title: 'Självstudie: Migrera SQL Server online till en SQL-databas'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en online-migrering från SQL Server till Azure SQL Database genom att använda Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/21/2020
ms.openlocfilehash: 8a34b0ae819f7bbb29a0004a89ea5417b52c127e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392681"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Självstudie: Migrera SQL Server till en enskild databas eller en databas i pooler i Azure SQL Database online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en SQL Server-instans till [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) med minimal stillestånds tid. I den här självstudien migrerar du databasen **Adventureworks2012** från en återställd databas till en lokal instans av SQL Server 2016 (eller senare) till en enkel databas eller en pooldatabas i Azure SQL Database med hjälp av Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> - Utvärdera din lokala databas med hjälp av Data Migration Assistant.
> - Migrera exempelschemat med hjälp av Data Migration Assistant.
> - Skapa en instans av Azure Database Migration Service.
> - Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> - Köra migreringen.
> - Övervaka migreringen.
> - Ladda ned en migreringsrapport.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserat på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I artikeln beskrivs en onlinemigrering från SQL Server till en enkel databas eller pooldatabas i Azure SQL Database. En offlinemigrering beskrivs i [Migrera SQL Server till Azure SQL Database offline med DMS](tutorial-sql-server-to-azure-sql.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Hämta och installera [SQL Server 2012 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads).
- Aktivera TCP/IP-protokollet, som är inaktiverat som standard under SQL Server Express-installation, genom att följa instruktionerna i artikeln om att [aktivera eller inaktivera ett servernätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Skapa en enkel databas (eller en pooldatabas) i Azure SQL Database. Det gör du genom att följa anvisningarna i artikeln [Skapa en enkel databas i Azure SQL Database med Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Om du använder SQL Server Integration Services (SSIS) och vill migrera katalogdatabasen för dina SSIS-projekt/-paket (SSISDB) från SQL Server till Azure SQL Database skapas och hanteras målet SSISDB och automatiskt åt dig när du etablerar SSIS i Azure Data Factory (ADF). Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

- Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) version 3.3 eller senare.
- Skapa en Microsoft Azure Virtual Network för Azure Database Migration Service med hjälp av Azure Resource Manager distributions modell, som tillhandahåller plats-till-plats-anslutning till dina lokala käll servrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i [Virtual Network-dokumentationen](https://docs.microsoft.com/azure/virtual-network/)och i synnerhet snabb starts artiklar med stegvisa anvisningar.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks-peering till Microsoft under installationen av det virtuella nätverket lägger du till följande tjänst [slut punkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i under nätet där tjänsten ska tillhandahållas:
    > - Slut punkt för mål databas (till exempel SQL-slutpunkt, Cosmos DB slut punkt och så vidare)
    > - Lagrings slut punkt
    > - Service Bus-slutpunkt
    >
    > Den här konfigurationen är nödvändig eftersom den Azure Database Migration Service saknar Internet anslutning.

- Se till att de virtuella nätverkets säkerhets grupp regler inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om Azure Virtual Network NSG trafik filtrering finns i artikeln [filtrera nätverks trafik med nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433. Om standard instansen lyssnar på någon annan port lägger du till den porten i brand väggen.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
- När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
- Skapa en [brand Väggs regel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på server nivå för Azure SQL Database för att tillåta Azure Database migration service åtkomst till mål databaserna. Ange under nätets intervall för det virtuella nätverk som används för Azure Database Migration Service.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen har [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)-behörigheter.
- Se till att de autentiseringsuppgifter som används för att ansluta till mål Azure SQL Databases instansen har kontroll databas behörighet på mål Azure SQL Database instanserna.
- Källversionen för SQL Server måste vara SQL Server 2005 och senare. För att fastställa vilken version av SQL Server-instansen som körs kan du läsa artikeln [Hur man fastställer version, utgåva, och uppdateringsnivå för SQL Server och dess komponenter](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Databaserna måste antingen vara bulkloggade eller i fullständigt återställningsläge. För att fastställa återställningsmodellen som har konfigurerats för din SQL Server-instans kan du läsa artikeln [Visa eller ändra återställningsmodellen för en databas (Microsoft SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Se till att göra fullständiga databassäkerhetskopieringar för databaserna. Om du vill skapa en fullständig säkerhetskopiering av databasen kan du läsa artikeln [How to: Create a Full Database Backup (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)) (Så här skapar du en fullständig säkerhetskopiering av databasen (Transact-SQL)).
- Om någon av tabellerna saknar en primärnyckel kan du aktivera sammanställning av ändringsdata (CDC) på databasen och specifika tabeller.
    > [!NOTE]
    > Du kan använda skriptet nedan för att hitta tabeller som inte har några primärnycklar.
    
    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
    ```

    Om resultatet visar en eller flera tabeller med "is_tracked_by_cdc" som "0" aktiverar du ändrad Capture för databasen och för de specifika tabellerna med processen som beskrivs i artikeln [Enable and Disable Change Data Capture (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017) (Aktivera och inaktivera sammanställning av ändringsdata (SQL Server)).

- Konfigurera distributörsrollen för SQL Server-källan.

    >[!NOTE]
    > Du kan fastställa om replikeringskomponenterna har installerats med nedanstående fråga.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    Om resultatet returnerar ett felmeddelande som förslår att du ska installera replikeringskomponenter ska du installera SQL Server-replikeringskomponenter med hjälp av processen i artikeln [Install SQL Server replication](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017) (Installera SQL Server-replikering).

    Om replikering redan har installerats kontrollerar du om distributionsrollen har konfigurerats på SQL Server-källan med hjälp av T-SQL-kommandot nedan.

    ```sql
    EXEC sp_get_distributor;
    ```

    Om distributionen inte har konfigurerats konfigurerar du distributionen med vägledningen i artikeln om att [konfigurera distribution](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017) där distributionsservern visar NULL för ovanstående utdata från kommandot.

- Inaktivera databasutlösare i målets Azure SQL Database.
    >[!NOTE]
    > Du hittar databasutlösarna i målets Azure SQL Database med följande fråga:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    Mer information finns i artikeln [om att inaktivera utlösare (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Utvärdera din lokala databas

Innan du kan migrera data från en SQL Server-instans till Azure SQL Database måste du utvärdera SQL Server databasen för eventuella spärrnings problem som kan förhindra migrering. Använd Data Migration Assistant version 3.3 eller senare och följ stegen som beskrivs i artikeln om att [utföra en SQL Server-migreringsutvärdering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) för att slutföra den lokala databasutvärderingen.

Utför följande steg för att utvärdera en lokal databas:

1. I DMA väljer du ikonen Nytt (+) och väljer sedan projekttypen **Utvärdering**.
2. Ange ett projektnamn. I textrutan **Typ av källserver** väljer du **SQL Server** , i textrutan **Målservertyp** väljer du **Azure SQL Database**. Välj sedan **Skapa** för att skapa projektet.

   När du utvärderar SQL Server-källdatabasen som ska migreras till en enkel databas eller pooldatabas i Azure SQL Database, kan du välja en eller båda av följande utvärderingsrapporttyper:

   - Kontrollera databaskompatibilitet
   - Kontrollera funktionsparitet

   Båda rapporttyperna väljs som standard.

3. På skärmen **Alternativ** i DMA väljer du **Nästa**.
4. På sidan **Välj källor** i dialogrutan **Anslut till en server** , anger du anslutningsinformationen till SQL Server och väljer sedan **Anslut**.
5. I dialogrutan **Lägg till källor** väljer du **AdventureWorks2012**. Välj **Lägg till** och välj sedan **Starta utvärderingen**.

    > [!NOTE]
    > Om du använder SSIS så har DMA för närvarande inget stöd för utvärdering av SSISDB-källan. Dock utvärderas/verifieras SSIS-projekt/-paket när de omdistribueras till SSISDB-målet med Azure SQL Database. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

    När utvärderingen är klar visas resultatet enligt följande bild:

    ![Utvärdera datamigrering](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Vid enkla databaser eller pooldatabaser i Azure SQL Database identifierar utvärderingarna problem med funktionsparitet och blockering vid migrering till en enkel databas eller pooldatabas.

    - Kategorin **SQL Server-funktionsparitet** innehåller en omfattande uppsättning rekommendationer, alternativa metoder tillgängliga i Azure och minimeringsåtgärder för att hjälpa dig att planera insatsen för dina migreringsprojekt.
    - Kategorin **kompatibilitetsproblem** identifierar funktioner som stöds delvis eller som inte stöds och som avspeglar kompatibilitetsproblem som kan blockera migrering av SQL Server databas (er) till Azure SQL Database. Det ges även rekommendationer, så att du kan hantera problemen.

6. Granska utvärderingsresultaten för blockeringsproblem för migreringen och funktionsparitetsproblem genom att välja de specifika alternativen.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

När du är nöjd med utvärderingen och finner att den valda databasen är en giltig kandidat för migrering till en enkel databas eller en pooldatabas i Azure SQL Database använder du DMA för att migrera schemat till Azure SQL Database.

> [!NOTE]
> Innan du skapar ett migreringsjobb i DMA bör du se till att du redan har skapat en SQL-databas i Azure som nämns i kraven. För den här självstudiekursen antas namnet på Azure SQL Database vara **AdventureWorksAzure** men du kan ange ett valfritt namn.

> [!IMPORTANT]
> Om du använder SSIS stöder DMA för närvarande inte migrering av käll-SSISDB, men du kan distribuera om ditt SSIS-projekt/-paket till SSISDB-målet med Azure SQL Database. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

Gör så här om du vill migrera **AdventureWorks2012** -schemat till en databas eller databas Azure SQL Database:

1. I Data Migration Assistant väljer du ikonen Nytt (+) och sedan under **Projekttyp** väljer du **Migrering**.
2. Ange ett projektnamn i textrutan **Typ av källserver** , välj **SQL Server**. I textrutan **Målservertyp** väljer du sedan **Azure SQL Database**.
3. Under **Migreringsomfång** väljer du **Endast schema**.

    När du har utfört de föregående stegen bör DMA-gränssnittet visas i följande bild:

    ![Skapa Data Migration Assistant-projekt](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. Välj **Skapa** för att skapa projektet.
5. I DMA anger du källanslutningsinformationen för SQL Server. Välj **Anslut** och välj databasen **AdventureWorks2012**.

    ![Källanslutningsinformation för Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Välj **Next** , under **Anslut till målserver** , ange målanslutningsinformationen för Azure SQL-databasen, välj **Anslut** och välj sedan databasen **AdventureWorksAzure** som du har företablerat i Azure SQL Database.

    ![Målanslutningsinformation för Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. Välj **Nästa** för att komma till sidan **Välj objekt** , där du kan ange schemaobjekten i databasen **AdventureWorks2012** som måste distribueras till Azure SQL Database.

    Som standard markeras alla objekt.

    ![Skapa SQL-skript](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Välj **Skapa SQL-skript** för att skapa SQL-skripten och granska sedan om det finns fel i skripten.

    ![Schemaskript](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Välj **Distribuera schema** för att distribuera schemat till Azure SQL Database, och när schemat har distribuerats kontrollerar du om det finns avvikelser på målservern.

    ![Distribuera schema](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera** till höger om **Microsoft. data migration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Skapa en instans

1. I Azure Portal väljer du + **Skapa en resurs** , söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till käll SQL Server och mål Azure SQL Database instansen.

    Mer information om hur du skapar ett virtuellt nätverk i Azure Portal finns i artikeln [skapa ett virtuellt nätverk med hjälp av Azure Portal](https://aka.ms/DMSVnet).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster** , söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

    ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

    ![Hitta din instans av Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, I textrutan **Typ av källserver** väljer du **SQL Server** , i textrutan **Målservertyp** väljer du **Azure SQL Database**.
5. I avsnittet **Välj typ av aktivitet** väljer du **migrering av data online**.

    ![Skapa Database Migration Service-projekt](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > Alternativt kan du välja **skapa endast projekt** för att skapa migreringsjobbet nu och utföra migreringen senare.

6. Välj **Spara**.

7. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    ![Skapa och köra en Azure Database Migration Service-aktivitet](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källinstansen.

    Se till att använda ett fullständigt domännamn (FQDN) för SQL Server-källinstansens namn. Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

2. Om du inte har installerat ett betrott certifikat på källservern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > TLS-anslutningar som krypteras med hjälp av ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte förlita dig på TLS med självsignerade certifikat i en produktions miljö eller på servrar som är anslutna till Internet.

   ![Källinformation](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Om du använder SSIS stöder DMS för närvarande inte migrering av käll-SSISDB, men du kan distribuera om ditt SSIS-projekt/-paket till SSISDB-målet med Azure SQL Database. Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara** och ange sedan anslutnings information för mål Azure SQL Database på skärmen **information om migreringen** , vilket är den företablerade Azure SQL Database som **AdventureWorks2012** -schemat har distribuerats med hjälp av DMA.

    ![Välja mål](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Välj **Spara**. Expandera tabellistan på sidan **Välj tabeller** och granska sedan listan över berörda fält.

    Azure Database Migration Service väljer automatiskt alla tomma källtabeller som finns på Azure SQL Database-målinstansen. Om du vill migrera tabeller på nytt som redan innehåller data måste du uttryckligen välja tabellerna på det här bladet.

    ![Välj tabeller](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

- Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

    ![Aktivitetsstatus – initieras](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

2. Klicka på en specifik databas för att komma till migreringsstatus för åtgärderna **Fullständig datainläsning** och **Inkrementell datasynkronisering**.

    ![Aktivitetsstatus – Pågår](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

    ![Starta snabb](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Stoppa alla inkommande transaktioner till källdatabasen och vänta tills **Väntande ändringar** visar **0**.
3. Välj **Bekräfta** och sedan **Apply** (Använd).
4. När status för databasmigreringen visar **Slutförd** ansluter du dina program till den nya Azure SQL-måldatabasen.

    ![Aktivitetsstatus – slutförd](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>Nästa steg

- Information om kända problem och begränsningar när du utför onlinemigreringar till Azure SQL Database finns i artikeln [Known issues and workarounds with Azure SQL Database online migrations](known-issues-azure-sql-online.md) (Kända problem och lösningar med Azure SQL Database-onlinemigreringar).
- Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
- Information om Azure SQL Database finns i artikeln [Vad är tjänsten SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
