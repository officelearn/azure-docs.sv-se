---
title: Migrera med dump och Restore-Azure Database for MySQL
description: I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database for MySQL med hjälp av verktyg som mysqldump, MySQL Workbench och PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65cd5e637434c717ab9ba1b5598c467eea9b4a74
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770942"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Migrera MySQL-databasen till Azure Database for MySQL med dumpa och Återställ
I den här artikeln beskrivs två vanliga sätt att säkerhetskopiera och återställa databaser i Azure Database for MySQL
- Dumpa och Återställ från kommando raden (med mysqldump) 
- Dumpa och Återställ med PHPMyAdmin 

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här instruktions guiden måste du ha:
- [Skapa Azure Database for MySQL server – Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- kommando rads verktyget [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) installerat på en dator.
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/), TOAD, Navicat eller andra MySQL-verktyg från tredje part för att utföra dump-och Restore-kommandon.

## <a name="use-common-tools"></a>Använd vanliga verktyg
Använd vanliga verktyg och verktyg som MySQL Workbench, mysqldump, TOAD eller Navicat för att fjärrans luta och återställa data till Azure Database for MySQL. Använd sådana verktyg på klient datorn med en Internet anslutning för att ansluta till den Azure Database for MySQL. Använd en SSL-krypterad anslutning för bästa säkerhets praxis, se även [Konfigurera SSL-anslutning i Azure Database for MySQL](concepts-ssl-connection-security.md). Du behöver inte flytta dumpfiler till någon speciell moln plats när du migrerar till Azure Database for MySQL. 

## <a name="common-uses-for-dump-and-restore"></a>Vanliga användnings områden för dump och återställning
Du kan använda MySQL-verktyg som mysqldump och mysqlpump för att dumpa och läsa in databaser i en Azure MySQL-databas i flera vanliga scenarier. I andra scenarier kan du använda metoden [import och export](concepts-migrate-import-export.md) i stället.

- Använd databas dum par när du migrerar hela databasen. Den här rekommendationen innehåller när du flyttar en stor mängd MySQL-data, eller när du vill minimera tjänst avbrott för Live-webbplatser eller program. 
-  Se till att alla tabeller i databasen använder InnoDB-lagringsmotorn vid inläsning av data i Azure Database for MySQL. Azure Database for MySQL stöder endast InnoDB lagrings motor och stöder därför inte alternativa lagrings motorer. Om dina tabeller har kon figurer ATS med andra lagrings motorer konverterar du dem till InnoDB-motorns format innan du migrerar till Azure Database for MySQL.
   Om du till exempel har en WordPress eller WebApp som använder mina ISAM-tabeller, måste du först konvertera tabellerna genom att migrera till InnoDB-format innan du återställer till Azure Database for MySQL. Använd satsen `ENGINE=InnoDB` för att ställa in motorn som används när du skapar en ny tabell och överför sedan data till den kompatibla tabellen innan du återställer. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- För att undvika eventuella kompatibilitetsproblem bör du se till att samma version av MySQL används i käll- och målsystemen när databaser dumpas. Om din befintliga MySQL-server till exempel är version 5,7 bör du migrera till Azure Database for MySQL konfigurerad för att köra version 5,7. Kommandot `mysql_upgrade` fungerar inte i en Azure Database for MySQL-server och stöds inte. Om du behöver uppgradera mellan MySQL-versioner måste du först dumpa eller exportera den lägre versions databasen till en högre version av MySQL i din egen miljö. Kör sedan `mysql_upgrade`innan du försöker migrera till en Azure Database for MySQL.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
För att optimera prestanda bör du tänka på följande när det gäller dumpning av stora databaser:
-   Använd alternativet `exclude-triggers` i mysqldump när det är dumpnings databaser. Exkludera utlösare från dumpfiler för att undvika att Utlös ande kommandon utlöses under data återställningen. 
-   Använd `single-transaction` alternativet för att ställa in transaktions isolerings läget till REPETERBARt läst och skickar ett SQL-uttryck för starttransaktion till servern innan du påbörjar dumpnings data. Dumpning av många tabeller i en enda transaktion medför att en extra lagring används under återställningen. Alternativet `single-transaction` och alternativet `lock-tables` är ömsesidigt uteslutande, eftersom lås tabeller gör att eventuella väntande transaktioner kan bekräftas implicit. Om du vill dumpa stora tabeller kombinerar du alternativet `single-transaction` med alternativet `quick`. 
-   Använd syntaxen `extended-insert` flera rader som innehåller flera värde listor. Detta resulterar i en mindre dumpfil och påskyndar infogningar när filen läses in igen.
-  Använd `order-by-primary` alternativet i mysqldump när det gäller dumpning av databaser, så att data skriptas i primär nyckel ordning.
-   Använd alternativet `disable-keys` i mysqldump när dumpnings data ska inaktivera begränsningar för sekundär nyckel före belastningen. Att inaktivera kontroller av sekundär nyckel ger prestanda vinster. Aktivera begränsningarna och kontrol lera data efter belastningen för att säkerställa referens integriteten.
-   Använd partitionerade tabeller när det är lämpligt.
-   Läs in data parallellt. Undvik för mycket parallellitet som skulle innebära att du träffar en resurs gräns och övervaka resurser med hjälp av de mått som är tillgängliga i Azure Portal. 
-   Använd alternativet `defer-table-indexes` i mysqlpump när det gäller dumpning av databaser, så att skapandet av indexet sker efter att tabell data har lästs in.
-   Använd alternativet `skip-definer` i mysqlpump om du vill utesluta definar-och SQL-säkerhetssatser från Create-instruktionerna för vyer och lagrade procedurer.  När du läser in dumpfilen igen skapas objekt som använder standardvärdena för avrundning och SQL-säkerhet.
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

Om du till exempel vill säkerhetskopiera en databas med namnet "testdb" på MySQL-servern med användar namnet "testuser" och utan lösen ord till en fil testdb_backup. SQL, använder du följande kommando. Kommandot säkerhetskopierar `testdb` databasen till en fil med namnet `testdb_backup.sql`, som innehåller alla SQL-instruktioner som krävs för att återskapa databasen. 

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
Skapa en tom databas på mål Azure Database for MySQL servern där du vill migrera data. Använd ett verktyg som MySQL Workbench, TOAD eller Navicat för att skapa databasen. Databasen kan ha samma namn som databasen som innehåller de dumpade data eller så kan du skapa en databas med ett annat namn.

För att ansluta, letar du upp anslutnings informationen i **översikten** över din Azure Database for MySQL.

![Hitta anslutnings informationen i Azure Portal](./media/concepts-migrate-dump-restore/1_server-overview-name-login.png)

Lägg till anslutnings informationen i MySQL Workbench.

![Anslutnings sträng för MySQL Workbench](./media/concepts-migrate-dump-restore/2_setup-new-connection.png)


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

## <a name="next-steps"></a>Nästa steg
- [Anslut program till Azure Database for MySQL](./howto-connection-string.md).
- Mer information om hur du migrerar databaser till Azure Database for MySQL finns i [Guide för databas migrering](https://aka.ms/datamigration).
