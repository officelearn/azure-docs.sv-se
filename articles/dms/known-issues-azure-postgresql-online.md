---
title: Artikel om begränsningar för kända problem/migrering med online migrering till Azure Database for MySQL | Microsoft Docs
description: Läs mer om begränsningar för kända problem/migrering med online migrering till Azure Database för MySQL.
services: database-migration
author: HJToland3
ms.author: scphang
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/22/2018
ms.openlocfilehash: 363a4bd4be5e175aad346486b56c4f6efd877143
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340532"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Begränsningar för kända problem/migrering med online migreringar till Azure DB för PostgreSQL

I följande avsnitt beskrivs kända problem och begränsningar som är associerade med online migreringar från PostgreSQL till Azure Database för PostgreSQL. 

## <a name="online-migration-configuration"></a>Onlinemigrering konfiguration
- Källan PostgreSQL-servern måste köra version 9.5.11, 9.6.7 eller 10.3 eller senare. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](../postgresql/concepts-supported-versions.md).
- Endast samma version migreringar stöds. Till exempel stöds migrera PostgreSQL 9.5.11 till Azure Database för PostgreSQL 9.6.7 inte.
- Att aktivera logiska replikering i den **source PostgreSQL postgresql.conf** fil, ange följande parametrar:
    - **wal_level** = logical
    - **max_replication_slots** = [max antal databaser för migrering]; om du vill migrera 4 databaser, ange värdet till 4
    - **max_wal_senders** = [antalet databaser som körs samtidigt]; det rekommenderade värdet är 10
- Lägg till DMS agenten IP i källan PostgresSQL pg_hba.conf
    1. Anteckna DMS IP-adressen när du har slutfört etablering av en instans av DMS.
    2. Lägg till IP-adressen i filen pg_hba.conf som visas:

        vara värd för alla 172.16.136.18/10 md5 värd replikering postgres 172.16.136.18/10 md5

- Användaren måste ha superuser-behörighet på den server som är värd för källdatabasen
- Förutom att ha ENUM i källan databasschemat måste databasscheman källan och målet överensstämma.
- Schemat i målets Azure Database for PostgreSQL får inte ha sekundärnycklar. Använd följande fråga om du vill släppa främmande nycklar:

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

- Schemat i målets Azure Database for PostgreSQL får inte ha utlösare. Använd följande för att inaktivera utlösare i måldatabasen:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>DataType-begränsningar

- **Begränsningen**: Om det finns en ENUM-datatyp i källdatabasen för PostgreSQL, misslyckas migreringen under kontinuerlig synkronisering.

    **Lösning**: Ändra ENUM datatype till tecknet varierande i Azure Database för PostgreSQL.

- **Begränsningen**: Om det finns ingen primärnyckel i tabeller, misslyckas kontinuerlig synkronisering.

    **Lösning**: Ange tillfälligt primärnyckel för tabellen för migrering att gå vidare. Du kan ta bort den primära nyckeln när migreringen är klar.

## <a name="lob-limitations"></a>LOB-begränsningar
Stora objekt (LOB)-kolumner är kolumner som kan växa. För PostgreSQL är exempel på LOB-datatyper XML, JSON, bild, TEXT osv.

- **Begränsningen**: Om LOB-datatyper används som primärnyckel för, misslyckas migreringen.

    **Lösning**: Ersätt primärnyckel med andra datatyper eller kolumner som inte är LOB.

- **Begränsningen**: Om längden på kolumnen för stora objekt (LOB) är större än 32 KB, kan data trunkeras på angivna. Du kan kontrollera längden på LOB-kolumn med hjälp av den här frågan:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Lösning**: Om du har LOB-objekt som är större än 32 KB, kontakta teknikteamet på [be Azure Databasmigreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Begränsningen**: Om det finns LOB-kolumner i tabellen och det finns inga primära nyckeluppsättning för tabellen, kan det inte att migrera data för den här tabellen.

    **Lösning**: Ange tillfälligt primärnyckel för tabellen för migrering att gå vidare. Du kan ta bort den primära nyckeln när migreringen är klar.

## <a name="postgresql10-workaround"></a>PostgreSQL10 lösning
PostgreSQL 10.x ändrar olika pg_xlog mappnamn och kan därför orsaka migrering inte körs som förväntat. Om du migrerar från PostgreSQL 10.x till Azure Database för PostgreSQL 10.3, kör följande skript på PostgreSQL källdatabasen för att skapa funktionen omslutning runt pg_xlog funktioner.

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

## <a name="other-limitations"></a>Andra begränsningar
- Databasnamnet får inte innehålla ett semikolon (;).
- Lösenordssträngen med inledande och avslutande klammerparentes {} stöds inte. Den här begränsningen gäller för både ansluta till PostgreSQL för källa och mål Azure Database för PostgreSQL.
- En avbildade tabell måste ha en primärnyckel. Om en tabell inte har en primär nyckel, blir resultatet av att ta bort och uppdatera poster operations oförutsägbart.
- Uppdaterar en primärnyckel segment ignoreras. I sådana fall kan identifieras tillämpa sådan uppdatering av målet som en uppdatering som uppdaterades inte några rader och leder till en post som skrivs till tabellen undantag.
- Migreringen av flera tabeller med samma namn men olika fall (t.ex. tabell1, tabell1 och tabell1) kan orsaka oväntade funktionssätt och stöds därför inte.
- Ändra bearbetning av [skapa | ALTER | Släpp] tabell DDLs stöds om inte de lagras i ett inre function-procedur brödtext block eller i andra kapslade konstruktioner. Till exempel samlas följande ändring inte in:

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

- Ändra bearbetning (kontinuerlig synkronisering) TRUNKERING åtgärder stöds inte. Migrering av partitionerade tabeller stöds inte. När en partitionerad tabell har identifierats, inträffar följande:
    - Databasen rapporterar en lista över överordnade och underordnade tabellerna.
    - Tabellen kommer att skapas på målet som en vanlig tabell med samma egenskaper som de markerade tabellerna.
    - Om den överordnade tabellen i källdatabasen har samma primärnyckel-värdet som dess underordnade tabeller, genereras ett ”dubblettnyckel”-fel.
- DMS är gränsen på databaser som ska migreras i en enda migreringsaktivitet fyra.