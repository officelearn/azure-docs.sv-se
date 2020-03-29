---
title: Migrera med dump och återställning - Azure Database för MariaDB
description: I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i din Azure-databas för MariaDB med hjälp av verktyg som mysqldump, MySQL Workbench och PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 72735e83af97fde8377e27daa45501704ef5a3c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164550"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migrera din MariaDB-databas till Azure Database för MariaDB med dump och återställning
I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i din Azure-databas för MariaDB
- Dumpa och återställa från kommandoraden (med mysqldump) 
- Dumpa och återställa med PHPMyAdmin

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här guiden måste du ha:
- [Skapa Azure Database för MariaDB-server - Azure-portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) kommandoradsverktyget installerat på en maskin.
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) eller annan tredje part MySQL verktyg för att dumpa och återställa kommandon.

## <a name="use-common-tools"></a>Använd vanliga verktyg
Använd vanliga verktyg och verktyg som MySQL Workbench eller mysqldump för att fjärr ansluta och återställa data till Azure Database för MariaDB. Använd sådana verktyg på klientdatorn med en internetanslutning för att ansluta till Azure-databasen för MariaDB. Använd en SSL-krypterad anslutning för bästa säkerhetspraxis, se även [Konfigurera SSL-anslutning i Azure Database för MariaDB](concepts-ssl-connection-security.md). Du behöver inte flytta dumpfilerna till någon särskild molnplats när du migrerar till Azure Database för MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Vanliga användningsområden för dump och återställning
Du kan använda MySQL-verktyg som mysqldump och mysqlpump för att dumpa och läsa in databaser i en Azure Database för MariaDB-server i flera vanliga scenarier. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Använd databasdumpar när du migrerar hela databasen. Den här rekommendationen gäller när du flyttar en stor mängd data eller när du vill minimera avbrott i tjänsten för live-webbplatser eller program. 
-  Se till att alla tabeller i databasen använder InnoDB-lagringsmotorn vid inläsning av data i Azure Database for MariaDB. Azure Database för MariaDB stöder endast InnoDB Storage-motor och stöder därför inte alternativa lagringsmotorer. Om dina tabeller är konfigurerade med andra lagringsmotorer konverterar du dem till InnoDB-motorformatet före migreringen till Azure Database för MariaDB.
   Om du till exempel har en WordPress eller WebApp med MyISAM-tabellerna konverterar du först dessa tabeller genom att migrera till InnoDB-format innan du återställer till Azure Database för MariaDB. Använd satsen `ENGINE=InnoDB` för att ställa in motorn som används när du skapar en ny tabell och överför sedan data till den kompatibla tabellen före återställningen. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- För att undvika eventuella kompatibilitetsproblem bör du se till att samma version av MariaDB används i käll- och målsystemen när databaser dumpas. Om din befintliga MariaDB-server till exempel är version 10.2 bör du migrera till Azure Database för MariaDB som konfigurerats för att köra version 10.2. Kommandot `mysql_upgrade` fungerar inte i en Azure-databas för MariaDB-server och stöds inte. Om du behöver uppgradera över MariaDB-versioner dumpar eller exporterar du först databasen med lägre versioner till en högre version av MariaDB i din egen miljö. Kör `mysql_upgrade`sedan , innan du försöker migrering till en Azure-databas för MariaDB.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
För att optimera prestanda, ta notis om dessa överväganden när dumpning stora databaser:
-   Använd `exclude-triggers` alternativet i mysqldump när du dumpar databaser. Uteslut utlösare från dumpfiler för att undvika att utlösarkommandona aktiveras under dataåterställningen. 
-   Använd `single-transaction` alternativet för att ställa in transaktionsisoleringsläget till REPETERBAR LÄSNING och skickar en SQL-sats för START TRANSACTION till servern innan du dumpar data. Om du dumpar många tabeller i en enskild transaktion förbrukas viss extra lagring under återställningen. Alternativet `single-transaction` och `lock-tables` alternativet utesluter varandra eftersom LÅS-TABELLER medför att alla väntande transaktioner genomförs implicit. Om du vill dumpa `single-transaction` stora `quick` tabeller kombinerar du alternativet med alternativet. 
-   Använd `extended-insert` syntaxen med flera rader som innehåller flera VÄRDE-listor. Detta resulterar i en mindre dumpfil och snabbar upp infogningar när filen laddas om.
-  Använd `order-by-primary` alternativet i mysqldump när du dumpar databaser, så att data skriptas i primärnyckelordning.
-   Använd `disable-keys` alternativet i mysqldump vid dumpning av data för att inaktivera begränsningar för sekundärnyckel före inläsning. Om du inaktiverar kontroller för sekundär nyckel ger prestandavinster. Aktivera begränsningarna och verifiera data efter inläsningen för att säkerställa referensintegritet.
-   Använd partitionerade tabeller när det är lämpligt.
-   Läs in data parallellt. Undvik för mycket parallellism som gör att du når en resursgräns och övervakar resurser med hjälp av de mått som finns i Azure-portalen. 
-   Använd `defer-table-indexes` alternativet i mysqlpump när du dumpar databaser, så att indexskapande sker när tabeller data läses in.
-   Kopiera säkerhetskopieringsfilerna till en Azure-blob/store och utför återställningen därifrån, vilket borde vara mycket snabbare än att utföra återställningen över Internet.

## <a name="create-a-backup-file"></a>Skapa en säkerhetskopia
Om du vill säkerhetskopiera en befintlig MariaDB-databas på den lokala lokala lokala servern eller på en virtuell dator kör du följande kommando med mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De parametrar som ska tillhandahållas är:
- [uname] Ditt databasanvändarnamn 
- - Jag vet inte vad du ska gå till. Lösenordet för databasen (observera att det inte finns något utrymme mellan -p och lösenordet) 
- [dbname] Namnet på databasen 
- [backupfile.sql] Filnamnet för säkerhetskopian av databasen 
- [--opt] Alternativet mysqldump 

Om du till exempel vill säkerhetskopiera en databas med namnet "testdb" på din MariaDB-server med användarnamnet "testanvändare" och utan lösenord till en fil testdb_backup.sql använder du följande kommando. Kommandot säkerhetskopierar `testdb` databasen till en `testdb_backup.sql`fil som heter , som innehåller alla SQL-uttryck som behövs för att återskapa databasen. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Om du vill markera specifika tabeller i databasen som ska säkerhetskopieras listar du tabellnamnen avgränsade med blanksteg. Om du till exempel bara vill säkerhetskopiera tabell1- och tabell2-tabeller från testdb:et: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Om du vill säkerhetskopiera mer än en databas samtidigt använder du växeln --database och listar databasnamnen avgränsade med blanksteg. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Skapa en databas på målservern
Skapa en tom databas på målet Azure Database för MariaDB-server där du vill migrera data. Använd ett verktyg som MySQL Workbench för att skapa databasen. Databasen kan ha samma namn som databasen som innehåller de dumpade data eller så kan du skapa en databas med ett annat namn.

Om du vill ansluta letar du reda på anslutningsinformationen i **översikten över** din Azure-databas för MariaDB.

![Hitta anslutningsinformationen i Azure-portalen](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Lägg till anslutningsinformationen i din MySQL Workbench.

![Anslutningssträng för MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Återställa din MariaDB-databas
När du har skapat måldatabasen kan du använda kommandot mysql eller MySQL Workbench för att återställa data till den specifika nyskapade databasen från dumpfilen.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
I det här exemplet återställer du data till den nyskapade databasen på mål-Azure-databasen för MariaDB-servern.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportera med PHPMyAdmin
För att exportera kan du använda det gemensamma verktyget phpMyAdmin, som du kanske redan har installerat lokalt i din miljö. Så här exporterar du din MariaDB-databas med PHPMyAdmin:
1. Öppna phpMyAdmin.
2. Välj databasen. Klicka på databasnamnet i listan till vänster. 
3. Klicka på länken **Exportera.** En ny sida visas för att visa dump av databasen.
4. Klicka på länken **Markera alla** i området Exportera för att välja tabellerna i databasen. 
5. Klicka på lämpliga alternativ i området SQL-alternativ. 
6. Klicka på alternativet **Spara som fil** och motsvarande komprimeringsalternativ och klicka sedan på knappen **Gå.** En dialogruta bör visas som uppmanar dig att spara filen lokalt.

## <a name="import-using-phpmyadmin"></a>Importera med PHPMyAdmin
Att importera databasen liknar export. Gör följande:
1. Öppna phpMyAdmin. 
2. På inställningssidan för phpMyAdmin klickar du på **Lägg** till för att lägga till din Azure-databas för MariaDB-server. Ange anslutningsuppgifter och inloggningsinformation.
3. Skapa en databas med lämpligt namn och markera den till vänster på skärmen. Om du vill skriva om den befintliga databasen klickar du på databasnamnet, markerar alla kryssrutor bredvid tabellnamnen och väljer **Släpp** för att ta bort de befintliga tabellerna. 
4. Klicka på **SQL-länken** om du vill visa sidan där du kan skriva in SQL-kommandon eller ladda upp SQL-filen. 
5. Använd **bläddringsknappen** för att hitta databasfilen. 
6. Klicka på **knappen Gå** om du vill exportera säkerhetskopian, köra SQL-kommandona och återskapa databasen.

## <a name="next-steps"></a>Nästa steg
- [Anslut program till Azure Database för MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
