---
title: 'Kända problem: Onlinemigreringar till Azure Database för MySQL'
titleSuffix: Azure Database Migration Service
description: Lär dig mer om kända problem och migreringsbegränsningar med onlinemigreringar till Azure Database för MySQL när du använder Migreringstjänsten för Azure Database.
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
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235285"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problem med onlinemigrering & begränsningar för Azure DB för MySQL med Azure Database Migration Service

Kända problem och begränsningar som är associerade med onlinemigreringar från MySQL till Azure Database för MySQL beskrivs i följande avsnitt.

## <a name="online-migration-configuration"></a>Konfiguration av onlinemigrering


- Källan MySQL Server-versionen måste vara version 5.6.35, 5.7.18 eller senare
- Azure Database för MySQL stöder:
  - MySQL community edition MySQL community edition MySQL community edition MyS
  - InnoDB-motor
- Samma version migrering. Det går inte att migrera MySQL 5.6 till Azure Database för MySQL 5.7.
- Aktivera binär loggning i my.ini (Windows) eller my.cnf (Unix)
  - Ange Server_id till valfritt tal större eller lika med 1, till exempel Server_id=1 (endast för MySQL 5.6)
  - Ange logg-lagerplats = \<sökväg> (endast för MySQL 5.6)
  - Ange binlog_format = rad
  - Expire_logs_days = 5 (rekommenderas - endast för MySQL 5.6)
- Användaren måste ha rollen ReplicationAdmin.
- Sorteringar som definierats för källan MySQL-databasen är desamma som de som definieras i mål Azure Database för MySQL.
- Schemat måste matcha mellan käll-MySQL-databas och måldatabas i Azure Database for MySQL.
- Schema i mål Azure Database för MySQL får inte ha externa nycklar. Använd följande fråga för att släppa externa nycklar:
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
- Schema i mål Azure Database för MySQL får inte ha några utlösare. Så här släpper du utlösare i måldatabasen:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Begränsningar för datatyp

- **Begränsning**: Om det finns en JSON-datatyp i källan MySQL-databasen misslyckas migreringen under kontinuerlig synkronisering.

    **Lösning**: Ändra JSON-datatyp till medeltext eller långtext i mysql-databasen.

- **Begränsning**: Om det inte finns någon primärnyckel på tabeller misslyckas kontinuerlig synkronisering.

    **Lösning**: Ange tillfälligt en primärnyckel för tabellen för att migrering ska fortsätta. Du kan ta bort primärnyckeln när datamigrering har slutförts.

## <a name="lob-limitations"></a>LOB-begränsningar

Stora objektkolumner (LOB) är kolumner som kan växa sig stora i storlek. För MySQL, Medium text, Longtext, Blob, Mediumblob, Longblob, etc., är några av de datatyper av LOB.

- **Begränsning**: Om LOB-datatyper används som primärnycklar misslyckas migreringen.

    **Lösning**: Ersätt primärnyckeln med andra datatyper eller kolumner som inte är LOB.

- **Begränsning**: Om längden på kolumnen Stora objekt (LOB) är större än 32 KB kan data trunkeras vid målet. Du kan kontrollera längden på LOB-kolumnen med den här frågan:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Lösning**: Om du har LOB-objekt som är större än 32 KB kontaktar du teknikteamet på [Fråga Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Begränsningar vid migrering online från AWS RDS MySQL

När du försöker utföra en onlinemigrering från AWS RDS MySQL till Azure Database for MySQL kan du stöta på följande fel.

- **Fel:** Databasen{0}' ' har sekundärnyckel på målet. Åtgärda målet och starta en ny migreringsaktivitet för data. Kör under skriptet på målet för att lista sekundärnyckeln(er)

  **Begränsning**: Om du har externa nycklar i schemat misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen.
  **Lösning:** Kör följande skript i MySQL-arbetsbänk för att extrahera skriptet släpp sekundär nyckel och lägga till skript för sekundär nyckel:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Fel:** Databasen{0}' ' finns inte på servern. Den angivna MySQL-källservern är skiftlägeskänslig. Kontrollera namnet på databasen.

  **Begränsning:** När du migrerar en MySQL-databas till Azure med hjälp av COMMAND Line Interface (CLI) kan användare komma till det här felet. Det gick inte att hitta databasen på källservern, vilket kan bero på att du kan ha angett ett felaktigt databasnamn eller att databasen inte finns på den angivna servern. Databasnamn är skiftlägeskänsliga.

  **Lösning**: Ange det exakta databasnamnet och försök sedan igen.

- **Fel:** Det finns tabeller med samma namn i databasen {database}. Azure Database för MySQL stöder inte skiftlägeskänsliga tabeller.

  **Begränsning**: Det här felet inträffar när du har två tabeller med samma namn i källdatabasen. Azure Database for MySQL stöder inte skiftlägeskänsliga tabeller.

  **Lösning**: Uppdatera tabellnamnen så att de är unika och försök sedan igen.

- **Fel:** Måldatabasen {database} är tom. Migrera schemat.

  **Begränsning**: Det här felet uppstår när mål Azure-databasen för MySQL-databasen inte har det schema som krävs. Schemamigrering krävs för att aktivera migrera data till ditt mål.

  **Lösning**: [Migrera schemat](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) från källdatabasen till måldatabasen.

## <a name="other-limitations"></a>Andra begränsningar

- En lösenordssträng som har öppna och stänga klaffiga parenteser { } i början och slutet av lösenordssträngen stöds inte. Den här begränsningen gäller både anslutning till källan MySQL och mål Azure Database för MySQL.
- Följande DDLs stöds inte:
  - Alla partitions-DDLs
  - Släpp tabell
  - Byt namn på tabell
- Det går inte att *använda alter-tabellen <table_name> lägga till kolumn <column_name*>-satsen för att lägga till kolumner i början eller i mitten av en tabell. Ändra *tabellen <table_name> lägga till kolumn <column_name>* lägger till kolumnen i slutet av tabellen.
- Index som skapas på endast en del av kolumndata stöds inte. Följande sats är ett exempel som skapar ett index med endast en del av kolumndata:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- I Azure Database Migration Service är gränsen för databaser att migrera i en enda migreringsaktivitet fyra.

- **Fel:** Radstorleken är för stor (> 8126). Det kan vara till hjälp att ändra vissa kolumner till TEXT eller BLOB. I aktuellt radformat lagras BLOB-prefixet på 0 byte infogad.

  **Begränsning:** Det här felet inträffar när du migrerar till Azure Database for MySQL med innoDB-lagringsmotorn och alla tabellradstorlekar är för stora (>8126 byte).

  **Lösning**: Uppdatera schemat för tabellen som har en radstorlek som är större än 8126 byte. Vi rekommenderar inte att du ändrar det strikta läget eftersom data trunkeras. Det går inte att ändra page_size.
