---
title: Migrera din MySQL-databas med hjälp av dump och återställa i Azure-databas för MySQL
description: Den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i din Azure-databas för MySQL, med hjälp av verktyg som mysqldump, MySQL arbetsstationen och PHPMyAdmin.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/02/2018
ms.openlocfilehash: c801426ad354a165ac749333ddd4671c13536edb
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265851"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrera MySQL-databas till Azure-databas för MySQL med dump och återställning
Den här artikeln förklarar två vanliga sätt att säkerhetskopiera och återställa databaser i din Azure-databas för MySQL
- Dump och återställning från kommandoraden (med mysqldump) 
- Dump och Återställ med hjälp av PHPMyAdmin 

## <a name="before-you-begin"></a>Innan du börjar
Du måste ha för att gå igenom den här instruktioner:
- [Skapa Azure-databas för MySQL - server i Azure-portalen](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) kommandoradsverktyg som installeras på en dator.
- MySQL-arbetsstationen [MySQL arbetsstationen hämta](https://dev.mysql.com/downloads/workbench/), Toad, Navicat eller andra verktyg för tredje parts MySQL dump och återställa kommandon.

## <a name="use-common-tools"></a>Använd gemensamma verktyg
Använd gemensamma verktyg och verktyg som MySQL arbetsstationen, mysqldump, Toad eller Navicat att fjärransluta och återställa data till Azure-databasen för MySQL. Använd dessa verktyg på klientdatorn med en Internetanslutning för att ansluta till Azure-databasen för MySQL. Använd en SSL-krypterad anslutning Metodtips för säkerhet, se även [Konfigurera SSL-anslutning i Azure-databas för MySQL](concepts-ssl-connection-security.md). Du behöver inte flytta dumpfiler vart särskilda moln när du migrerar till Azure-databas för MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Vanliga användningsområden för dump och återställning
Du kan använda MySQL verktyg, till exempel mysqldump och mysqlpump till dump och Läs in databaser i en MySQL-databas på Azure i flera vanliga scenarier. I annat fall kan du använda den [importera och exportera](concepts-migrate-import-export.md) närmar sig i stället.

- Använd database Dumpar när du migrerar hela databasen. Denna rekommendation innehåller när du flyttar en stor mängd MySQL data eller när du vill minimera driftstopp för live-webbplatser eller program. 
-  Kontrollera att alla tabeller i databasen använder lagringsmotorn InnoDB vid inläsning av data till Azure-databas för MySQL. Azure-databas för MySQL stöder endast InnoDB lagringsmotorn och därför stöd inte för alternativa lagring motorer. Om dina tabeller har konfigurerats med andra lagring motorer, kan du konvertera dem till formatet InnoDB motorn innan migreringen till Azure-databas för MySQL.
   Till exempel om du har en WordPress eller Webbappen med hjälp av MyISAM-tabeller, först konvertera dessa tabeller genom att migrera till InnoDB format innan du återställer till Azure-databas för MySQL. Använd satsen `ENGINE=InnoDB` för att ange den motor som används när du skapar en ny tabell, sedan överföra data till tabellen kompatibel innan återställningen. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Se till att samma version av MySQL används på käll- och system när dumpning databaser för att undvika eventuella problem med programkompatibilitet. Till exempel om den befintliga MySQL-servern är version 5.7 bör sedan du migrera till Azure-databas för MySQL som konfigurerats för att köra version 5.7. Den `mysql_upgrade` kommandot fungerar inte i en Azure-databas för MySQL-server och stöds inte. Om du behöver uppgradera mellan olika versioner av MySQL först dump eller exportera lägre version databasen till en senare version av MySQL i din egen miljö. Kör sedan `mysql_upgrade`, innan du försöker migrera till en Azure-databas för MySQL.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
För att optimera prestanda uppmärksamma dessa överväganden när dumpning stora databaser:
-   Använd den `exclude-triggers` alternativet i mysqldump när dumpning databaser. Exkludera utlösare från dumpfiler att undvika kommandona utlösare startar under återställning av data. 
-   Använd den `single-transaction` alternativet för att ange transaktionen isoleringsläge REPETERBARA läsa och skickar en starta TRANSAKTION SQL-instruktion till servern innan dumpning data. Dumpning många tabeller i en enda transaktion gör vissa extra lagring som ska förbrukas under återställning. Den `single-transaction` alternativet och `lock-tables` alternativet är ömsesidigt uteslutande eftersom Lås tabeller leder till att alla väntande transaktioner ska genomföras implicit. Om du vill dump stora tabeller, kombinera den `single-transaction` alternativet med den `quick` alternativet. 
-   Använd den `extended-insert` syntax för flera rader som innehåller flera värdelistor. Detta resulterar i en mindre dumpfil och snabbare infogningar när filen läses.
-  Använd den `order-by-primary` alternativet i mysqldump när dumpning databaser, så att data är skripta i primär nyckel ordning.
-   Använd den `disable-keys` alternativ i mysqldump när dumpning data, för att inaktivera sekundärnyckelbegränsningar innan belastningen. Inaktivera främmande nycklar kontroller ger prestandavinster. Aktivera begränsningar och verifiera data efter belastningen så referensintegritet.
-   Använd partitionerade tabeller när det är lämpligt.
-   Läsa in data parallellt. Undvik att för mycket parallellitet som skulle innebära att du har nått gränsen för en resurs och övervaka resurser med hjälp av mätvärden som är tillgängliga i Azure-portalen. 
-   Använd den `defer-table-indexes` alternativet i mysqlpump när dumpning databaser, så att skapandet av index uppstår efter tabeller data har lästs in.
-   Kopiera de säkerhetskopiera filerna till en Azure blobstore och utföra återställningen därifrån som ska vara mycket snabbare än att utföra återställningen via Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Skapa en säkerhetskopia från kommandoraden med hjälp av mysqldump
Om du vill säkerhetskopiera en befintlig MySQL-databas på den lokala lokal servern eller i en virtuell dator, kör du följande kommando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametrarna för att ange är:
- [uname] Användarnamn för databasen 
- [pass] Lösenordet för databasen (Observera att det finns inget utrymme mellan -p och -lösenordet) 
- [dbname] Namnet på din databas 
- [backupfile.sql] filnamn för säkerhetskopiering av databas 
- [--välja] Alternativet mysqldump 

Till exempel om du vill säkerhetskopiera en databas med namnet 'testdb' på MySQL-servern med användarnamnet ”testuser' och utan lösenord till en fil testdb_backup.sql, använder du följande kommando. Kommandot säkerhetskopierar den `testdb` databas till en fil med namnet `testdb_backup.sql`, som innehåller alla SQL-instruktioner som krävs för att skapa databasen igen. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Att markera specifika tabeller i databasen för att säkerhetskopiera, lista tabellnamn avgränsade med blanksteg. Till exempel om du vill säkerhetskopiera endast tabell1 och tabell2 tabeller från testdb följa det här exemplet: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Om du vill säkerhetskopiera mer än en databas samtidigt använda--databasen växla och visa en lista över databasnamn avgränsade med blanksteg. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Om du vill säkerhetskopiera alla databaser på servern på samma gång, bör du använda alternativet--alla databaser.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Skapa en databas på mål Azure-databas för MySQL-server
Skapa en tom databas på Azure-databas för målet för MySQL-server där du vill migrera data. Använda ett verktyg som MySQL arbetsstationen, Toad eller Navicat för att skapa databasen. Databasen kan ha samma namn som den databas som innehåller dumpade data eller du kan skapa en databas med ett annat namn.

Om du vill hämta ansluten, hitta anslutningsinformationen i den **översikt** för din Azure-databas för MySQL.

![Hitta anslutningsinformationen i Azure-portalen](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Lägg till informationen i MySQL-arbetsstationen.

![MySQL-arbetsstationen anslutningssträngen](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Återställ MySQL-databas med hjälp av kommandoradsverktyget eller MySQL arbetsstationen
När du har skapat måldatabasen, kan du använda mysql-kommandot eller MySQL-arbetsstationen för att återställa data till den specifika nyligen skapade databasen från dumpfilen.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Återställa data i databasen som har skapats på aktiva Azure-databas för MySQL-servern i det här exemplet.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportera med PHPMyAdmin
Om du vill exportera, kan du använda den vanliga verktyget phpMyAdmin som du redan har installerat lokalt i din miljö. Så här exporterar MySQL-databasen med hjälp av PHPMyAdmin:
1. Öppna phpMyAdmin.
2. Välj din databas. Klicka på namnet på databasen i listan till vänster. 
3. Klicka på den **exportera** länk. En ny sida visas att visa dumpning av databasen.
4. Exportera, klicka på den **Markera alla** länk till Välj tabellerna i databasen. 
5. Klicka på alternativ i området för SQL-alternativ. 
6. Klicka på den **Spara som filen** alternativet och motsvarande komprimering och klickar sedan på den **Gå** knappen. En dialogruta visas där du uppmanas att spara filen lokalt.

## <a name="import-using-phpmyadmin"></a>Importera med hjälp av PHPMyAdmin
Importera databasen liknar export. Gör följande:
1. Öppna phpMyAdmin. 
2. Klicka på installationssidan phpMyAdmin **Lägg till** att lägga till din Azure-databas för MySQL-servern. Ange anslutningsinformationen och inloggningsinformation.
3. Skapa en korrekt namngiven databas och markera den till vänster på skärmen. Om du vill skriva om den befintliga databasen, klickar du på namnet på databasen, markerar du kryssrutorna bredvid tabellnamnen och väljer **släppa** att ta bort de befintliga tabellerna. 
4. Klicka på den **SQL** länken för att visa sidan där du kan skriva i SQL-kommandon eller ladda upp din SQL-fil. 
5. Använd den **Bläddra** för att hitta databasfilen. 
6. Klicka på den **Gå** för att exportera säkerhetskopieringen, köra SQL-kommandon och återskapa din databas.

## <a name="next-steps"></a>Nästa steg
- [Ansluta program till Azure-databas för MySQL](./howto-connection-string.md).
- Mer information om hur du migrerar databaser till Azure-databas för MySQL, finns det [databasen Migreringsguide](http://aka.ms/datamigration).
