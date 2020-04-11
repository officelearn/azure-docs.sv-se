---
title: 'Självstudiekurs: Migrera RDS PostgreSQL online till Azure Database för PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en onlinemigrering från RDS PostgreSQL till Azure Database för PostgreSQL med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/11/2020
ms.openlocfilehash: be6f0cd734d31f43557b49f8e9314e925b383899
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113953"
---
# <a name="tutorial-migrate-rds-postgresql-to-azure-db-for-postgresql-online-using-dms"></a>Självstudiekurs: Migrera RDS PostgreSQL till Azure DB för PostgreSQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaser från en RDS PostgreSQL-instans till [Azure Database för PostgreSQL](https://docs.microsoft.com/azure/postgresql/) medan källdatabasen är online under migreringen. Med andra ord kan migrering uppnås med minimal stilleståndstid till programmet. I den här självstudien migrerar du **exempeldatabasen för DVD-uthyrning** från en instans av RDS PostgreSQL 9.6 till Azure Database for PostgreSQL med hjälp av onlinemigreringsaktiviteten i Azure Database Migration Service.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
>
> * Migrera exempelschemat med hjälp av verktyget pg_dump.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.
> * Utför migreringsöverskärning.

> [!NOTE]
> Användning av Azure Database Migration Service för att utföra en onlinemigrering kräver att en instans skapas baserat på prisnivån Premium. Mer information finns i på [prissättningssidan](https://azure.microsoft.com/pricing/details/database-migration/) för Azure Database Migration Service. Vi krypterar disk för att förhindra datastöld under migreringen.

> [!IMPORTANT]
> För optimala migreringsfunktioner rekommenderar Microsoft att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den här artikeln beskrivs hur du utför en onlinemigrering från en lokal instans av PostgreSQL till Azure Database för PostgreSQL.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Ladda ned och installera [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 eller 10. PostgreSQL-källserverversionen måste vara 9.5.11, 9.6.7, 10 eller senare. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

   Observera också att mål azure-databasen för PostgreSQL-versionen måste vara lika med eller senare än RDS PostgreSQL-versionen. Rds PostgreSQL 9.6 kan till exempel bara migrera till Azure Database för PostgreSQL 9.6, 10 eller 11, men inte till Azure Database för PostgreSQL 9.5.

* Skapa en instans av [Azure Database för PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) eller [Azure Database för PostgreSQL - Hyperscale (Citus)](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal). Mer [information om](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal#connect-to-the-postgresql-server-using-pgadmin) hur du ansluter till PostgreSQL-servern finns i det här avsnittet i dokumentet.
* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/)till det virtuella nätverket och särskilt snabbstartsartiklarna med steg-för-steg-information.
* Kontrollera att dina regler för nätverkssäkerhetsgrupp för virtuella nätverk inte blockerar följande inkommande kommunikationsportar till Azure Database Migration Service: 443, 53, 9354, 445 och 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen så att Azure Database Migration Service kan komma åt källpostgreSQL-servern, som som standard är TCP-port 5432.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure Database for PostgreSQL-servern för att tillåta Azure Database Migration Service åtkomst till måldatabaserna. Ange undernätsområdet för det virtuella nätverk som används för Azure Database Migration Service.

### <a name="set-up-aws-rds-postgresql-for-replication"></a>Konfigurera AWS RDS PostgreSQL för replikering

1. Om du vill skapa en ny parametergrupp följer du instruktionerna från AWS i artikeln [Arbeta med DB-parametergrupper](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithParamGroups.html).
2. Använd huvudanvändarnamnet för att ansluta till källan från Azure Database Migration Service. Om du använder ett annat konto än huvudanvändarkontot måste kontot ha den rds_superuser rollen och rds_replication roll. Den rds_replication rollen ger behörighet att hantera logiska platser och strömma data med hjälp av logiska platser.
3. Skapa en ny parametergrupp med följande konfiguration:

    a. Ange parametern rds.logical_replication i parametergruppen DB till 1.

    b. max_wal_senders =[antal samtidiga aktiviteter] - Parametern max_wal_senders anger antalet samtidiga aktiviteter som kan köras, rekommenderar 10 aktiviteter.

    c. max_replication_slots – = [antal platser], rekommenderar inställd på fem platser.

4. Associera parametergruppen som du skapade till RDS PostgreSQL-instansen.

## <a name="migrate-the-schema"></a>Migrera schemat

1. Extrahera schemat från källdatabasen och tillämpas på måldatabasen för att slutföra migreringen av alla databasobjekt, till exempel tabellscheman, index och lagrade procedurer.

    Det enklaste sättet att migrera endast schemat är att använda pg_dump med alternativet -s. Mer information finns i [exemplen](https://www.postgresql.org/docs/9.6/app-pgdump.html#PG-DUMP-EXAMPLES) i självstudiekursen Postgres pg_dump.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Om du till exempel vill dumpa en schemafil för **dvdrental-databasen** använder du följande kommando:

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

2. Skapa en tom databas i måltjänsten, som är Azure Database för PostgreSQL. Om du vill ansluta och skapa en databas läser du någon av följande artiklar:

    * [Skapa en Azure-databas för PostgreSQL-server med hjälp av Azure-portalen](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal)
    * [Skapa en Azure-databas för PostgreSQL - Citus-server (Hyperscale) med Azure-portalen](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)

3. Importera schemat till måltjänsten, som är Azure Database för PostgreSQL. Om du vill återställa schemadumpfilen kör du följande kommando:

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Ett exempel:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. Om du vill extrahera skriptet för att släppa sekundärnyckel och lägga till skript för sekundärnyckel vid målet (Azure Database for PostgreSQL) kör du följande skript i PgAdmin eller i psql:
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

5. Kör den bortsläppa sekundärnyckeln (som är den andra kolumnen) i frågeresultatet för att släppa sekundärnyckeln.

6. Om du har utlösare (infoga eller uppdatera utlösare) i data, kommer det att framtvinga dataintegritet i målet innan replikera data från källan. Rekommendationen är att inaktivera utlösare i alla tabeller *vid målet* under migreringen och sedan aktivera utlösare när migreringen är klar.

    Så här inaktiverar du utlösare i måldatabasen:

    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **Resursleverantörer**.

    ![Visa resursprovidrar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Skapa en instans av Azure Database Migration Service

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service.

5. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan PostgreSQL-instans och målet Azure Database för PostgreSQL-instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [Skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

6. Välj en prisnivå. för den här onlinemigreringen, se till att välja prisnivån Premium: 4vCores.

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-settings5.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-search.png)

2. På skärmen **Azure Database Migration Services** söker du efter namnet på azure Database Migration Service-instansen som du skapade, väljer instansen och väljer sedan + Nytt **migreringsprojekt**.
3. På skärmen **Nytt migreringsprojekt** anger du ett namn för projektet, i textrutan **Källservertyp,** väljer **AWS RDS för PostgreSQL**och väljer sedan **Azure-databas för PostgreSQL**i **textrutan** Källaserver.
4. I avsnittet **Välj aktivitetstyp** väljer du **Datamigrering online**.

    > [!IMPORTANT]
    > Var noga med att välja **Online datamigrering;** offlinemigreringar stöds inte för det här scenariot.

    ![Skapa Database Migration Service-projekt](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-create-project5.png)

    > [!NOTE]
    > Alternativt kan du **välja Skapa projekt bara** för att skapa migreringsprojektet nu och köra migreringen senare.

5. Välj **Spara**.

6. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

    > [!NOTE]
    > Anteckna de förutsättningar som krävs för att ställa in onlinemigrering i bladet för att skapa projekt.

## <a name="specify-source-details"></a>Ange källinformation

* På skärmen **Lägg till källinformation** anger du anslutningsinformation för källpostlagreSQL-instansen.

   ![Källinformation](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara**och ange **Target details** sedan anslutningsinformation för målinformationen för Azure-databasen för PostgreSQL-servern, som är företablerade och har **DVD Rentals-schemat** distribuerat med pg_dump.

    ![Målinformation](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-target-details.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-map-target-databases.png)

3. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Migreringssammanfattning](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Initieras**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Körs**.

    ![Aktivitetsstatus - körs](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-activity-status3.png)

2. Under **DATABASNAMN**väljer du en specifik databas för att komma åt migreringsstatus för **fullständiga datainläsningar** och **inkrementella datasynkroniseringsåtgärder.**

    **Fullständig datainläsning** visar den ursprungliga belastningsmigreringsstatusen, medan **inkrementell datasynkronisering** visar status för ändringsdatainsamling (CDC).

    ![Lagerskärm - fullständig databelastning](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-full-load.png)

    ![Lagerskärm - inkrementell datasynkronisering](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den första fullständiga inläsningen är klar markeras databaserna **Redo att klippa över**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Vänta tills räknaren **Väntande ändringar** visar **0** för att säkerställa att alla inkommande transaktioner till källdatabasen stoppas, markera kryssrutan **Bekräfta** och välj sedan **Använd**.

    ![Komplett cutover-skärm](media/tutorial-rds-postgresql-server-azure-db-for-postgresql-online/dms-complete-cutover.png)

3. När databasmigreringsstatusen visar **Slutförd**ansluter du dina program till den nya Azure-databasen för PostgreSQL-databasen.

Din onlinemigrering av en lokal instans av RDS PostgreSQL till Azure Database för PostgreSQL är nu klar.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Information om Azure Database för PostgreSQL finns i artikeln [Vad är Azure Database för PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
* Om du vill ha andra frågor kan du skicka alias Ask [Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) till e-post.
