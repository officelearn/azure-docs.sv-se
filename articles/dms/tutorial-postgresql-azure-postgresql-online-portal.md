---
title: 'Självstudiekurs: Migrera PostgreSQL till Azure DB för PostgreSQL online via Azure-portalen'
titleSuffix: Azure Database Migration Service
description: Lär dig att utföra en onlinemigrering från PostgreSQL lokalt till Azure Database för PostgreSQL med hjälp av Azure Database Migration Service via Azure-portalen.
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
ms.openlocfilehash: e01cc1c07d720c4743a03b5001e640f8b851dd5c
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114004"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-portal"></a>Självstudiekurs: Migrera PostgreSQL till Azure DB för PostgreSQL online med DMS via Azure-portalen

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal PostgreSQL-instans till [Azure Database för PostgreSQL](https://docs.microsoft.com/azure/postgresql/) med minimal stilleståndstid till programmet. I den här självstudien migrerar du **exempeldatabasen för DVD-uthyrning** från en lokal instans av PostgreSQL 9.6 till Azure Database för PostgreSQL med hjälp av onlinemigreringsaktiviteten i Azure Database Migration Service.

I den här självstudiekursen får du lära du dig att:
> [!div class="checklist"]
>
> * Migrera exempelschemat med hjälp av verktyget pg_dump.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt i Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.
> * Utför migreringsöverskärning.

> [!NOTE]
> För att använda Azure Database Migration Service för att utföra en onlinemigrering måste du skapa en instans baserat på premiumprisnivån. Vi krypterar disk för att förhindra datastöld under migreringen

> [!IMPORTANT]
> För en optimal migreringsupplevelse rekommenderar Microsoft att du skapar en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

* Ladda ner och installera [PostgreSQL community edition](https://www.postgresql.org/download/) 9.4, 9.5, 9.6 eller 10. Källversionen av PostgreSQL Server måste vara 9.4, 9.5, 9.6, 10 eller 11. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Observera också att mål azure-databasen för PostgreSQL-versionen måste vara lika med eller senare än den lokala PostgreSQL-versionen. PostgreSQL 9.6 kan till exempel migrera till Azure Database för PostgreSQL 9.6, 10 eller 11, men inte till Azure Database för PostgreSQL 9.5.

* [Skapa en Azure-databas för PostgreSQL-server](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) eller [Skapa en Azure-databas för Citus-server (Hyperscale ).](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal)
* Skapa en Microsoft Azure Virtual Network for Azure Database Migration Service med hjälp av distributionsmodellen för Azure Resource Manager, som tillhandahåller anslutning från plats till plats till dina lokala källservrar med hjälp av [Antingen ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i [dokumentationen](https://docs.microsoft.com/azure/virtual-network/)till det virtuella nätverket och särskilt snabbstartsartiklarna med steg-för-steg-information.

    > [!NOTE]
    > Om du använder ExpressRoute med nätverks peering i Microsoft under installationen av virtuella nätverk lägger du till följande [tjänstslutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) i undernätet där tjänsten ska etableras:
    >
    > * Slutpunkt för måldatabas (till exempel SQL-slutpunkt, Cosmos DB-slutpunkt och så vidare)
    > * Slutpunkt för lagring
    > * Ändpunkt för servicebuss
    >
    > Den här konfigurationen är nödvändig eftersom Azure Database Migration Service saknar internetanslutning.

* Kontrollera att NSG-reglerna (Network Security Group) för det virtuella nätverket inte blockerar följande inkommande kommunikationsportar till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om filtrering av NSG-trafik i det virtuella nätverket finns i artikeln [Filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna Windows-brandväggen så att Azure Database Migration Service kan komma åt källan PostgreSQL Server, som som standard är TCP-port 5432.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure Database for PostgreSQL så att Azure Database Migration Service kan komma åt måldatabaserna. Ange undernätsområdet för det virtuella nätverk som används för Azure Database Migration Service.
* Aktivera logisk replikering i filen postgresql.config och ange följande parametrar:

  * wal_level = **logical**
  * max_replication_slots = [antal platser], rekommenderar att du ställer in **på fem platser**
  * max_wal_senders = [antalet samtidiga uppgifter] – parametern max_wal_senders anger antal samtidiga aktiviteter som kan köras, rekommenderad inställning är **10 uppgifter**

> [!IMPORTANT]
> Alla tabeller i den befintliga databasen behöver en primärnyckel för att säkerställa att ändringar kan synkroniseras med måldatabasen.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

För att slutföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på databasen.

1. Använd pg_dump -s-kommandot för att skapa en schemadumpfil för en databas.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Om du till exempel vill skapa en schemadumpfil för **dvdrental-databasen:**

    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Mer information om hur du använder verktyget pg_dump finns i exemplen i självstudien [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Skapa en tom databas i målmiljön, vilken är Azure Database for PostgreSQL.

    Mer information om hur du ansluter och skapar en databas finns i artikeln [Skapa en Azure Database för PostgreSQL-server i Azure-portalen](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) eller Skapa en [Azure-databas för PostgreSQL - Citus-server (Hyperscale) i Azure-portalen](https://docs.microsoft.com/azure/postgresql/quickstart-create-hyperscale-portal).

    > [!NOTE]
    > En instans av Azure Database for PostgreSQL - Hyperscale (Citus) har bara en enda databas: **citus**.

3. Importera schemat till måldatabasen som du skapade genom att återställa schemadumpfilen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Ett exempel:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Om du vill extrahera skriptet för att släppa sekundärnyckel och lägga till det vid målet (Azure Database for PostgreSQL), i PgAdmin eller psql kör du följande skript.

   > [!IMPORTANT]
   > Externa nycklar i schemat gör att den första belastningen och den kontinuerliga synkroniseringen av migreringen misslyckas.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ', foreignkey), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ', table_schema, '.', table_name, STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ', foreignkey, ' FOREIGN KEY (', column_name, ')', ' REFERENCES ', foreign_table_schema, '.', foreign_table_name, '(', foreign_column_name, ')' ), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.

6. Om du vill inaktivera utlösare i måldatabasen kör du skriptet nedan.

   > [!IMPORTANT]
   > Utlösare (infoga eller uppdatera) i data framtvingar dataintegritet i målet före de data som replikeras från källan. Därför rekommenderar vi att du inaktiverar utlösare i alla tabeller **vid målet** under migreringen och sedan återaktivera utlösarna när migreringen är klar.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure Portal och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Välj den prenumeration där du vill skapa instansen av Azure Database Migration Service och välj sedan **Resursleverantörer**.

    ![Visa resursprovidrar](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Sök efter migrering och välj sedan **Registrera**till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Skapa en DMS-instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. På skärmen **Skapa migreringstjänst** anger du ett namn, en prenumeration, en ny eller befintlig resursgrupp och platsen för tjänsten.

4. Välj ett befintligt virtuellt nätverk eller skapa ett nytt.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källpostgreSQL-servern och målet Azure Database för PostgreSQL-instans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [Skapa ett virtuellt nätverk med Azure-portalen](https://aka.ms/DMSVnet).

5. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Välj **Granska + skapa** för att skapa tjänsten.

   Skapandet av tjänsten kommer att slutföras inom cirka 10 till 15 minuter.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta reda på alla instanser av Azure Database Migration Service](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. På skärmen **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen som du skapade, väljer instansen och väljer sedan + Nytt **migreringsprojekt**.

3. På skärmen **Nytt migreringsprojekt** anger du ett namn för projektet, i textrutan **Källservertyp,** väljer Dula för **PostgresSQL**i textrutan **Målservertyp,** väljer **Azure-databas för PostgreSQL**.

4. I avsnittet **Välj aktivitetstyp** väljer du **Datamigrering online**.

    ![Skapa azure database migration service-projekt](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternativt kan du **välja Skapa projekt bara** för att skapa migreringsprojektet nu och köra migreringen senare.

5. Välj **Spara**, notera kraven för att använda Azure Database Migration Service för att migrera data och välj sedan **Skapa och köra aktivitet**.

## <a name="specify-source-details"></a>Ange källinformation

1. På skärmen **Lägg till källinformation** anger du anslutningsinformation för källpostlagreSQL-instansen.

    ![Skärmen Lägg till källinformation](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

2. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På skärmen **Målinformation** anger du anslutningsinformationen för målservern Hyperscale (Citus), som är den företablerade instansen hyperskala (Citus) som **DVD Rentals-schemat** distribuerades till med hjälp av pg_dump.

    ![Skärmen Målinformation](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Karta för att rikta databasskärmen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Välj **Spara**och acceptera sedan standardvärdena på skärmen **Migreringsinställningar.**

    ![Skärmen Inställningar för migrering](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Välj **Spara**. I rutan **Aktivitetsnamn** på skärmen **Migreringssammanfattning** anger du ett namn för migreringsaktiviteten och granskar sedan sammanfattningen för att se till att informationen för källa och mål matchar det du angav tidigare.

    ![Sammanfattningsskärm för migrering](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Fönstret migreringsaktivitet visas och **aktivitetens status** ska uppdateras för att visas som **pågående säkerhetskopiering**.

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

     ![Övervakar migreringsprocessen](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. När migreringen är klar väljer du en specifik databas under **Databasnamn**för att komma åt migreringsstatusen för **fullständig datainläsning** och **inkrementella datasynkroniseringsåtgärder.**

   > [!NOTE]
   > **Fullständig datainläsning** visar den ursprungliga belastningsmigreringsstatusen, medan **inkrementell datasynkronisering** visar status för ändringsdatainsamling (CDC).

     ![Fullständig information om datainläsning](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Information om inkrementell datasynkronisering](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Utföra snabbmigrering

När den fullständiga inläsningen är klar är databaserna märkta med **Klar för snabbmigrering**.

1. När du är redo att slutföra databasmigreringen väljer du **Starta snabb**.

2. Vänta tills räknaren **Väntande ändringar** visar **0** för att säkerställa att alla inkommande transaktioner till källdatabasen stoppas, markera kryssrutan **Bekräfta** och välj sedan **Använd**.

    ![Komplett cutover-skärm](media/tutorial-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. När databasmigreringsstatusen visar **Slutförd**ansluter du dina program till den nya målinstansen i Azure Database för PostgreSQL.

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Information om Azure Database för PostgreSQL finns i artikeln [Vad är Azure Database för PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
