---
title: Importera och exportera i Azure Database for MySQL
description: Den här artikeln beskriver vanliga sätt att importera och exportera databaser i Azure Database för MySQL med hjälp av verktyg, till exempel MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: fa72037c8f54271f5651667765c5d5e2e9c03619
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545526"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrera din MySQL-databas med hjälp av importera och exportera
Den här artikeln beskrivs två vanliga sätt att importera och exportera data till en Azure Database for MySQL-server med MySQL Workbench. 

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här guiden, måste du:
- En Azure Database for MySQL-server genom att följa [skapa en Azure Database for MySQL-server med Azure-portalen](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [ned](https://dev.mysql.com/downloads/workbench/), eller något annat MySQL-verktyg för att importera och exportera.

## <a name="use-common-tools"></a>Använd gemensamma verktyg
Använd gemensamma verktyg, till exempel MySQL Workbench, Toad eller Navicat för att ansluta och importera eller exportera data till Azure Database for MySQL via en fjärranslutning. 

Använd dessa verktyg på din klientdator med en Internetanslutning för att ansluta till Azure Database för MySQL. Använda en SSL-krypterad anslutning för Metodtips för säkerhet, enligt beskrivningen i [Konfigurera SSL-anslutning i Azure Database för MySQL](concepts-ssl-connection-security.md).

Du behöver inte att flytta din importera och exportera filer var som helst särskilda molnet när du migrerar till Azure Database för MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Skapa en databas på Azure Database for MySQL-server
Skapa en tom databas på Azure Database for MySQL-server där du vill migrera data. Använd ett verktyg som MySQL Workbench, Toad eller Navicat för att skapa databasen. Databasen kan ha samma namn som den databas som innehåller den dumpade data, eller du kan skapa en databas med ett annat namn.

Om du vill ansluta, letar du upp informationen i den **översikt** för din Azure Database for MySQL.

![Hitta anslutningsinformationen i Azure portal](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Lägg till anslutningsinformationen till MySQL Workbench.

![Anslutningssträngen för MySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Avgöra när du ska importera och exportera tekniker i stället för en dump och återställa
Använda MySQL-verktyg för att importera och exportera databaser till Azure MySQL-databas i följande scenarier. I annat fall kan du dra nytta av den [dumpa och Återställ](concepts-migrate-dump-restore.md) metod, i stället. 

- När du måste välja selektivt några tabeller att importera från en befintlig MySQL-databas till Azure MySQL-databas, är det bäst att använda importera och exportera teknik.  Då kan utelämna du onödiga tabeller med migreringen att spara tid och resurser. Till exempel använda den `--include-tables` eller `--exclude-tables` växel med [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) och `--tables` växel med [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- När du flyttar databasobjekt än tabeller, uttryckligen skapa dessa objekt. Omfattar begränsningar (primärnyckel, sekundärnyckel, index), vyer, funktioner, procedurer, utlösare och andra databasobjekt som du vill migrera.
- När du migrerar data från externa datakällor än en MySQL-databas, skapa flata filer och importera dem med hjälp av [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Se till att alla tabeller i databasen använder InnoDB-lagringsmotorn när du läser in data till Azure Database för MySQL. Azure Database för MySQL stöder bara InnoDB lagringsmotorn, så att det inte stöder alternativt lagringskonto motorer. Om dina tabeller kräver ett alternativt lagringskonto motorer, kan du konvertera dem för att använda InnoDB engine-format innan du migrerar till Azure Database för MySQL. 

Till exempel om du har en WordPress- eller web app som använder MyISAM motorn först konvertera tabellerna genom att migrera data till InnoDB-tabeller. Återställ sedan till Azure Database för MySQL. Använd satsen `ENGINE=INNODB` att motorn för att skapa en tabell och sedan överföra data till tabellen kompatibla före migreringen. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestandarekommendationer för import och export
-   Skapa grupperade index och primärnycklar innan du läser in data. Läsa in data enligt den primära nyckeln. 
-   Fördröjning skapandet av sekundärindex förrän efter data har lästs in. Skapa alla sekundära index när inläsning. 
-   Inaktivera sekundärnyckelbegränsningar innan du läser in. Inaktivera främmande nycklar kontroller ger betydande prestandavinster. Aktivera begränsningarna och verifiera data efter inläsningen så referensintegritet.
-   Läsa in data parallellt. Undvik att för mycket parallellitet som skulle du skriva en resursgräns, och övervaka resurser med hjälp av mått som är tillgängliga i Azure-portalen. 
-   Använd partitionerade tabeller när det är lämpligt.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importera och exportera genom att använda MySQL Workbench
Det finns två sätt att exportera och importera data i MySQL Workbench. Var och en har en annan funktion. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Tabelldata exportera och importera guider objektet webbläsarens snabbmenyn
![MySQL Workbench guider på snabbmenyn för objekt-webbläsare](./media/concepts-migrate-import-export/p1.png)

Guider för tabelldata stöder importera och exportera åtgärder med hjälp av CSV och JSON-filer. De innehåller flera konfigurationsalternativ som avgränsare, Kolumnurval och kodning val. Du kan utföra varje guide mot lokalt eller via en fjärranslutning anslutna MySQL-servrar. Import-åtgärd innehåller tabeller, kolumner och typmappningen. 

Du kan komma åt dessa guider objektet webbläsarens snabbmenyn genom att högerklicka på en tabell. Välj antingen **Data exportera Tabellguiden** eller **guiden Importera tabell-Data**. 

#### <a name="table-data-export-wizard"></a>Tabellguiden Data Export
I följande exempel exporterar tabellen till en CSV-fil: 
1. Högerklicka på tabellen på databasen som ska exporteras. 
2. Välj **tabellen dataexportguiden**. Välj kolumnerna som ska exporteras, rad förskjutning (om sådan finns) och (om sådan finns). 
3. På den **väljer data för export** klickar du på **nästa**. Välj sökvägen till filen, CSV eller JSON-filtypen. Välj också avgränsare för rad, metoden för omslutande strängar och fältavgränsare. 
4. På den **väljer utdata filplats** klickar du på **nästa**. 
5. På den **exportera data** klickar du på **nästa**.

#### <a name="table-data-import-wizard"></a>Guiden Importera tabell-Data
I följande exempel importerar tabellen från en CSV-fil:
1. Högerklicka på tabellen i databasen som ska importeras. 
2. Bläddra till och välj den CSV-fil som ska importeras och klickar sedan på **nästa**. 
3. Välj tabellen (nytt eller befintligt), och markera eller avmarkera den **trunkering registret före import** markerar du kryssrutan. Klicka på **Nästa**.
4. Välj kodning och kolumnerna som ska importeras och klicka sedan på **nästa**. 
5. På den **dataimport** klickar du på **nästa**. Guiden importerar data i enlighet med detta.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL-data exportera och importera guider från navigeringsfönstret
Använd en guide för att exportera eller importera SQL genereras från MySQL Workbench eller genereras från kommandot mysqldump. Komma åt dessa guider från den **Navigator** fönstret eller genom att välja **Server** på huvudmenyn. Välj sedan **dataexport** eller **dataimporten**. 

#### <a name="data-export"></a>Dataexport
![MySQL Workbench data exportera med hjälp av fönstret navigatör](./media/concepts-migrate-import-export/p2.png)

Du kan använda den **dataexport** flik för att exportera dina MySQL-data. 
1. Välj varje schema som du vill exportera, du kan också välja specifika schemat objekt/tabeller från varje schema och generera exporten. Alternativ inkluderar export till en projektmapp, till exempel eller en fristående SQL-fil, dumpa lagrade rutiner och händelser eller hoppa över tabelldata. 
 
   Du kan också använda **exportera resultat** så här exporterar du en specifik resultatmängd i ett annat format, till exempel CSV, JSON, HTML och XML-SQL-redigeraren. 
3. Välj databasobjekt som ska exporteras och konfigurera relaterade alternativ.
4. Klicka på **uppdatera** att läsa in de aktuella objekten.
5. Du kan också öppna den **avancerade alternativ** flik för att förfina exportåtgärden. Lägg exempelvis till tabellen lås, Använd Ersätt i stället för insert-satser och citatidentifierare med backtick tecken.
6. Klicka på **starta exportera** att starta exportprocessen.


#### <a name="data-import"></a>Importera data
![MySQL Workbench dataimporten med Management navigatör](./media/concepts-migrate-import-export/p3.png)

Du kan använda den **dataimport** flik för att importera eller Återställ exporterade data från exportåtgärden data eller från kommandot mysqldump. 
1. Väljer du den projektmapp, till exempel eller en fristående SQL-fil, schemat om du vill importera till eller **New** att definiera ett nytt schema. 
2. Klicka på **börja importera** att starta importen.

## <a name="next-steps"></a>Nästa steg
- Som en annan förhållningssätt till Databasmigrering, läsa [migrera din MySQL-databas med dumpa och Återställ i Azure Database för MySQL](concepts-migrate-dump-restore.md).
- Mer information om hur du migrerar databaser till Azure Database för MySQL, finns i den [Guide för Databasmigrering](https://aka.ms/datamigration). 
