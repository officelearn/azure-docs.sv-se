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
ms.date: 03/12/2019
ms.openlocfilehash: 75549302ca5d942a8a56cd9f72bc34297078a1e1
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790823"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Begränsningar för kända problem/migrering med online migreringar till Azure DB för MySQL

I följande avsnitt beskrivs kända problem och begränsningar som är associerade med online migrering från MySQL till Azure Database för MySQL. 

## <a name="online-migration-configuration"></a>Onlinemigrering konfiguration
- MySQL-källserverversionen måste vara version 5.6.35, 5.7.18 eller senare
- Azure Database för MySQL stöder:
    - MySQL community edition
    - InnoDB-motorn
- Samma Versionsmigrering. Migrera MySQL 5.6 till Azure Database för MySQL 5.7 stöds inte.
- Aktivera binär loggning i my.ini (Windows) eller my.cnf (Unix)
    - Som Server_id till ett tal som är större eller lika med 1, till exempel Server_id = 1 (endast för MySQL 5.6)
    - Ange log-bin = <path> (endast för MySQL 5.6)
    - Ange binlog_format = rad
    - Expire_logs_days = 5 (rekommenderas - endast för MySQL 5.6)
- Användare måste ha ReplicationAdmin-rollen.
- Sorteringar som definierats för källdatabasen för MySQL är desamma som de som definierats i Azure-måldatabas för MySQL.
- Schemat måste matcha mellan källan MySQL-databas och måldatabasen i Azure Database för MySQL.
- Schemat i Azure-måldatabas för MySQL får inte ha sekundärnycklar. Använd följande fråga om du vill släppa främmande nycklar:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.
- Schemat i Azure-måldatabas för MySQL får inte ha utlösare. Att släppa utlösare i måldatabasen:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>DataType-begränsningar
- **Begränsningen**: Om det finns en JSON-datatyp i källan MySQL-databas, misslyckas migreringen under kontinuerlig synkronisering.

    **Lösning**: Ändra JSON-datatyp till medelhög text eller longtext i källan MySQL-databas.

- **Begränsningen**: Om det finns ingen primärnyckel i tabeller, misslyckas kontinuerlig synkronisering.
 
    **Lösning**: Ange tillfälligt primärnyckel för tabellen för migrering att fortsätta. Du kan ta bort den primära nyckeln när migreringen är klar.

## <a name="lob-limitations"></a>LOB-begränsningar
Stora objekt (LOB)-kolumner är kolumner som kan växa i storlek. För MySQL, medelhög text Longtext, Blob, Mediumblob, Longblob osv är några av datatyper i LOB.

- **Begränsningen**: Om LOB-datatyper används som primärnyckel för, misslyckas migreringen.

    **Lösning**: Ersätt primärnyckel med andra datatyper eller kolumner som inte är LOB.

- **Begränsningen**: Om längden på kolumnen för stora objekt (LOB) är större än 32 KB, kan data trunkeras på angivna. Du kan kontrollera längden på LOB-kolumn med hjälp av den här frågan:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Lösning**: Om du har LOB-objekt som är större än 32 KB, kontakta teknikteamet på [be Azure Databasmigreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com). 

## <a name="other-limitations"></a>Andra begränsningar
- En sträng med lösenord som har inledande och avslutande klammerparentes {} i början och slutet av lösenordssträngen stöds inte. Den här begränsningen gäller för båda ansluter till källan MySQL och Azure-måldatabas för MySQL.
- Följande DDLs stöds inte:
    - Alla partition DDLs
    - Ta bort tabell
    - Byt namn på tabell
- Med hjälp av den *alter table < table_name > Lägg till kolumn < kolumnnamn >* -uttrycket för att lägga till kolumner i början eller mitten av en tabell stöds inte. Den *alter table < table_name > Lägg till kolumn < kolumnnamn >* lägger du till kolumnen i slutet av tabellen.
- Index som skapats på endast en del av kolumndata stöds inte. Följande instruktion är ett exempel som skapar ett index med endast en del av kolumndata:
    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- DMS är gränsen på databaser som ska migreras i en enda migreringsaktivitet fyra.
