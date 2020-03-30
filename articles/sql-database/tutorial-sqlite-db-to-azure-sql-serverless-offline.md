---
title: 'Självstudiekurs: Så här migrerar du SQLite-databasen till Azure SQL Database Serverless'
description: Lär dig att utföra en offlinemigrering från SQLite till Azure SQL Database Serverless med hjälp av Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
manager: prda
ms.service: sql-database
ms.workload: data-services
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: c718daa4bc99bffd6fcfeb084299bed6682fe884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780514"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Migrera din SQLite-databas till Azure SQL Database Serverless
För många människor ger SQLite sin första erfarenhet av databaser och SQL-programmering. Det är inkludering i många operativsystem och populära applikationer gör SQLite till en av de mest använda och använda databasmotorerna i världen. Och eftersom det är sannolikt den första databasmotorn många människor använder, kan det ofta hamna som en central del av projekt eller applikationer. I sådana fall där projektet eller programmet utgår från den ursprungliga SQLite-implementeringen kan utvecklare behöva migrera sina data till ett tillförlitligt, centraliserat datalager.

Azure SQL Database serverless är en beräkningsnivå för enskilda databaser som automatiskt skalar beräkning baserat på arbetsbelastningsbehov och räkningar för mängden beräkning som används per sekund. Den serverlösa beräkningsnivån pausar också automatiskt databaser under inaktiva perioder när endast lagring faktureras och automatiskt återupptar databaser när aktiviteten returnerar.

När du har följt stegen nedan migreras databasen till Azure SQL Database Serverless, så att du kan göra databasen tillgänglig för andra användare eller program i molnet och bara betala för det du använder, med minimala programkodändringar.

## <a name="prerequisites"></a>Krav
- En Azure-prenumeration
- SQLite2 eller SQLite3 databas som du vill migrera
- En Windows-miljö
  - Om du inte har en lokal Windows-miljö kan du använda en Windows VM i Azure för migreringen. Flytta och gör din SQLite-databasfil tillgänglig på den virtuella datorn med Azure Files and Storage Explorer.

## <a name="steps"></a>Steg

1. Etablera en ny Azure SQL-databas på beräkningsnivån Serverlös.

    ![skärmbild av Azure-portal som visar etableringsexempel för azure sql database serverless](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/provision-serverless.png)

2. Se till att du har din SQLite-databasfil tillgänglig i din Windows-miljö. Installera en SQLite ODBC-drivrutin om du inte redan har en (det finns många tillgängliga i Öppen källkod, http://www.ch-werner.de/sqliteodbc/)till exempel .

3. Skapa ett system-DSN för databasen. Se till att du använder datakälladministratörsprogrammet som matchar systemarkitekturen (32-bitars vs 64-bitars). Du hittar vilken version du kör i systeminställningarna.

    - Öppna ODBC-datakällaadministratören i din miljö.
    - Klicka på fliken System-DSN och klicka på "Lägg till"
    - Välj den SQLite ODBC-anslutning som du har installerat och ge anslutningen ett meningsfullt namn, till exempel sqlitemigrationsource
    - Ange databasnamnet till DB-filen
    - Spara och avsluta

4. Hämta och installera den självvärderade integrationskörningen. Det enklaste sättet att göra detta är alternativet Express installera, som beskrivs i dokumentationen. Om du väljer en manuell installation måste du förse programmet med en autentiseringsnyckel som kan placeras i din Data Factory-instans genom att:

    - Starta ADF (Författare och övervakare från tjänsten i Azure-portalen)
    - Klicka på fliken "Författare" (Blå penna) till vänster
    - Klicka på Anslutningar (längst ned till vänster) och sedan integrationskörningar
    - Lägg till nya självvärderade integrationskörning, ge den ett namn, välj *Alternativ 2*.

5. Skapa en ny länkad tjänst för källdatabasen SQLite i datafabriken.

    ![skärmbild som visar tomt blad av länkade tjänster i Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create.png)

6. Klicka på Ny under Länkad tjänst i Anslutningar

7. Sök efter och välj "ODBC"-kontakten


    ![skärmbild som visar ODBC-anslutningslogotyp i bladet för länkade tjänster i Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-odbc.png)

8. Ge den länkade tjänsten ett meningsfullt namn, till exempel "sqlite_odbc". Välj din integrationskörning från listrutan "Anslut via integrationskörning". Ange nedanstående i anslutningssträngen och ersätt variabeln Initial Catalog med filsökvägen för DB-filen och DSN med namnet på system-DSN-anslutningen: 

    ```
    Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Ange autentiseringstypen till Anonym

10. Testa anslutningen

    ![skärmbild som visar lyckad anslutning i Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-test-successful.png)

11. Skapa en annan länkad tjänst för ditt Serverless SQL-mål. Markera databasen med hjälp av guiden länkad tjänst och ange SQL-autentiseringsuppgifter.

    ![skärmbild som visar Azure SQL Database som valts i Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/linked-services-create-target.png)

12. Extrahera CREATE TABLE-satserna från din SQLite-databas. Du kan göra detta genom att köra nedanstående Python-skript på databasfilen.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Skapa landningstabellerna i serverlös SQL-målmiljö genom att kopiera CREATE-tabellsatserna från filen CreateTables.sql och köra SQL-uttrycken i Frågeredigeraren i Azure-portalen.

14. Gå tillbaka till startskärmen på datafabriken och klicka på "Kopiera data" för att köra igenom guiden skapa arbetstillfällen.

    ![skärmbild som visar kopiera dataguidens logotyp i Azure Data Factory](./media/tutorial-sqlite-db-to-azure-sql-serverless-offline/copy-data.png)

15. Markera alla tabeller från källdatabasen SQLite med kryssrutorna och mappa dem till måltabellerna i Azure SQL. När jobbet har körts har du migrerat dina data från SQLite till Azure SQL!

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Snabbstart: Skapa en enda databas i Azure SQL Database med Azure-portalen](sql-database-single-database-get-started.md).
- För resursbegränsningar finns i [Resursgränser för serverlös beräkningsnivå](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
