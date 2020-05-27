---
title: Migrera med dump och Restore-Azure Database for MySQL
description: I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database for MySQL med hjälp av verktyg som mysqldump, MySQL Workbench och PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 158dd5e1f69340e233a0c2392d3f19fd5cf562ea
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845554"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrera MySQL-databasen till Azure Database för MySQL med säkerhetskopiering och återställning
I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database for MySQL
- Dumpa och Återställ från kommando raden (med mysqldump) 
- Dumpa och Återställ med PHPMyAdmin 

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här instruktions guiden måste du ha:
- [Skapa en Azure Database for MySQL-server – Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- kommando rads verktyget [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) installerat på en dator.
- MySQL Workbench [MySQL Workbench Hämta](https://dev.mysql.com/downloads/workbench/) eller ett annat mysql-verktyg från tredje part för att utföra dump-och Restore-kommandon.

Om du vill migrera stora databaser med databas storlekar på över 1 TBs kanske du vill överväga att använda community-verktyg som t. ex. en dumpare/för-belastningsutjämnare som stöder parallell export och import. Parallell dumpning och återställning kan bidra till att avsevärt minska tiden för migreringen för stora databaser. Du kan läsa vår [techcommunity-blogg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699) om du vill ha bästa praxis för att migrera stora databaser till Azure Database for MySQL-tjänsten med hjälp av Microsofts verktyg för dumpare/inläsnings verktyg.

## <a name="use-common-tools"></a>Använd vanliga verktyg
Använd vanliga verktyg och verktyg som MySQL Workbench eller mysqldump för att fjärrans luta och återställa data till Azure Database for MySQL. Använd sådana verktyg på klient datorn med en Internet anslutning för att ansluta till den Azure Database for MySQL. Använd en SSL-krypterad anslutning för bästa säkerhets praxis, se även [Konfigurera SSL-anslutning i Azure Database for MySQL](concepts-ssl-connection-security.md). Du behöver inte flytta dumpfiler till någon speciell moln plats när du migrerar till Azure Database for MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Vanliga användnings områden för dump och återställning
Du kan använda MySQL-verktyg som mysqldump och mysqlpump för att dumpa och läsa in databaser i en Azure MySQL-databas i flera vanliga scenarier. I andra scenarier kan du använda metoden [import och export](concepts-migrate-import-export.md) i stället.

- Använd databas dum par när du migrerar hela databasen. Den här rekommendationen innehåller när du flyttar en stor mängd MySQL-data, eller när du vill minimera tjänst avbrott för Live-webbplatser eller program. 
-  Se till att alla tabeller i databasen använder InnoDB-lagringsmotorn vid inläsning av data i Azure Database for MySQL. Azure Database for MySQL stöder endast InnoDB lagrings motor och stöder därför inte alternativa lagrings motorer. Om dina tabeller har kon figurer ATS med andra lagrings motorer konverterar du dem till InnoDB-motorns format innan du migrerar till Azure Database for MySQL.
   Om du till exempel har en WordPress eller WebApp som använder mina ISAM-tabeller, måste du först konvertera tabellerna genom att migrera till InnoDB-format innan du återställer till Azure Database for MySQL. Använd satsen `ENGINE=InnoDB` för att ställa in motorn som används när du skapar en ny tabell och överför sedan data till den kompatibla tabellen innan du återställer. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- För att undvika eventuella kompatibilitetsproblem bör du se till att samma version av MySQL används i käll- och målsystemen när databaser dumpas. Om din befintliga MySQL-server till exempel är version 5,7 bör du migrera till Azure Database for MySQL konfigurerad för att köra version 5,7. `mysql_upgrade`Kommandot fungerar inte i en Azure Database for MySQL-server och stöds inte. Om du behöver uppgradera mellan MySQL-versioner måste du först dumpa eller exportera den lägre versions databasen till en högre version av MySQL i din egen miljö. Kör sedan `mysql_upgrade` innan du försöker migrera till en Azure Database for MySQL.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
För att optimera prestanda bör du tänka på följande när det gäller dumpning av stora databaser:
-   Använd `exclude-triggers` alternativet i mysqldump när det är dumpnings databaser. Exkludera utlösare från dumpfiler för att undvika att Utlös ande kommandon utlöses under data återställningen. 
-   Använd `single-transaction` alternativet för att ställa in transaktions isolerings läget till REPETERBAR läsning och skicka ett SQL-uttryck för starttransaktion till servern innan du påbörjar dumpnings data. Dumpning av många tabeller i en enda transaktion medför att en extra lagring används under återställningen. `single-transaction`Alternativet och `lock-tables` alternativet är ömsesidigt uteslutande eftersom lås tabeller gör att eventuella väntande transaktioner kan bekräftas implicit. Om du vill dumpa stora tabeller kombinerar du `single-transaction` alternativet med `quick` alternativet. 
-   Använd `extended-insert` syntax med flera rader som innehåller flera värde listor. Detta resulterar i en mindre dumpfil och påskyndar infogningar när filen läses in igen.
-  Använd `order-by-primary` alternativet i mysqldump när det gäller dumpnings databaser, så att data skriptas i primär nyckel ordning.
-   Använd `disable-keys` alternativet i mysqldump när dumpnings data ska inaktivera begränsningar för sekundär nyckel före belastningen. Att inaktivera kontroller av sekundär nyckel ger prestanda vinster. Aktivera begränsningarna och kontrol lera data efter belastningen för att säkerställa referens integriteten.
-   Använd partitionerade tabeller när det är lämpligt.
-   Läs in data parallellt. Undvik för mycket parallellitet som skulle innebära att du träffar en resurs gräns och övervaka resurser med hjälp av de mått som är tillgängliga i Azure Portal. 
-   Använd `defer-table-indexes` alternativet i mysqlpump när det gäller dumpnings databaser, så att skapandet av index sker efter att tabell data har lästs in.
-   Använd `skip-definer` alternativet i mysqlpump för att utelämna definar-och SQL-säkerhetssatser från Create-instruktionerna för vyer och lagrade procedurer.  När du läser in dumpfilen igen skapas objekt som använder standardvärdena för avrundning och SQL-säkerhet.
-   Kopiera säkerhetskopieringsfilerna till en Azure Blob/Store och utför återställningen därifrån, vilket bör vara mycket snabbare än att utföra återställningen via Internet.

## <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Skapa en säkerhets kopierings fil från kommando raden med mysqldump
Om du vill säkerhetskopiera en befintlig MySQL-databas på den lokala lokala servern eller på en virtuell dator kör du följande kommando: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De parametrar som ska tillhandahållas är:
- [uname] Ditt användar namn för databasen 
- pass Lösen ordet för din databas (Observera att det inte finns något blank steg mellan-p och lösen ordet) 
- DB Namnet på din databas 
- [BackupFile. SQL] fil namnet för säkerhets kopian av databasen 
- [--opt] Alternativet mysqldump 

Om du till exempel vill säkerhetskopiera en databas med namnet "testdb" på MySQL-servern med användar namnet "testuser" och utan lösen ord till en fil testdb_backup. SQL, använder du följande kommando. Kommandot säkerhetskopierar `testdb` databasen till en fil `testdb_backup.sql` med namnet, som innehåller alla SQL-instruktioner som krävs för att återskapa databasen. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Om du vill välja vissa tabeller i databasen som ska säkerhets kopie ras, listar du tabell namnen avgränsade med blank steg. Om du till exempel vill säkerhetskopiera endast TABLE1-och tabell2-tabeller från "testdb", följer du det här exemplet: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Om du vill säkerhetskopiera fler än en databas samtidigt använder du--databas växeln och listar databas namnen avgränsade med blank steg. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Skapa en databas på mål Azure Database for MySQLs servern
Skapa en tom databas på mål Azure Database for MySQL servern där du vill migrera data. Använd ett verktyg som MySQL Workbench för att skapa databasen. Databasen kan ha samma namn som databasen som innehåller de dumpade data eller så kan du skapa en databas med ett annat namn.

För att ansluta, letar du upp anslutnings informationen i **översikten** över din Azure Database for MySQL.

![Hitta anslutnings informationen i Azure Portal](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Lägg till anslutnings informationen i MySQL Workbench.

![Anslutnings sträng för MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>Förbereda mål Azure Database for MySQL servern för snabb data inläsningar
Följande Server parametrar och konfiguration måste ändras för att förbereda mål Azure Database for MySQL servern för snabbare data inläsningar.
- max_allowed_packet – Ställ in på 1073741824 (dvs. 1 GB) för att förhindra eventuella problem med data spill på grund av långa rader.
- slow_query_log – inställt på att stänga av den långsamma frågans logg. Detta eliminerar överbelastningen som orsakas av långsam loggning av frågor vid data inläsning.
- query_store_capture_mode – Ställ in båda på ingen för att inaktivera Frågearkivet. Detta eliminerar överbelastningen som orsakas av provtagnings aktiviteter från Query Store.
- innodb_buffer_pool_size – skala upp servern till 32 vCore-minnesoptimerade SKU från pris nivån i portalen under migreringen för att öka innodb_buffer_pool_size. Innodb_buffer_pool_size kan bara ökas genom att skala upp beräkning för Azure Database for MySQL server.
- innodb_write_io_threads & innodb_write_io_threads-ändra till 16 från Server parametrarna i Azure Portal för att förbättra migreringens hastighet.
- Skala upp lagrings nivå – IOPs för Azure Database for MySQL server ökar progressivt med ökningen på lagrings nivå. För snabbare belastningar kanske du vill öka lagrings nivån för att öka den IOPs som är etablerad. Kom ihåg att lagringen bara kan skalas upp, inte.

När migreringen är klar kan du återgå till att återställa Server parametrarna och konfigurationen för beräknings nivån till dess tidigare värden. 

## <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Återställa MySQL-databasen med hjälp av kommando raden eller MySQL Workbench
När du har skapat mål databasen kan du använda MySQL-kommandot eller MySQL Workbench för att återställa data till den specifika nyligen skapade databasen från dumpfilen.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
I det här exemplet återställer du data till den nyligen skapade databasen på mål Azure Database for MySQLs servern.
```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
## <a name="export-using-phpmyadmin"></a>Exportera med PHPMyAdmin
Om du vill exportera kan du använda det vanliga verktyget phpMyAdmin, som du kanske redan har installerat lokalt i din miljö. Så här exporterar du MySQL-databasen med PHPMyAdmin:
1. Öppna phpMyAdmin.
2. Välj din databas. Klicka på databas namnet i listan till vänster. 
3. Klicka på länken **Exportera** . En ny sida visas för att Visa dumpen av databasen.
4. I export-ytan klickar du på länken **Välj alla** för att välja tabeller i databasen. 
5. I avsnittet SQL-alternativ klickar du på lämpliga alternativ. 
6. Klicka på alternativet **Spara som fil** och motsvarande komprimerings alternativ och klicka sedan på knappen **gå** . En dialog ruta visas där du ombeds att spara filen lokalt.

## <a name="import-using-phpmyadmin"></a>Importera med PHPMyAdmin
Att importera databasen liknar att exportera. Utför följande åtgärder:
1. Öppna phpMyAdmin. 
2. På sidan installation av phpMyAdmin klickar du på **Lägg** till för att lägga till din Azure Database for MySQL-server. Ange anslutnings informationen och inloggnings informationen.
3. Skapa en korrekt namngiven databas och välj den till vänster på skärmen. Om du vill skriva om den befintliga databasen klickar du på databas namnet, markerar alla kryss rutorna bredvid tabell namnen **och väljer Ta bort för** att ta bort befintliga tabeller. 
4. Klicka på **SQL** -länken för att visa sidan där du kan skriva SQL-kommandon eller ladda upp din SQL-fil. 
5. Använd knappen **Bläddra** för att hitta databas filen. 
6. Klicka på knappen **gå** för att exportera säkerhets kopian, kör SQL-kommandona och återskapa databasen.

## <a name="known-issues"></a>Kända problem
För kända problem, tips och trick rekommenderar vi att du tittar på vår [techcommunity-blogg](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912).

## <a name="next-steps"></a>Nästa steg
- [Anslut program till Azure Database for MySQL](./howto-connection-string.md).
- Mer information om hur du migrerar databaser till Azure Database for MySQL finns i [Guide för databas migrering](https://aka.ms/datamigration).
