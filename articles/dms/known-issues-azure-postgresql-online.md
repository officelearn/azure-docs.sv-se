---
title: 'Kända problem: online-migreringar från PostgreSQL till Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Läs om kända problem och begränsningar för migrering med online-migrering från PostgreSQL till Azure Database for PostgreSQL-en server med hjälp av Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 10/27/2019
ms.openlocfilehash: c5c0015c5034dd3b30b716264fd97e9881b3fe67
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437861"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql-single-server"></a>Kända problem/migrerings begränsningar med online-migreringar från PostgreSQL till Azure DB för PostgreSQL-enskild server

Kända problem och begränsningar som är kopplade till online-migrering från PostgreSQL till Azure Database for PostgreSQL-enskild server beskrivs i följande avsnitt.

## <a name="online-migration-configuration"></a>Konfiguration av online-migrering

- PostgreSQL-servern måste köra version 9.5.11, 9.6.7 eller 10,3 eller senare. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](../postgresql/concepts-supported-versions.md).
- Det finns bara stöd för migrering av samma version. Exempelvis stöds inte migrering av PostgreSQL-9.5.11 till Azure Database for PostgreSQL 9.6.7.

    > [!NOTE]
    > För PostgreSQL version 10 stöder för närvarande DMS endast migrering av version 10,3 till Azure Database for PostgreSQL. Vi planerar att stödja nyare versioner av PostgreSQL snart.

- Om du vill aktivera logisk replikering i filen **Source postgresql postgresql. conf** anger du följande parametrar:
  - **wal_level** = logisk
  - **max_replication_slots** = [Max antal databaser för migrering]; Om du vill migrera fyra databaser ställer du in värdet på 4
  - **max_wal_senders** = [antal databaser som körs samtidigt]; det rekommenderade värdet är 10
- Lägg till DMS-agentens IP-adress till källan PostgreSQL pg_hba. conf
  1. Anteckna DMS-IP-adressen när du har slutfört etableringen av en instans av DMS.
  2. Lägg till IP-adressen i filen pg_hba. conf som visas:

        värd för alla 172.16.136.18/10 MD5 Host Replication postgres 172.16.136.18/10 MD5

- Användaren måste ha behörigheten Super på den server som är värd för käll databasen
- Förutom att ha ENUM i käll databasens schema måste käll-och mål databasens scheman överensstämma.
- Schemat i mål Azure Database for PostgreSQL-en server får inte ha sekundär nycklar. Använd följande fråga om du vill ta bort sekundär nycklar:

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

    Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.

- Schemat i mål Azure Database for PostgreSQL-en server får inte ha några utlösare. Använd följande för att inaktivera utlösare i mål databasen:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Begränsningar för data typer

- **Begränsning**: om det finns en uppräknings data typ i käll PostgreSQL-databasen, kommer migreringen att Miss under kontinuerlig synkronisering.

    **Lösning**: ändra Enum-datatype till Character varierande i Azure Database for PostgreSQL.

- **Begränsning**: om det inte finns någon primär nyckel för tabeller kommer den kontinuerliga synkroniseringen att Miss växlar.

    **Lösning**: temporärt ange en primär nyckel för tabellen för migrering för att fortsätta. Du kan ta bort den primära nyckeln när migreringen är klar.

- **Begränsning**: JSONB-datatype stöds inte för migrering.

## <a name="lob-limitations"></a>LOB-begränsningar

LOB-kolumner (Large Object) är kolumner som kan växa stora. För PostgreSQL är exempel på LOB-datatyper XML, JSON, IMAGE, TEXT osv.

- **Begränsning**: om LOB-datatyper används som primär nycklar kommer migreringen att Miss Miss förflyttningen.

    **Lösning**: Ersätt primär nyckel med andra data typer eller kolumner som inte är LOB.

- **Begränsning**: om kolumnen för stora objekt (LOB) är större än 32 KB kan data trunkeras vid målet. Du kan kontrol lera längden på LOB-kolumnen med den här frågan:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Lösning**: om du har LOB-objekt som är större än 32 KB kan du kontakta teknik teamet på [fråga Azure Database-migreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Begränsning**: om det finns LOB-kolumner i tabellen och det inte finns någon primär nyckel uppsättning för tabellen, kanske inte data migreras för den här tabellen.

    **Lösning**: temporärt ange en primär nyckel för tabellen för migrering för att gå vidare. Du kan ta bort den primära nyckeln när migreringen är klar.

## <a name="postgresql10-workaround"></a>PostgreSQL10-lösning

PostgreSQL 10. x gör olika ändringar i pg_xlog mappnamn och därför orsakar migreringen inte som förväntat. Om du migrerar från PostgreSQL 10. x till Azure Database for PostgreSQL 10,3 kör du följande skript i käll PostgreSQL-databasen för att skapa en wrapper-funktion kring pg_xlog functions.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

  > [!NOTE]
  > I föregående skript refererar "PG_User" till det användar namn som används för att ansluta till migrerings källan.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Begränsningar vid migrering online från AWS RDS PostgreSQL

När du försöker utföra en online-migrering från AWS RDS PostgreSQL till Azure Database for PostgreSQL kan du stöta på följande fel.

- **Fel**: standardvärdet för kolumnen {Column} i tabellen {Table} i databasen {Database} är inte samma på käll-och mål servrarna. Det har ”{value on source}” i källan och ”{value on target}” i målet.

  **Begränsning**: det här felet uppstår när standardvärdet i ett kolumn schema skiljer sig mellan käll-och mål databaserna.
  **Lösning**: kontrol lera att schemat på målet matchar schemat på källan. Information om hur du migrerar schemat finns i [dokumentationen för Azure postgresql online-migrering](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Fel**: mål databasen {Database} innehåller {Number of tables}-tabeller där som käll databasen {Database} har {Number of tables} tabeller. Antalet tabeller i käll- och måldatabaserna måste vara lika många.

  **Begränsning**: det här felet uppstår när antalet tabeller skiljer sig mellan käll-och mål databaserna.
  **Lösning**: kontrol lera att schemat på målet matchar schemat på källan. Information om hur du migrerar schemat finns i [dokumentationen för Azure postgresql online-migrering](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Fel:** Käll databasen {Database} är tom.

  **Begränsning**: det här felet uppstår när käll databasen är tom. Det beror sannolikt på att du har valt fel databas som källa.
  **Lösning**: kontrol lera den käll databas som du har valt för migrering och försök sedan igen.

- **Fel:** Mål databasen {Database} är tom. Migrera schemat.

  **Begränsning**: det här felet uppstår när det inte finns något schema i mål databasen. Kontrol lera att schemat på målet matchar schemat på källan.
  **Lösning**: kontrol lera att schemat på målet matchar schemat på källan. Information om hur du migrerar schemat finns i [dokumentationen för Azure postgresql online-migrering](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Andra begränsningar

- Databas namnet får inte innehålla semikolon (;).
- Lösen ords strängen som har inledande och avslutande klammer {} stöds inte. Den här begränsningen gäller både för att ansluta till käll PostgreSQL och mål Azure Database for PostgreSQL.
- En uppfångad tabell måste ha en primär nyckel. Om en tabell inte har en primär nyckel, blir resultatet av åtgärderna ta bort och uppdatera post oförutsägbart.
- Uppdatering av ett primär nyckel segment ignoreras. I sådana fall identifieras en sådan uppdatering av målet som en uppdatering som inte uppdaterade några rader och resulterar i att en post skrivs till tabellen undantag.
- Migrering av flera tabeller med samma namn, men ett annat fall (t. ex. TABLE1, TABLE1 och Table1) kan orsaka oförutsägbart beteende och stöds därför inte.
- Ändrings bearbetning av [CREATE | ÄNDRA | DROP] Table DDLs stöds om de inte lagras i ett inre Function/Procedure Body-block eller i andra kapslade konstruktioner. Till exempel kommer följande ändring inte att fångas in:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Ändrings bearbetning (kontinuerlig synkronisering) av TRUNKiska åtgärder stöds inte. Migrering av partitionerade tabeller stöds inte. När en partitionerad tabell identifieras inträffar följande saker:

  - Databasen kommer att rapportera en lista över över-och underordnade tabeller.
  - Tabellen kommer att skapas på målet som en vanlig tabell med samma egenskaper som de valda tabellerna.
  - Om den överordnade tabellen i käll databasen har samma primär nyckel värde som dess underordnade tabeller, kommer en "dubblettnyckel"-fel genereras.

- I DMS är gränsen för hur många databaser som ska migreras i en enda migrering fyra.
