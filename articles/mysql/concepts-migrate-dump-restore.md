---
title: Migrera MySQL-databasen med hjälp av dump och Återställ i Azure Database för MySQL
description: Den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i din Azure Database för MySQL med hjälp av verktyg som mysqldump, MySQL Workbench och PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: e79c83ecb17c4dcd11f7ccbecded59e7d1d13dfd
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542469"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrera din MySQL-databas till Azure Database för MySQL med säkerhetskopiering och återställning
Den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i din Azure Database for MySQL
- Säkerhetskopiering och återställning från kommandoraden (Använd mysqldump) 
- Dumpa och återställa med hjälp av PHPMyAdmin 

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här guiden, måste du ha:
- [Skapa Azure Database for MySQL-server – Azure-portalen](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) kommandoradsverktyg som installeras på en dator.
- MySQL Workbench [MySQL Workbench hämta](https://dev.mysql.com/downloads/workbench/), Toad, Navicat eller andra tredjeparts-MySQL-verktyget för att dumpa och återställa kommandon.

## <a name="use-common-tools"></a>Använd gemensamma verktyg
Använda vanliga funktioner och verktyg, till exempel MySQL Workbench, mysqldump, Toad eller Navicat för att fjärransluta och återställer data till Azure Database för MySQL. Använd dessa verktyg på din klientdator med en Internetanslutning för att ansluta till Azure Database för MySQL. Använda en SSL-krypterad anslutning för bästa säkerhetspraxis, se även [Konfigurera SSL-anslutning i Azure Database för MySQL](concepts-ssl-connection-security.md). Du behöver inte flytta filer med felsökningsdumpar vart särskilda molnet när du migrerar till Azure Database för MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Vanliga användningsområden för säkerhetskopiering och återställning
Du kan använda MySQL-verktyg, till exempel mysqldump och mysqlpump till dump och load-databaser till en Azure MySQL-databas i flera vanliga scenarier. I annat fall kan du använda den [importera och exportera](concepts-migrate-import-export.md) metod, i stället.

- Använd database minnesdumpar när du migrerar hela databasen. Den här rekommendationen innehåller när du flyttar en stor del av MySQL-data, eller när du vill minimera avbrott i tjänsten för live-webbplatser eller program. 
-  Kontrollera att alla tabeller i databasen använder InnoDB-lagringsmotorn vid inläsning av data till Azure Database för MySQL. Azure Database för MySQL stöder bara InnoDB lagringsmotorn och stöder därför inte alternativt lagringskonto motorer. Om dina tabeller har konfigurerats med andra storage-motorer, kan du konvertera dem till InnoDB engine-format innan du migrerar till Azure Database för MySQL.
   Till exempel om du har en WordPress- eller WebApp med MyISAM tabeller, först konvertera dessa tabeller genom att migrera till InnoDB format innan du återställer till Azure Database för MySQL. Använd satsen `ENGINE=InnoDB` för att ange den motor som används när du skapar en ny tabell, sedan överföra data till tabellen kompatibel efter återställningen. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Kontrollera att samma version av MySQL används på käll-och mål när dumpning databaser för att undvika eventuella kompatibilitetsproblem. Exempel: om din befintliga MySQL-server är version 5.7, sedan du bör migrera till Azure Database för MySQL som konfigurerats för att köra version 5.7. Den `mysql_upgrade` kommandot fungerar inte i en Azure Database for MySQL-server och stöds inte. Om du behöver uppgraderar från en MySQL-version först dump eller exportera lägre version databasen till en senare version av MySQL i din egen miljö. Kör sedan `mysql_upgrade`, innan du försöker migrera till en Azure Database för MySQL.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
För att optimera prestanda, märke till dessa överväganden när dumpning stora databaser:
-   Använd den `exclude-triggers` alternativ i mysqldump när dumpning databaser. Undanta utlösare från filer med felsökningsdumpar att undvika kommandona utlösaren aktiveringen under återställningen data. 
-   Använd den `single-transaction` alternativ för att ange isoleringsläget transaktion till REPEATABLE READ och skickar ett starta TRANSAKTION SQL-uttryck till servern innan dumpning data. Dumpning många tabeller i en enda transaktion gör vissa extra lagringsutrymme som ska förbrukas under återställning. Den `single-transaction` alternativet och `lock-tables` alternativet är ömsesidigt uteslutande eftersom Lås tabeller leder till att alla pågående transaktioner som anslås implicit. Om du vill dump stora tabeller, kombinera den `single-transaction` alternativet med den `quick` alternativet. 
-   Använd den `extended-insert` syntax i flera rader som innehåller flera värdelistor. Detta resulterar i en mindre dumpfil och snabbar upp infogningar när filen laddas.
-  Använd den `order-by-primary` alternativ i mysqldump när dumpning databaser, så att data är skriptade i primära nyckelordning.
-   Använd den `disable-keys` alternativet i mysqldump när dumpning data, om du vill inaktivera sekundärnyckelbegränsningar innan belastningen. Inaktivera främmande nycklar kontroller ger prestandavinster. Aktivera begränsningarna och verifiera data efter inläsningen så referensintegritet.
-   Använd partitionerade tabeller när det är lämpligt.
-   Läsa in data parallellt. Undvik att för mycket parallellitet som skulle du skriva en resursgräns, och övervaka resurser med hjälp av mått som är tillgängliga i Azure-portalen. 
-   Använd den `defer-table-indexes` alternativ i mysqlpump när dumpning databaser, så att skapa index händer efter tabeller data har lästs in.
-   Kopiera de säkerhetskopiera filerna till en Azure blobblagring och utföra återställningen därifrån, vilket borde vara mycket snabbare än att utföra återställningen via Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Skapa en säkerhetskopia från kommandoraden med hjälp av mysqldump
Om du vill säkerhetskopiera en befintlig MySQL-databas på den lokala servern eller på en virtuell dator, kör du följande kommando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Parametrarna för att tillhandahålla är:
- [uname] Din databasanvändarnamn 
- [pass] Lösenordet för din databas (Observera att det finns inget utrymme mellan -p och lösenordet) 
- [dbname] Namnet på din databas 
- [backupfile.sql] filnamnet för säkerhetskopian databas 
- [--opt] Alternativet mysqldump 

Till exempel om du vill säkerhetskopiera en databas med namnet ”testdb” på MySQL-servern med användarnamnet ”testuser” och inget lösenord till en fil testdb_backup.sql, använder du följande kommando. Kommandot som säkerhetskopierar de `testdb` databas till en fil med namnet `testdb_backup.sql`, som innehåller alla SQL-instruktioner som krävs för att skapa databasen på nytt. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Att välja specifika tabeller i databasen för att säkerhetskopiera, lista tabellnamn avgränsade med blanksteg. Om du vill säkerhetskopiera endast tabell1 och tabell2 tabeller från testdb följer du till exempel det här exemplet: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Om du vill säkerhetskopiera flera databaser på samma gång, Använd--databasen växla och lista databasnamn avgränsade med blanksteg. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Om du vill säkerhetskopiera alla databaser på servern i taget, bör du använda alternativet--alla databaser.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Skapa en databas på Azure-måldatabas för MySQL-server
Skapa en tom databas på Azure-måldatabas för MySQL-server där du vill migrera data. Använd ett verktyg som MySQL Workbench, Toad eller Navicat för att skapa databasen. Databasen kan ha samma namn som den databas som är innehöll dumpade data eller du kan skapa en databas med ett annat namn.

Om du vill ansluta, letar du upp informationen i den **översikt** för din Azure Database for MySQL.

![Hitta anslutningsinformationen i Azure portal](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Lägg till anslutningsinformation till MySQL Workbench.

![Anslutningssträngen för MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Återställ med hjälp av kommandoradsverktyget MySQL-databas eller MySQL Workbench
När du har skapat måldatabasen, kan du använda mysql-kommandot eller MySQL Workbench för att återställa data till den specifika databasen du skapade nyss från dumpfilen.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
I det här exemplet, återställer du data till den nyligen skapade databasen på Azure-måldatabas för MySQL-server.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportera med hjälp av PHPMyAdmin
Om du vill exportera, kan du använda den vanliga verktyg phpMyAdmin som du kanske redan har installerat lokalt i din miljö. Så här exporterar du din MySQL-databas med hjälp av PHPMyAdmin:
1. Öppna phpMyAdmin.
2. Välj din databas. Klicka på namnet på databasen i listan till vänster. 
3. Klicka på den **exportera** länk. En ny sida visas att visa dumpning av databasen.
4. I området för Export klickar du på den **Markera alla** länk för att välja tabellerna i databasen. 
5. Klicka på lämplig alternativen i området för SQL-alternativ. 
6. Klicka på den **Spara som fil** alternativet och motsvarande komprimering och klickar sedan på den **Gå** knappen. En dialogruta visas där du uppmanas att spara filen lokalt.

## <a name="import-using-phpmyadmin"></a>Importera med hjälp av PHPMyAdmin
Importera din databas liknar export. Gör följande:
1. Öppna phpMyAdmin. 
2. På sidan phpMyAdmin installationen klickar du på **Lägg till** att lägga till din Azure Database for MySQL-server. Ange anslutningsinformationen och inloggningsinformation.
3. Skapa en korrekt namngivna databas och välj den till vänster på skärmen. Om du vill skriva om den befintliga databasen, klickar du på namnet på databasen, markerar du kryssrutorna bredvid tabellnamn och välj **släppa** att ta bort de befintliga tabellerna. 
4. Klicka på den **SQL** länk för att visa sidan där du kan skriva in SQL-kommandon eller ladda upp din SQL-fil. 
5. Använd den **Bläddra** knappen för att hitta databasfilen. 
6. Klicka på den **Gå** knappen för att exportera säkerhetskopieringen, köra SQL-kommandon och återskapa din databas.

## <a name="next-steps"></a>Nästa steg
- [Anslut program till Azure Database för MySQL](./howto-connection-string.md).
- Mer information om hur du migrerar databaser till Azure Database för MySQL, finns i den [Guide för Databasmigrering](https://aka.ms/datamigration).
