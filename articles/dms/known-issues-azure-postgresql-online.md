---
title: 'Kända problem: Onlinemigreringar från PostgreSQL till Azure Database för PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Lär dig mer om kända problem och migreringsbegränsningar med onlinemigreringar från PostgreSQL till Azure Database för PostgreSQL med hjälp av Azure Database Migration Service.
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
ms.date: 02/20/2020
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235268"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Kända problem/migreringsbegränsningar med onlinemigreringar från PostgreSQL till Azure DB för PostgreSQL

Kända problem och begränsningar som är associerade med onlinemigreringar från PostgreSQL till Azure Database för PostgreSQL beskrivs i följande avsnitt.

## <a name="online-migration-configuration"></a>Konfiguration av onlinemigrering

- Källan PostgreSQL-servern måste köra version 9.4, 9.5, 9.6, 10 eller 11. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](../postgresql/concepts-supported-versions.md).
- Endast migreringar till samma eller en högre version stöds. Det stöds till exempel att migrera PostgreSQL 9.5 till Azure Database för PostgreSQL 9.6 eller 10, men det går inte att migrera från PostgreSQL 11 till PostgreSQL 9.6.
- Om du vill aktivera logisk replikering i **källfilen PostgreSQL postgresql.conf** anger du följande parametrar:
  - **wal_level** = logisk
  - **max_replication_slots** = [minst maximalt antal databaser för migrering]; Om du vill migrera fyra databaser anger du värdet till minst 4.
  - **max_wal_senders** = [antal databaser som körs samtidigt]; det rekommenderade värdet är 10
- Lägg till DMS-agent-IP i källan PostgreSQL pg_hba.conf
  1. Anteckna DMS IP-adress när du har etablerat en instans av Azure Database Migration Service.
  2. Lägg till IP-adressen i filen pg_hba.conf enligt bilden:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- Användaren måste ha rollen REPLIKERING på servern som är värd för källdatabasen.
- Käll- och måldatabasscheman måste matchas.
- Schemat i målet Azure Database för PostgreSQL-Single-server får inte ha externa nycklar. Använd följande fråga för att släppa externa nycklar:

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

- Schemat i målet Azure Database för PostgreSQL-Single-server får inte ha några utlösare. Använd följande för att inaktivera utlösare i måldatabasen:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Begränsningar för datatyp

  **Begränsning**: Om det inte finns någon primärnyckel på tabeller kan det hända att ändringar inte synkroniseras med måldatabasen.

  **Lösning**: Ange tillfälligt en primärnyckel för tabellen för att migrering ska fortsätta. Du kan ta bort primärnyckeln när datamigrering har slutförts.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Begränsningar vid migrering online från AWS RDS PostgreSQL

NÃ¤r du fÃ¶rsÃ¶ks en onlinemigrering frÃ¶rs frÃ¶r AWS RDS PostgreSQL till Azure Database for PostgreSQL kan fÃ¶lja fÃ¶ringar av fÃ¶rsÃ¶kningar uppstÃ¶r.

- **Fel:** Standardvärdet för kolumnen {column} i tabellen {table} i databasen {database} skiljer sig åt på käll- och målservrar. Det har ”{value on source}” i källan och ”{value on target}” i målet.

  **Begränsning**: Det här felet uppstår när standardvärdet i ett kolumnschema skiljer sig mellan käll- och måldatabaserna.
  **Lösning**: Se till att schemat på målet matchar schemat på källan. Mer information om migreringsschema finns i [azure postgresql-dokumentationen för onlinemigrering](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Fel:** Måldatabasen {database} har {antal tabeller} tabeller där som källdatabasen {database} har register {antal tabeller}. Antalet tabeller i käll- och måldatabaserna måste vara lika många.

  **Begränsning**: Det här felet uppstår när antalet tabeller skiljer sig mellan käll- och måldatabaserna.

  **Lösning**: Se till att schemat på målet matchar schemat på källan. Mer information om migreringsschema finns i [azure postgresql-dokumentationen för onlinemigrering](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

- **Fel:** Källdatabasen {database} är tom.

  **Begränsning**: Det här felet uppstår när källdatabasen är tom. Det beror troligen på att du har valt fel databas som källa.

  **Lösning**: Dubbelkolla källdatabasen som du har valt för migrering och försök sedan igen.

- **Fel:** Måldatabasen {database} är tom. Migrera schemat.

  **Begränsning**: Det här felet uppstår när det inte finns något schema i måldatabasen. Kontrollera att schemat för målet matchar schemat på källan.
  **Lösning**: Se till att schemat på målet matchar schemat på källan. Mer information om migreringsschema finns i [azure postgresql-dokumentationen för onlinemigrering](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema).

## <a name="other-limitations"></a>Andra begränsningar

- Databasnamnet får inte innehålla ett semikolon (;).
- En infångad tabell måste ha en primärnyckel. Om en tabell inte har en primärnyckel blir resultatet av poståtgärder för DELETE och UPDATE oförutsägbara.
- Uppdatering av ett primärnyckelsegment ignoreras. I sådana fall identifieras en sådan uppdatering av målet som en uppdatering som inte har uppdaterat några rader och resulterar i en post som skrivits till undantagstabellen.
- Migrering av flera tabeller med samma namn men ett annat ärende (t.ex. tabell1, TABELL1 och Tabell1) kan orsaka oförutsägbart beteende och stöds därför inte.
- Ändringsbearbetning av [CREATE | ALTER | DROP | Kort trunkera-tabell-DDLs stöds inte.
- I Azure Database Migration Service kan en enda migreringsaktivitet endast rymma upp till fyra databaser.
