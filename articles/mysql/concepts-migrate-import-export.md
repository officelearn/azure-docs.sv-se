---
title: Import och export – Azure Database för MySQL
description: I den här artikeln beskrivs vanliga sätt att importera och exportera databaser i Azure Database for MySQL med hjälp av verktyg som MySQL Workbench.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/27/2020
ms.openlocfilehash: 83b0a69e063e9427c726216ef873f5a1c97f9582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163734"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Migrera MySQL-databasen med hjälp av import och export
I den här artikeln beskrivs två vanliga metoder för att importera och exportera data till en Azure-databas för MySQL-server med hjälp av MySQL Workbench. 

## <a name="before-you-begin"></a>Innan du börjar
För att gå igenom den här guiden behöver du:
- En Azure-databas för MySQL-server genom att följa [Skapa en Azure Database for MySQL-server med Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- MySQL Workbench [MySQL Workbench Download](https://dev.mysql.com/downloads/workbench/) eller annan tredje part MySQL verktyg för att göra import / export.

## <a name="use-common-tools"></a>Använd vanliga verktyg
Använd vanliga verktyg och verktyg som MySQL Workbench eller mysqldump för att fjärr ansluta och importera eller exportera data till Azure Database för MySQL. 

Använd sådana verktyg på klientdatorn med en Internetanslutning för att ansluta till Azure Database for MySQL. Använd en SSL-krypterad anslutning för bästa säkerhetspraxis, enligt beskrivningen i [Konfigurera SSL-anslutning i Azure Database för MySQL](concepts-ssl-connection-security.md).

Du behöver inte flytta dina import- och exportfiler till någon särskild molnplats när du migrerar till Azure Database for MySQL. 

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Skapa en databas på Azure Database for MySQL-servern
Skapa en tom databas på Azure Database for MySQL-servern där du vill migrera data. Använd ett verktyg som MySQL Workbench, Toad eller Navicat för att skapa databasen. Databasen kan ha samma namn som databasen som innehåller de dumpade data, eller så kan du skapa en databas med ett annat namn.

Om du vill ansluta letar du reda på anslutningsinformationen i **översikten över** din Azure-databas för MySQL.

![Hitta anslutningsinformationen i Azure-portalen](./media/concepts-migrate-import-export/1_server-overview-name-login.png)

Lägg till anslutningsinformationen i MySQL Workbench.

![Anslutningssträngen mySQL Workbench](./media/concepts-migrate-import-export/2_setup-new-connection.png)

## <a name="determine-when-to-use-import-and-export-techniques-instead-of-a-dump-and-restore"></a>Bestäm när import- och exporttekniker ska användas i stället för en dump och återställning
Använd MySQL-verktyg för att importera och exportera databaser till Azure MySQL-databas i följande scenarier. I andra scenarier kan du dra nytta av att använda [dump- och återställningsmetoden](concepts-migrate-dump-restore.md) i stället. 

- När du behöver välja några tabeller som du vill importera från en befintlig MySQL-databas till Azure MySQL-databas är det bäst att använda import- och exporttekniken.  På så sätt kan du utelämna onödiga tabeller från migreringen för att spara tid och resurser. Till exempel, `--include-tables` använd `--exclude-tables` eller växla med [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) och switch med `--tables` [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- När du flyttar andra databasobjekt än tabeller skapar du uttryckligen dessa objekt. Inkludera begränsningar (primärnyckel, sekundärnyckel, index), vyer, funktioner, procedurer, utlösare och andra databasobjekt som du vill migrera.
- När du migrerar data från andra externa datakällor än en MySQL-databas skapar du platta filer och importerar dem med [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

Kontrollera att alla tabeller i databasen använder InnoDB-lagringsmotorn när du läser in data i Azure Database for MySQL. Azure Database for MySQL stöder endast InnoDB-lagringsmotorn, så den stöder inte alternativa lagringsmotorer. Om dina tabeller kräver alternativa lagringsmotorer måste du konvertera dem för att använda InnoDB-motorformatet före migreringen till Azure Database for MySQL. 

Om du till exempel har en WordPress- eller webbapp som använder MyISAM-motorn konverterar du först tabellerna genom att migrera data till InnoDB-tabeller. Återställ sedan till Azure-databas för MySQL. Använd satsen `ENGINE=INNODB` för att ställa in motorn för att skapa en tabell och sedan överföra data till den kompatibla tabellen före migreringen. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestandarekommendationer för import och export
-   Skapa klustrade index och primära nycklar innan du läser in data. Läs in data i primärnyckelordning. 
-   Fördröja skapandet av sekundära index tills data har lästs in. Skapa alla sekundära index efter inläsning. 
-   Inaktivera begränsningar för sekundärnyckel innan du läser in. Inaktivera utländska nyckelkontroller ger betydande prestandavinster. Aktivera begränsningarna och verifiera data efter inläsningen för att säkerställa referensintegritet.
-   Läs in data parallellt. Undvik för mycket parallellism som gör att du når en resursgräns och övervakar resurser med hjälp av de mått som finns i Azure-portalen. 
-   Använd partitionerade tabeller när det är lämpligt.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importera och exportera med MySQL Workbench
Det finns två sätt att exportera och importera data i MySQL Workbench. Varje tjänar ett annat syfte. 

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Tabelldataexport- och importguider från objektbläddrarens snabbmeny
![MySQL Workbench-guider på objektbläddrarens snabbmeny](./media/concepts-migrate-import-export/p1.png)

Guiderna för tabelldata stöder import- och exportåtgärder med hjälp av CSV- och JSON-filer. De innehåller flera konfigurationsalternativ, till exempel avgränsare, kolumnval och kodningsmarkering. Du kan utföra varje guide mot lokala eller fjärranslutna MySQL-servrar. Importåtgärden omfattar tabell-, kolumn- och typmappning. 

Du kan komma åt dessa guider från objektbläddrarens snabbmeny genom att högerklicka på en tabell. Välj sedan **guiden Exportera tabelldata** eller **guiden Importera tabelldata**. 

#### <a name="table-data-export-wizard"></a>Exportguide för tabelldata
I följande exempel exporteras tabellen till en CSV-fil: 
1. Högerklicka på tabellen för den databas som ska exporteras. 
2. Välj **exportguide för tabelldata**. Markera de kolumner som ska exporteras, radförskjutning (om sådan finns) och räkna (om sådana finns). 
3. Klicka på **Nästa**på sidan **Välj data för export** . Markera filsökvägen CSV eller JSON-filtypen. Markera även radavgränsaren, metoden för att omge strängar och fältavgränsare. 
4. Klicka på **Nästa**på **sidan Välj utdatafilsplats** . 
5. Klicka på **Nästa**på sidan **Exportera data** .

#### <a name="table-data-import-wizard"></a>Guiden Importera tabelldata
I följande exempel importeras tabellen från en CSV-fil:
1. Högerklicka på tabellen för den databas som ska importeras. 
2. Bläddra till och markera den CSV-fil som ska importeras och klicka sedan på **Nästa**. 
3. Markera måltabellen (ny eller befintlig) och markera eller avmarkera kryssrutan **Trunkera tabellen före import.** Klicka på **Nästa**.
4. Markera kodning och de kolumner som ska importeras och klicka sedan på **Nästa**. 
5. Klicka på **Nästa**på sidan **Importera data** . Informationen importeras i enlighet med detta.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Guiden exportera och importera SQL-data från fönstret Navigator
Använd en guide för att exportera eller importera SQL som genereras från MySQL Workbench eller genererat från mysqldump-kommandot. Öppna dessa guider i **navigatorfönstret** eller genom att välja **Server** på huvudmenyn. Välj sedan **Dataexport** eller **Dataimport**. 

#### <a name="data-export"></a>Dataexport
![MySQL Workbench-data exporterar med hjälp av fönstret Navigator](./media/concepts-migrate-import-export/p2.png)

Du kan använda fliken **Dataexport** för att exportera mysql-data. 
1. Markera varje schema som du vill exportera, välj eventuellt specifika schemaobjekt/tabeller från varje schema och generera exporten. Konfigurationsalternativen inkluderar export till en projektmapp eller fristående SQL-fil, dumpa lagrade rutiner och händelser eller hoppa över tabelldata. 
 
   Du kan också använda **Exportera en resultatuppsättning** för att exportera en viss resultatuppsättning i SQL-redigeraren till ett annat format, till exempel CSV, JSON, HTML och XML. 
3. Markera de databasobjekt som ska exporteras och konfigurera relaterade alternativ.
4. Klicka på **Uppdatera** om du vill läsa in de aktuella objekten.
5. Du kan också öppna fliken **Avancerade alternativ** för att förfina exportåtgärden. Lägg till tabelllås, använd ersätt i stället för infoga satser och offertidentifierare med bakåtsträvande tecken.
6. Klicka på **Starta export** om du vill påbörja exportprocessen.


#### <a name="data-import"></a>Dataimport
![MySQL Workbench Data Import med Hjälp av Management Navigator](./media/concepts-migrate-import-export/p3.png)

Du kan använda fliken **Dataimport** för att importera eller återställa exporterade data från dataexportåtgärden eller från kommandot mysqldump. 
1. Välj projektmappen eller den fristående SQL-filen, välj det schema som ska importeras till eller välj **Nytt** för att definiera ett nytt schema. 
2. Klicka på **Starta import** om du vill påbörja importprocessen.

## <a name="next-steps"></a>Nästa steg
- Som en annan migreringsmetod läser [du Migrera din MySQL-databas med dump och återställning i Azure Database for MySQL](concepts-migrate-dump-restore.md).
- Mer information om hur du migrerar databaser till Azure Database for MySQL finns i [guiden För databasmigrering](https://aka.ms/datamigration). 
