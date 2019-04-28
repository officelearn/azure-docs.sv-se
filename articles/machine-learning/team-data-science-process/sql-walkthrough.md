---
title: Skapa och distribuera en modell i en SQL Server VM - Team Data Science Process
description: Skapa och distribuera en machine learning-modell som använder SQL Server på en virtuell Azure-dator med en datauppsättning som är allmänt tillgängliga.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c9d707d1a76b3b5913d66745767df8e84362a192
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61045804"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Team Data Science Process i praktiken: med SQL Server
I den här självstudien får du går igenom processen för att skapa och distribuera en maskininlärningsmodell med SQL Server och en datauppsättning som är allmänt tillgängliga – [NYC Taxi kommunikation](https://www.andresmh.com/nyctaxitrips/) datauppsättning. Proceduren följer en standard arbetsflöde för datavetenskap: mata in och utforska data, bygg funktioner för att förenkla inlärningen, och sedan skapa och distribuera en modell.

## <a name="dataset"></a>NYC Taxi sätts datauppsättning beskrivning
NYC Taxi resedata är cirka 20GB komprimerat CSV-filer (~ 48GB okomprimerad), som består av mer än 173 miljoner enskilda kommunikation och priser betalda för varje resa. Hämtning och dropoff plats och tid, avidentifierade hack (drivrutin) licensnummer och medallion (taxi's unikt id) antalet innehåller varje resa-post. Informationen som täcker alla kommunikation i år 2013 och anges i följande två datauppsättningar för varje månad:

1. Trip_data CSV innehåller resans information, till exempel antalet passagerare, hämtning och dropoff punkter, resans varaktighet och resans längd. Här följer några Exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Trip_fare CSV innehåller information om avgiften betalat för varje förflyttning, till exempel betalningstypen, avgiften belopp, tillägg och skatter, tips och vägtullar, och det totala beloppet som betalas. Här följer några Exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den unika nyckeln för att ansluta till resans\_data och resans\_avgiften består av fälten: medallion, hacka\_licens och upphämtning\_datetime.

## <a name="mltasks"></a>Exempel på uppgifter för förutsägelse
Vi kommer att formulera tre förutsägelse problem baserat på den *tips\_belopp*, nämligen:

1. Binär klassificering: Förutsäga huruvida ett tips har betalat för en resa, dvs. en *tips\_belopp* som är större än $0 är ett positivt exempel, medan en *tips\_belopp* $0 är ett exempel på negativt.
2. Multiklass-baserad klassificering: Att förutsäga vilka tips som har betalat för resan. Vi dela upp den *tips\_belopp* i fem lagerplatser eller klasser:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Regression uppgift: Att förutsäga mängden tips som har betalat för en resa.  

## <a name="setup"></a>In the Azure datavetenskapsmiljö för avancerad analys
Som du kan se den [Plan Your Environment](plan-your-environment.md) guide, det finns flera alternativ för att arbeta med NYC Taxi och RETUR-datauppsättningen i Azure:

* Arbeta med data i Azure-blobar i Azure Machine Learning-modell
* Läsa in data i en SQL Server-databas och en modell i Azure Machine Learning

I den här självstudien visar vi parallell massimport av data till en SQL Server, datagranskning, funktion tekniska och ned sampling med SQL Server Management Studio samt med hjälp av IPython Notebook. [Exempel på skript](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) och [IPython-anteckningsböcker](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) delas i GitHub. Det finns också en exemplet IPython notebook att arbeta med data i Azure-blobar på samma plats.

Du ställer in din Azure Data Science-miljö:

1. [Skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md)
2. [Skapa en Azure Machine Learning-arbetsyta](../studio/create-workspace.md)
3. [Etablera en virtuell dator för datavetenskap](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), vilket ger en SQL Server och en IPython Notebook-server.
   
   > [!NOTE]
   > Exempel på skript och IPython-anteckningsböcker laddas ned till den virtuella datorn för datavetenskap under installationen. När det virtuella datorn skriptet är klar blir exemplen i den Virtuella datorns dokumentbibliotek:  
   > 
   > * Exemplet skript: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * IPython Exempelanteckningsböcker: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   där `<user_name>` är den Virtuella datorns Windows-inloggningsnamn. Vi kommer att referera till exempel mappar som **exempelskript** och **IPython Exempelanteckningsböcker**.
   > 
   > 

Baserat på storleken på datauppsättningen och datakällplats valda Azure målmiljön kan det här scenariot är liknar [scenariot \#5: Stor datauppsättning i lokala filer, rikta SQL Server i Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Hämta Data från offentliga källa
Att hämta den [NYC Taxi kommunikation](https://www.andresmh.com/nyctaxitrips/) datauppsättning från dess offentlig plats, kan du använda någon av metoderna som beskrivs i [flytta Data till och från Azure Blob Storage](move-azure-blob.md) att kopiera data till din nya virtuella dator.

Kopiera data med AzCopy:

1. Logga in på den virtuella datorn (VM)
2. Skapa en ny katalog i den Virtuella datorns datadisk (Obs: Använd inte den temporära disken som medföljer den virtuella datorn som en datadisk).
3. Kör följande Azcopy kommandorad, ersätta < path_to_data_folder > med dina datamapp som skapades i (2) i ett kommandotolksfönster:
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    När AzCopy är klar totalt av 24 zippade CSV-filer (12 för resans\_data och 12 för resans\_avgiften) ska vara i datamappen.
4. Packa upp de hämtade filerna. Kom ihåg vilken mapp där de okomprimerade filerna finns. Den här mappen kommer kallas den < sökväg\_till\_data\_filer\>.

## <a name="dbload"></a>Massinläsning importera Data till SQL Server-databas
Prestanda för inläsning av/överföring av stora mängder data till en SQL-databas och efterföljande frågor kan förbättras genom att använda *partitionerade tabeller och vyer*. I det här avsnittet kommer vi följa instruktionerna i [parallella Bulk Data Import med hjälp av SQL-partitionstabeller](parallel-load-sql-partitioned-tables.md) att skapa en ny databas och läsa in data i partitionerade tabeller parallellt.

1. Logga in den virtuella datorn, starta **SQL Server Management Studio**.
2. Anslut med Windows-autentisering.
   
    ![SSMS Anslut][12]
3. Om du har ännu inte har ändrat SQL Server-autentiseringsläget och skapas en ny användare för SQL-inloggning, öppna skriptfilen med namnet **ändra\_auth.sql** i den **exempelskript** mapp. Ändra standard-användarnamn och lösenord. Klicka på **! Köra** i verktygsfältet för att köra skriptet.
   
    ![Kör skriptet][13]
4. Verifiera och/eller ändra SQL Server standard databasen och loggfiler mapparna så som nyligen skapade databaser kommer att lagras i en datadisk. SQL Server-VM-avbildning som är optimerad för datawarehousing belastning är förkonfigurerad med data och loggfiler diskar. Om din virtuella dator inkluderade inte en datadisk och du har lagt till nya virtuella hårddiskar under installationsprocessen VM, ändra standardmappar enligt följande:
   
   * Högerklicka på namnet på SQL Server på den vänstra panelen och klicka på **egenskaper**.
     
       ![Egenskaper för SQL Server][14]
   * Välj **databasinställningar** från den **Välj en sida** listan till vänster.
   * Verifiera och/eller ändra den **databasen standardplatserna** till den **datadisk** platserna för ditt val. Det här är där nya databaser finns om skapats med standardinställningarna för platsen.
     
       ![Standardvärdet för SQL-databasen][15]  
5. Om du vill skapa en ny databas och en uppsättning filgrupper för partitionerade tabeller, öppna exempelskriptet **skapa\_db\_default.sql**. Skriptet skapar en ny databas med namnet **TaxiNYC** och 12 filgrupper på standardplatsen för data. Varje filgrupp ska innehålla en månad resans\_data och resans\_färdavgiften data. Ändra namnet på databasen, om så önskas. Klicka på **! Köra** att köra skriptet.
6. Skapa sedan två partitionstabeller, en för resan\_data och en annan för resan\_avgiften. Öppna exempelskriptet **skapa\_partitionerade\_table.sql**, kan:
   
   * Skapa en partitionsfunktion om du vill dela upp informationen per månad.
   * Skapa ett partitionsschema att mappa varje månad data till en annan filgrupp.
   * Skapa två partitionerade tabeller som mappats till partitionsschemat: **nyctaxi\_resans** ska innehålla resan\_data och **nyctaxi\_avgiften** ska innehålla resans\_färdavgiften data.
     
     Klicka på **! Köra** att köra skriptet och skapa partitionerade tabeller.
7. I den **exempelskript** mapp, det finns två PowerShell-exempelskript som demonstrerar parallella bulk import av data till SQL Server-tabeller.
   
   * **BCP\_parallella\_generic.ps1** är ett Allmänt skript till parallella bulk importera data till en tabell. Ändra det här skriptet för att ställa in indata och target-variabler som anges i kommentar raderna i skriptet.
   * **BCP\_parallella\_nyctaxi.ps1** är en förkonfigurerad version av det allmänna skriptet och kan användas för att läsa in båda tabellerna för NYC Taxi och RETUR-data.  
8. Högerklicka på den **bcp\_parallella\_nyctaxi.ps1** skriptets namn och klicka på **redigera** att öppna den i PowerShell. Granska de förinställda variablerna och ändra baserat på din valda databasens namn, indata-mappen, log målmappen och sökvägar till formatet exempelfilerna **nyctaxi_trip.xml** och **nyctaxi\_fare.xml** (i den **exempelskript** mapp).
   
    ![Importera bulkdata][16]
   
    Du kan också välja autentiseringsläge, standard är Windows-autentisering. Klicka på den gröna pilen i verktygsfältet för att köra. Skriptet startar 24 import massåtgärder i parallella, 12 för varje tabell som är partitionerad. Du kan övervaka förloppet för data import genom att öppna SQL Server-standardmappen för data som angetts ovan.
9. PowerShell-skriptet rapporterar start- och sluttider. När alla bulk import slutförd rapporteras sluttiden. Kontrollera loggen målmappen för att kontrollera att stora importerar har installerats, dvs, inga fel rapporteras i loggen målmappen.
10. Din databas är nu redo för utforskning, funktionsframställning och andra åtgärder som du vill. Eftersom tabellerna partitioneras enligt den **upphämtning\_datetime** fältet frågor som omfattar **upphämtning\_datetime** villkor i den **där** satsen drar nytta av partitionsschemat.
11. I **SQL Server Management Studio**, utforska exempelskript som angivna **exempel\_queries.sql**. Om du vill köra någon av exempelfrågor Markera frågerader och klicka på **! Köra** i verktygsfältet.
12. NYC Taxi och RETUR-data har lästs in i två olika tabeller. För att förbättra kopplingsåtgärder, rekommenderar vi starkt att indexera tabellerna. Exempelskriptet **skapa\_partitionerade\_index.sql** skapar partitionerade index i sammansatta join-nyckeln **medallion, hacka\_licens och upphämtning\_ datetime**.

## <a name="dbexplore"></a>Datagranskning och de funktioner i SQLServer
I det här avsnittet ska vi köra genereringen av data utforskning och funktionen genom att köra SQL-frågor direkt i den **SQL Server Management Studio** med hjälp av SQL Server-databasen skapades tidigare. Ett exempelskript som heter **exempel\_queries.sql** har angetts i den **exempelskript** mapp. Ändra skriptet om du vill ändra namnet på databasen, om det skiljer sig från standard: **TaxiNYC**.

I den här övningen ska du:

* Ansluta till **SQL Server Management Studio** med hjälp av antingen Windows-autentisering eller SQL-autentisering och SQL-inloggningsnamn och lösenord.
* Utforska data distributioner av ett fåtal fält i olika tidsfönster.
* Undersök datakvaliteten fält för longitud och latitud.
* Generera binära och inom klassificeringsetiketter baserat på den **tips\_belopp**.
* Skapa funktioner och beräkning/jämför resans avstånd.
* Koppla två tabeller och extrahera ett slumpmässigt urval som ska användas för att skapa modeller.

När du är redo att gå vidare till Azure Machine Learning kan du antingen:  

1. Spara den slutliga SQL-frågan för att extrahera och hämtar exempel från data och kopiera och klistra in frågan direkt i en [importdata] [ import-data] modul i Azure Machine Learning, eller
2. Spara den provade och bakåtkompilerade data som du planerar att använda för att skapa i en ny databas-modellen tabellen och använder den nya tabellen i den [importdata] [ import-data] modul i Azure Machine Learning.

I det här avsnittet kommer vi att spara den sista frågan för att extrahera och hämtar exempel från data. Den andra metoden som visas i den [Datagranskning och funktionen Engineering i IPython Notebook](#ipnb) avsnittet.

En snabb kontroll av antalet rader och kolumner i tabellerna fylls i tidigare med parallell massimport

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Utforskning: Resans distribution enligt medallion
Det här exemplet identifierar medallion (taxi-nummer) med fler än 100 kommunikation inom en viss tidsperiod. Frågan skulle ha nytta av partitionerade tabellåtkomst eftersom det villkor som partitionsschemat för **upphämtning\_datetime**. Fråga hela datauppsättningen innebär även att använda partitionerade tabellen och/eller index-genomsökning.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Utforskning: Resans distribution enligt medallion och hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Data utvärdering: Kontrollera poster med felaktig longitud och/eller latitud
Det här exemplet undersöker om något av fälten för longitud och/eller latitud antingen innehåller ett ogiltigt värde (radian grader bör vara mellan-90 och 90), eller så har (0, 0) koordinater.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Utforskning: Lutad vs. Inte lutad och RETUR-distribution
Det här exemplet hittar antalet turer som har lutad jämfört med lutad inte i en given tidpunkt tidsperiod (eller i hela datauppsättningen om som täcker hela året). Den här distributionen återspeglar distribution av binära etiketter ska användas senare för binär klassificering modellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Utforskning: Tips klass/intervall Distribution
Det här exemplet beräknar fördelningen av tip-intervall i en viss tidsperiod (eller i hela datauppsättningen om som täcker hela året). Det här är distributionen av klasserna etiketten som ska användas senare för multiklass-baserad klassificering modellering.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Utforskning: Beräkna och jämföra resans avstånd
Det här exemplet konverterar hämtning och dropoff longitud och latitud till SQL geografiskt område återställningspunkter, beräknar avståndet resa med hjälp av SQL geografi punkter skillnaden och returnerar ett slumpmässigt urval av resultaten för jämförelse. I exempel begränsar resultaten till giltiga koordinater bara med data quality utvärdering fråga beskrivs tidigare.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Funktionsframställning i SQL-frågor
Etikett generation och geografi konvertering utforskning frågorna kan också användas för att generera etiketter/funktioner genom att ta bort fler är på gång. Extrafunktioner engineering SQL-exempel finns i den [Datagranskning och funktionen Engineering i IPython Notebook](#ipnb) avsnittet. Det är mer effektivt att köra funktionen generation frågor i hela datauppsättningen eller en stor del av den med hjälp av SQL-frågor som körs direkt på SQL Server-databasinstans. Frågorna som kan köras i **SQL Server Management Studio**, IPython Notebook eller alla verktyg/utvecklingsmiljöer som har åtkomst till databasen, lokalt eller fjärranslutet.

#### <a name="preparing-data-for-model-building"></a>Förbereda Data för att skapa modellen
Följande fråga kopplingar i **nyctaxi\_resans** och **nyctaxi\_avgiften** tabeller, genererar en binär klassificeringsetikett **lutad**, ett flera Klassificeringsetiketten **tips\_klass**, och extraherar ett slumpmässigt urval 1% från fullständig domänansluten datauppsättningen. Den här frågan kan kopieras och klistras in direkt i den [Azure Machine Learning Studio](https://studio.azureml.net) [importdata] [ import-data] -modulen för direkta datainmatning från SQL Server-databasen instans i Azure. Frågan utesluter poster med fel (0, 0) koordinater.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Datagranskning och de funktioner i IPython Notebook
I det här avsnittet ska vi köra datagranskning och funktionen generation med hjälp av både Python och SQL-frågor mot SQL Server-databasen som skapades tidigare. Ett exempel IPython notebook med namnet **machine-Learning-data-science-process-sql-story.ipynb** har angetts i den **IPython Exempelanteckningsböcker** mapp. Den här anteckningsboken är även tillgängligt i [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Rekommenderade sekvensen när du arbetar med stordata är följande:

* Läs i ett litet antal data till en ram för data i minnet.
* Utför vissa visualiseringar och explorations med exempeldata.
* Experimentera med funktionsframställning med exempeldata.
* För större datagranskning, datamanipulering av och funktioner du Använd Python för att skicka SQL-frågor direkt mot SQL Server-databas i Azure-VM.
* Bestäm urvalsstorlek ska användas för att skapa Azure Machine Learning-modell.

När du är klar för att gå vidare till Azure Machine Learning kan du antingen:  

1. Spara den slutliga SQL-frågan för att extrahera och hämtar exempel från data och kopiera och klistra in frågan direkt i en [importdata] [ import-data] modul i Azure Machine Learning. Den här metoden visas i den [bygga modeller i Azure Machine Learning](#mlmodel) avsnittet.    
2. Spara provade och bakåtkompilerade data som du planerar att använda för att skapa med en ny databastabell-modellen och sedan använda den nya tabellen i den [importdata] [ import-data] modulen.

Här följer några datagranskning, visualisering av data och funktionen tekniska exempel. Fler exempel finns i exemplet SQL IPython notebook i den **IPython Exempelanteckningsböcker** mapp.

#### <a name="initialize-database-credentials"></a>Initiera Databasautentiseringsuppgifter
Initiera dina anslutningsinställningar i följande variabler:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Skapa databasanslutning
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Rapporten antalet rader och kolumner i tabellen nyctaxi_trip
    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

* Totalt antal rader = 173179759  
* Totalt antal kolumner = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Läs i ett litet datasampel från SQL Server-databas
    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tid för att läsa exempeltabell är 6.492000 sekunder  
Antal rader och kolumner hämtas = (84952, 21)

#### <a name="descriptive-statistics"></a>Beskrivande statistik
Är nu redo att utforska exempeldata. Vi börjar med att titta på beskrivande statistik för den **resans\_avståndet** (eller någon annan) nyckelfälten:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualisering: Exempel på diagram
Därefter tittar vi på Låddiagram för resans avståndet till visualisera quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Rita #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisering: Exempel för distribution-diagram
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Rita #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisering: -Fältet och rad områden
I det här exemplet vi bin resans avståndet till fem lagerplatser och visualisera datagrupperingen resultat.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan rita ovan bin-distribution i ett fält eller rad diagram enligt nedan

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Rita #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Rita #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualisering: Spridningsdiagrammet exempel
Vi visar spridningsdiagrammet mellan **resans\_tid\_i\_sekunder** och **resans\_avståndet** att se om det finns några korrelation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Rita #6][6]

På samma sätt kan vi Kontrollera relationen mellan **rate\_kod** och **resans\_avståndet**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Rita #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Icke samlar Data i SQL
När du förbereder data för att skapa med modellen [Azure Machine Learning Studio](https://studio.azureml.net), kan du antingen välja på den **SQL-fråga som ska användas direkt i modulen importera Data** eller spara bakåtkompilerade och samplade data i en ny tabellen, vilket du kan använda i den [importera Data] [ import-data] modulen med en enkel **Välj * från < din\_nya\_tabell\_namn >** .

I det här avsnittet ska vi skapa en ny tabell för att lagra data provade och bakåtkompilerade. Ett exempel på en direkt SQL-fråga för att bygga modellen finns i den [Datagranskning och funktionen Engineering i SQL Server](#dbexplore) avsnittet.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Skapa ett exempel på tabellen och fylla i med 1% av de kopplade tabellerna. Ta bort den första tabellen om den finns.
I det här avsnittet ska vi ansluta tabellerna **nyctaxi\_resans** och **nyctaxi\_avgiften**, extrahera ett slumpmässigt urval 1% och bevara exempeldata i en ny tabellnamn  **nyctaxi\_en\_procent**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Datagranskning med SQL-frågor i IPython Notebook
I det här avsnittet ska utforska vi data distributioner med hjälp av 1% samplas data som sparas i den nya tabellen som vi skapade ovan. Observera att liknande explorations kan utföras med hjälp av de ursprungliga tabellerna, vid behov kan **TABLESAMPLE** att begränsa att utforska exemplet eller genom att begränsa resultaten till en given tidpunkt period med den **upphämtning\_datetime** partitionerar, enligt beskrivningen i den [Datagranskning och funktionen Engineering i SQL Server](#dbexplore) avsnittet.

#### <a name="exploration-daily-distribution-of-trips"></a>Utforskning: Dagliga distribution av kommunikation
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Utforskning: Resans distribution per medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Funktionen Generation med SQL-frågor i IPython Notebook
I det här avsnittet vi kommer att generera nya etiketter och funktioner direkt med hjälp av SQL-frågor, körs på 1% exempeltabell vi skapade i föregående avsnitt.

#### <a name="label-generation-generate-class-labels"></a>Etikettgenerering: Generera klassen etiketter
I följande exempel skapar vi två uppsättningar med etiketter ska användas för modellering:

1. Binär klass etiketter **lutad** (att förutsäga om ett tips ges)
2. Inom etiketter **tips\_klass** (förutsäga tips bin eller intervall)
   
        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''
   
        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()
   
        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''
   
        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funktionsframställning: Antal funktioner för Kategoriska kolumner
Det här exemplet omvandlar ett kategoriskt fält till ett numeriskt fält genom att ersätta varje kategori med alla dess förekomster i data.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funktionsframställning: Bin-funktioner för numeriska kolumner
Det här exemplet omvandlar en kontinuerlig numeriskt fält till förinställda kategori intervall, d.v.s. transformeringen numeriskt fält i ett kategoriska fält.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funktionsframställning: Extrahera plats funktioner från Decimal latitud/longitud
Det här exemplet eliminerar formatet för ett latitud/longitud eller fält i flera regioner fält av olika kornighet som, land, stad, stad, blockera, osv. Observera att de nya geo-fält inte är mappade till faktiska platser. Information om mappning geocode platser finns i [Bing Maps REST-tjänster](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Kontrollera den slutliga utformning av tabellen trädmodell
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Vi är nu redo att gå vidare till modellskapandet och distribution av modeller i [Azure Machine Learning](https://studio.azureml.net). Data är redo för någon av de förutsägelse problem som konstaterats tidigare, nämligen:

1. Binär klassificering: För att förutsäga om ett tips har betalat för en resa.
2. Multiklass-baserad klassificering: Att förutsäga vilka tips betalt enligt de tidigare definierade klasserna.
3. Regression uppgift: Att förutsäga mängden tips som har betalat för en resa.  

## <a name="mlmodel"></a>Skapa modeller i Azure Machine Learning
Logga in på din Azure Machine Learning-arbetsyta om du vill börja modellering. Om du inte har skapat en machine learning-arbetsytan finns i [skapar en Azure Machine Learning-arbetsyta](../studio/create-workspace.md).

1. Kom igång med Azure Machine Learning, se [vad är Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net).
3. Sidan Studio tillhandahåller en mängd information, videor, självstudier, länkar till moduler-referenser och andra resurser. Mer information om Azure Machine Learning finns det [Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

En typisk träningsexperiment består av följande:

1. Skapa en **+ ny** experimentera.
2. Hämta data till Azure Machine Learning.
3. Förbearbeta, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela upp data i utbildning / / valideringstestning datauppsättningar (eller har separata datauppsättningar för var och en).
6. Välj en eller flera maskininlärningsalgoritmer beroende på träningsproblemet att lösa. T.ex. binär klassificering, multiklass-baserad klassificering, regression.
7. Skapa en eller flera modeller med hjälp av datauppsättning för träning.
8. Bedöma verifiering datauppsättningen med hjälp av den tränade modeller.
9. Utvärdera modeller för att beräkna relevanta mått för träningsproblemet.
10. Bra finjustera modell(er) och välja modellen med bäst att distribuera.

I den här övningen har vi redan utforskas och utformat data i SQL Server och valt urvalsstorlek att mata in i Azure Machine Learning. Skapa en eller flera av förutsägelsemodeller som vi har valt:

1. Hämta data till Azure Machine Learning med hjälp av den [importdata] [ import-data] modulen, som är tillgängliga i den **Data indata och utdata** avsnittet. Mer information finns i den [importdata] [ import-data] referenssida för modulen.
   
    ![Azure Machine Learning importera Data][17]
2. Välj **Azure SQL Database** som den **datakälla** i den **egenskaper** panelen.
3. Ange namnet på databasen DNS i den **Databasservernamnet** fält. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange den **databasnamn** i motsvarande fält.
5. Ange den **SQL-användarnamnet** i den **Server användarkontonamn**, och **lösenord** i den **Server lösenord**.
7. I den **databasfråga** redigera textområde, klistrar in frågan som extraherar de nödvändiga databasfält (inklusive eventuella beräknade fält, till exempel etiketter) och ned samplar data till önskad urvalsstorlek.

Ett exempel på en binär klassificering experiment som läser data direkt från SQL Server-databasen är i bilden nedan. Liknande experiment kan konstrueras för multiklass-baserad klassificering och regressionsproblem.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> För modellering av finansdata extrahering och samlar fråga exempel som i föregående avsnitt **alla etiketter för tre modellering övningar som ingår i frågan**. Ett viktigt steg i var och en av modellering övningarna för (obligatoriskt) är att **undanta** onödiga etiketter för de andra två problemen och andra **rikta läckage av**. För t.ex. när du använder binär klassificering, använda etiketten **lutad** och utelämna fälten **tips\_klass**, **tips\_belopp**, och **totala\_belopp**. Dessa är målet läckage eftersom de innebär tipset betald.
> 
> Om du vill exkludera onödiga kolumner och/eller rikta läckage av, kan du använda den [Välj kolumner i datauppsättning] [ select-columns] modul eller [redigera Metadata][edit-metadata]. Mer information finns i [Välj kolumner i datauppsättning] [ select-columns] och [redigera Metadata] [ edit-metadata] referera till sidorna.
> 
> 

## <a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När din modell är klar kan distribuera du enkelt den som en webbtjänst direkt från experimentet. Mer information om hur du distribuerar Azure Machine Learning-webbtjänster finns i [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).

Om du vill distribuera en ny webbtjänst, måste du:

1. Skapa en arbetsflödesbaserad experiment.
2. Distribuera webbtjänsten.

Skapa en arbetsflödesbaserad experiment från en **slutfört** utbildning experiment, klickar du på **skapa bedömning EXPERIMENTERA** i lägre Åtgärdsfältet.

![Azure-bedömning][18]

Azure Machine Learning försöker skapa en arbetsflödesbaserad experiment som bygger på komponenterna för träningsexperimentet. I synnerhet att:

1. Spara den tränade modellen och ta bort modellen utbildningsmoduler.
2. Identifiera en logisk **indataporten** som motsvarar det förväntade indataschema.
3. Identifiera en logisk **utgående port** som motsvarar det förväntade web service utdata-schemat.

När bedömnings experimentet har skapats kan du granska den och justera efter behov. En typisk justering är att ersätta datauppsättningen för indata och/eller fråga med en vilket utesluter Etikettfälten som dessa inte är tillgänglig när tjänsten anropas. Det är också en bra idé att minska storleken på datauppsättningen för indata och/eller fråga till några poster, bara tillräckligt för att indikera inmatningsschemat. För den utgående porten, är det vanligt att exkludera alla inmatningsfält och bara innehålla den **poängsatta etiketter** och **poängsätts sannolikhet** i utdata med den [Välj kolumner i datauppsättning] [ select-columns] modulen.

Ett sampel bedömning experimentet är i bilden nedan. När du är klar att distribuera klickar du på den **publicera WEBBTJÄNSTEN** knappen i lägre Åtgärdsfältet.

![Publicera Azure Machine Learning][11]

Om du vill tar och sammanfattar, i den här genomgången i självstudien har du skapat en Azure data science-miljö har samarbetat med en stor offentlig datauppsättning hela vägen från datainsamling modellera träning och distributionen av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licensinformation
Det här exemplet genomgång och dess tillhörande skript och IPython notebook(s) som delas av Microsoft under MIT-licensen. Kontrollera filen LICENSE.txt i katalogen på exempelkoden på GitHub för mer information.

### <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi kommunikation hämtningssidan](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxitransport Resedata av Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi och Limousine kommissionen forskning och statistik](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
