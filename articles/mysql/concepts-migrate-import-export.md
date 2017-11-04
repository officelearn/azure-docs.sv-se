---
title: "Importera och exportera i Azure-databas för MySQL | Microsoft Docs"
description: "Den här artikeln beskrivs vanliga sätt att importera och exportera databaser i Azure-databas för MySQL, med hjälp av verktyg som MySQL-arbetsstationen."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 11/02/2017
ms.openlocfilehash: 36ffa7082ce60093cbd90d0c12187e28f517646d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrera MySQL-databas genom att importera och exportera
Den här artikeln förklarar två vanliga sätt att importera och exportera data till en Azure-databas för MySQL-servern genom att använda MySQL-arbetsstationen. 

## <a name="before-you-begin"></a>Innan du börjar
Du behöver följande för att gå igenom den här instruktioner:
- En Azure-databas för MySQL-server genom att följa [skapa en Azure-databas för MySQL-server med hjälp av Azure portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL-arbetsstationen [ned](https://dev.mysql.com/downloads/workbench/), eller något annat MySQL-verktyg för att importera och exportera.

## <a name="use-common-tools"></a>Använd gemensamma verktyg
Använd gemensamma verktyg som MySQL arbetsstationen, Toad eller Navicat ansluta och importera eller exportera data till Azure-databas för MySQL via fjärranslutning. 

Använd dessa verktyg på klientdatorn med en Internetanslutning för att ansluta till Azure-databas för MySQL. Använd en SSL-krypterad anslutning Metodtips för säkerhet, enligt beskrivningen i [Konfigurera SSL-anslutning i Azure-databas för MySQL](concepts-ssl-connection-security.md).

Du behöver inte flytta importen och exportera filer som helst särskilda moln när du migrerar till Azure-databas för MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Skapa en databas på Azure-databasen för MySQL-server
Skapa en tom databas på Azure-databasen för MySQL-server där du vill migrera data. Använda ett verktyg som MySQL arbetsstationen, Toad eller Navicat för att skapa databasen. Databasen kan ha samma namn som den databas som innehåller dumpade data eller du kan skapa en databas med ett annat namn.

Om du vill hämta ansluten, hitta anslutningsinformationen på den **egenskaper** rutan i Azure för MySQL-databas.

![Hitta anslutningsinformationen i Azure-portalen](./media/concepts-migrate-import-export/1_server-properties-name-login.png)

Lägg till anslutningsinformationen MySQL-arbetsstationen.

![MySQL-arbetsstationen anslutningssträngen](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Avgöra när du ska importera och exportera metoder i stället för en dump och återställa
Använda MySQL-verktyg för att importera och exportera databaser till Azure MySQL-databas i följande scenarier. I annat fall kan du dra nytta av den [dump och återställa](concepts-migrate-dump-restore.md) närmar sig i stället. 

- När du måste välja selektivt några tabeller för att importera från en befintlig MySQL-databas till Azure MySQL-databas, är det bäst att använda importera och exportera tekniken.  På så sätt, kan du utelämna onödiga tabeller från migrering för att spara tid och resurser. Till exempel använda den `--include-tables` eller `--exclude-tables` växel med [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) och `--tables` växel med [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- När du flyttar databasobjekt än tabeller, uttryckligen skapa dessa objekt. Inkludera begränsningar (primärnyckel, sekundärnyckel, index), vyer, funktioner, procedurer, utlösare och andra databasobjekt som du vill migrera.
- När du migrerar data från externa datakällor än en MySQL-databas, skapa flat-filer och importera dem med hjälp av [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Kontrollera att alla tabeller i databasen lagringsmotorn InnoDB när du läser in data till Azure-databas för MySQL. Azure-databas för MySQL stöder endast InnoDB lagringsmotorn, därför inte stöd för alternativa lagring motorer. Om dina tabeller kräver alternativa lagring motorer, måste du konvertera dem för att använda formatet InnoDB motorn före migreringen till Azure-databas för MySQL. 

Till exempel om du har en WordPress- eller web app som använder MyISAM motorn först konvertera tabellerna genom att migrera data i InnoDB tabeller. Återställer du sedan till Azure-databas för MySQL. Använd satsen `ENGINE=INNODB` ange motorn för att skapa en tabell och överföra data till tabellen kompatibel före migreringen. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Rekommendationer för import och export
-   Skapa grupperade index och primärnycklar innan data läses in. Läsa in data i primär nyckel ordning. 
-   Fördröjning skapandet av sekundärindex förrän data har lästs in. Skapa alla sekundärindex efter inläsning. 
-   Inaktivera sekundärnyckelbegränsningar före inläsning. Inaktivera främmande nycklar kontroller ger betydande prestandaförbättringar. Aktivera begränsningar och verifiera data efter belastningen så referensintegritet.
-   Läsa in data parallellt. Undvik att för mycket parallellitet som skulle innebära att du har nått gränsen för en resurs och övervaka resurser med hjälp av mätvärdena som är tillgängliga i Azure-portalen. 
-   Använd partitionerade tabeller när det är lämpligt.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importera och exportera genom att använda MySQL-arbetsstationen
Det finns två sätt att exportera och importera data i MySQL-arbetsstationen. Var och en har ett annat syfte. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Tabelldata exportera och importera guider i Objektgranskning snabbmenyn
![MySQL-arbetsstationen guider i webbläsaren objekt snabbmeny](./media/concepts-migrate-import-export/p1.png)

Guider för tabelldata stöd importera och exportera åtgärder med hjälp av CSV- och JSON-filer. De omfattar flera konfigurationsalternativ som avgränsare, kolumnen och val av kodning. Du kan göra guiderna mot lokal eller fjärransluten anslutna MySQL-servrar. Åtgärd för import innehåller tabeller, kolumner och mappning. 

Du kan komma åt dessa guider i Objektgranskning snabbmenyn genom att högerklicka på en tabell. Välj antingen **Data exportera Tabellguiden** eller **guiden Importera tabell-Data**. 

#### <a name="table-data-export-wizard"></a>Tabellguiden Data Export
I följande exempel exporterar tabellen till en CSV-fil: 
1. Högerklicka på tabellen i databasen för att exporteras. 
2. Välj **tabell guiden exportera affärsdata**. Markera kolumnerna som ska exporteras, rad förskjutningen (eventuella) och count (eventuella). 
3. På den **väljer data för export** klickar du på **nästa**. Välj sökvägen till filen, CSV eller JSON filtyp. Välj också radbrytningstecken metod i den omslutande strängar och fältavgränsaren. 
4. På den **väljer utdata filplats** klickar du på **nästa**. 
5. På den **exportera data** klickar du på **nästa**.

#### <a name="table-data-import-wizard"></a>Guiden Importera tabell-Data
I följande exempel importerar tabellen från en CSV-fil:
1. Högerklicka på tabellen i databasen som ska importeras. 
2. Bläddra till och välj den CSV-fil som ska importeras och klicka sedan på **nästa**. 
3. Välj tabellen (ny eller befintlig), och markera eller avmarkera den **trunkeringen registret före import** kryssrutan. Klicka på **Nästa**.
4. Välj kodning och kolumnerna som ska importeras och klicka sedan på **nästa**. 
5. På den **dataimport** klickar du på **nästa**. Guiden importerar data i enlighet med detta.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>SQL-data exportera och importera guider från fönstret Navigator
Använda en guide för att exportera eller importera SQL genereras från MySQL arbetsstationen eller genereras från kommandot mysqldump. Komma åt dessa guider från den **Navigator** fönstret eller genom att välja **Server** på huvudmenyn. Välj sedan **dataexporten** eller **dataimporten**. 

#### <a name="data-export"></a>Export av data
![MySQL-arbetsstationen data exportera med hjälp av fönstret Navigator](./media/concepts-migrate-import-export/p2.png)

Du kan använda den **dataexporten** fliken Exportera MySQL-data. 
1. Markera varje schema som du vill exportera, du kan också välja specifika schemat objekt/tabeller från varje schema och generera exporten. Konfigurationsalternativ omfattar export till en projektmappen eller fristående SQL-fil, dump lagrade rutiner och händelser eller hoppa över tabelldata. 
 
   Du kan också använda **exportera resultatuppsättning** att exportera en specifik resultatmängd i SQL-redigeraren till ett annat format, till exempel CSV, JSON, HTML- och XML. 
3. Välj objekt i databasen för att exportera och konfigurera relaterade alternativ.
4. Klicka på **uppdatera** att läsa in de aktuella objekten.
5. Du kan också öppna den **avancerade alternativ** fliken förfina exporten. Lägg exempelvis till tabellen lås, Använd Ersätt i stället för insert-satser och citatidentifierare med backtick tecken.
6. Klicka på **starta exportera** ska börja vid exporten.


#### <a name="data-import"></a>Import av data
![MySQL-arbetsstationen dataimporten använder Management Navigator](./media/concepts-migrate-import-export/p3.png)

Du kan använda den **dataimport** att importera eller Återställ exporterade data från exporten data eller kommandot mysqldump. 
1. Välj projektmappen eller fristående SQL-filen, Välj scheman som du vill importera till eller **ny** att definiera ett nytt schema. 
2. Klicka på **starta importera** att starta importen.

## <a name="next-steps"></a>Nästa steg
Som en annan metod för migrering, läsa [migrera din MySQL-databas med dump och återställa i Azure-databas för MySQL](concepts-migrate-dump-restore.md). 
