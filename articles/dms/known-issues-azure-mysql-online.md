---
title: Artikel om kända problem/Överflyttnings begränsningar med online-migreringar till Azure Database for MySQL | Microsoft Docs
description: Läs om kända problem/begränsningar för migrering med online-migreringar till Azure Database for MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/06/2019
ms.openlocfilehash: fc5565ab9e3be21b96ce5aa5a938cf22ec3caeb0
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848487"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Kända problem/migrerings begränsningar med online-migreringar till Azure DB för MySQL

Kända problem och begränsningar som är kopplade till online-migreringar från MySQL till Azure Database for MySQL beskrivs i följande avsnitt.

## <a name="online-migration-configuration"></a>Konfiguration av online-migrering

- Käll Server versionen för MySQL måste vara version 5.6.35, 5.7.18 eller senare
- Azure Database for MySQL stöder:
  - MySQL Community Edition
  - InnoDB-motor
- Migrering av samma version. Migrering av MySQL 5,6 till Azure Database for MySQL 5,7 stöds inte.
- Aktivera binär loggning i My. ini (Windows) eller My. cnf (UNIX)
  - Ange Server_id till ett tal som är större eller lika med 1, till exempel Server_id = 1 (endast för MySQL 5,6)
  - Ange log-bin = \<sökväg > (endast för MySQL 5,6)
  - Ange binlog_format = rad
  - Expire_logs_days = 5 (rekommenderas endast för MySQL 5,6)
- Användaren måste ha rollen ReplicationAdmin.
- Sorteringar som har definierats för käll-MySQL-databasen är desamma som de som definierats i mål Azure Database for MySQL.
- Schemat måste matcha mellan käll-MySQL-databasen och mål databasen i Azure Database for MySQL.
- Schemat i mål Azure Database for MySQL får inte ha sekundär nycklar. Använd följande fråga om du vill ta bort sekundär nycklar:
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
- Schemat i mål Azure Database for MySQL får inte ha några utlösare. Så här släpper du utlösare i mål databasen:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Begränsningar för data typer

- **Begränsning**: Om det finns en JSON-datatype i käll-MySQL-databasen går det inte att migrera under kontinuerlig synkronisering.

    **Lösning**: Ändra JSON-datatype till medium text eller LONGTEXT i käll-MySQL-databasen.

- **Begränsning**: Om det inte finns någon primär nyckel för tabeller kommer den kontinuerliga synkroniseringen att Miss växlar.

    **Lösning**: Ange tillfälligt en primär nyckel för tabellen för migrering för att fortsätta. Du kan ta bort den primära nyckeln när migreringen är klar.

## <a name="lob-limitations"></a>LOB-begränsningar

LOB-kolumner (Large Object) är kolumner som kan växa stora i storlek. För MySQL, är medel text, LONGTEXT, BLOB, MEDIUMBLOB, LONGBLOB osv. några av data typerna för LOB.

- **Begränsning**: Om LOB-datatyper används som primär nycklar kommer migreringen att Miss förflyttningen.

    **Lösning**: Ersätt primär nyckel med andra data typer eller kolumner som inte är LOB.

- **Begränsning**: Om längden på den stora objekt kolumnen (LOB) är större än 32 KB kan data trunkeras vid målet. Du kan kontrol lera längden på LOB-kolumnen med den här frågan:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Lösning**: Om du har LOB-objekt som är större än 32 KB kan du kontakta teknik teamet på [fråga Azure Database](mailto:AskAzureDatabaseMigrations@service.microsoft.com)-migreringar. 

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Begränsningar vid migrering online från AWS RDS MySQL

När du försöker utföra en online-migrering från AWS RDS MySQL till att Azure Database for MySQL, kan du komma över följande fel.

- **Fel:** {0}Databasen har sekundär nyckel (er) på målet. Åtgärda målet och starta en ny migreringsaktivitet för data. Kör skriptet nedan på målet för att visa en lista över sekundär nyckel (er)

  **Begränsning**: Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen.
  **Lösning**: Kör följande skript i MySQL Workbench för att extrahera drop-sekundärnyckeln och lägga till ett sekundärnyckelskript:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Fel:** {0}Databasen finns inte på servern. Den angivna MySQL-källservern är skiftlägeskänslig. Kontrollera namnet på databasen.

  **Begränsning**: När du migrerar en MySQL-databas till Azure med hjälp av CLI, kan användarna råka ut för det här felet. Tjänsten kunde inte hitta databasen på käll servern, vilket kan bero på att du har angett ett felaktigt databas namn eller att databasen inte finns på den angivna servern. Obs! databas namn är Skift läges känsliga.

  **Lösning**: Ange det exakta databas namnet och försök igen.

- **Fel:** Det finns tabeller med samma namn i databasen {Database}. Azure Database för MySQL stöder inte skiftlägeskänsliga tabeller.

  **Begränsning**: Det här felet inträffar när du har två tabeller med samma namn i källdatabasen. Azure Database for MySQL stöder inte Skift läges känsliga tabeller.

  **Lösning**: Uppdatera tabell namnen så att de blir unika och försök sedan igen.

- **Fel:** Mål databasen {Database} är tom. Migrera schemat.

  **Begränsning**: Felet uppstår när mål Azure Database for MySQL databasen inte har det schema som krävs. Schema migrering krävs för att kunna migrera data till målet.

  **Lösning**: [Migrera schemat](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) från käll databasen till mål databasen.

## <a name="other-limitations"></a>Andra begränsningar

- En lösen ords sträng som har inledande och avslutande klammer {} i början och slutet av lösen ords strängen stöds inte. Den här begränsningen gäller både anslutning till källa MySQL och mål Azure Database for MySQL.
- Följande DDLs stöds inte:
  - Alla partition DDLs
  - Ta bort tabell
  - Byt namn på tabell
- Det går inte att lägga till kolumner i början eller i mitten av en tabell med hjälp av instruktionen *Alter table < table_name > Lägg till kolumn < column_name >* . *Alter table < table_name > Lägg till kolumn < column_name >* lägger till kolumnen i slutet av tabellen.
- Index som skapats endast för en del av kolumn data stöds inte. Följande instruktion är ett exempel som skapar ett index med endast en del av kolumn data:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- I DMS är gränsen för hur många databaser som ska migreras i en enda migrering fyra.
