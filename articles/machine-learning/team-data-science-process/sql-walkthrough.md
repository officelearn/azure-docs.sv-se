---
title: "Skapa och distribuera en maskininlärningsmodell som använder SQL Server på en virtuell dator i Azure | Microsoft Docs"
description: "Processen för avancerade analyser och teknik i åtgärd"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6066b083-262c-4453-a712-a5c05acc3df8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: fashah;bradsev
ms.openlocfilehash: d42377a55b1decc0918932b3ecc13cf575f934a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Team vetenskap av data i praktiken: använder SQL Server
I den här kursen går igenom processen att skapa och distribuera en maskininlärningsmodell med hjälp av SQL Server och ett offentligt tillgängliga dataset – [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset. Förfarandet som följer en standard datavetenskap arbetsflödet: infognings- och utforska data, tekniker funktioner för att underlätta learning, och sedan skapa och distribuera en modell.

## <a name="dataset"></a>NYC Taxi resor Dataset-beskrivning
NYC Taxi resa data är cirka 20GB komprimerat CSV-filer (~ 48GB okomprimerade), som består av fler än 173 miljoner enskilda resor och priser betalat för varje resa. Hämtning och Samlingsbibliotek plats och tid, anonymiserade hackare (drivrutin) licensnummer och medallion (taxi's unikt id) nummer innehåller varje resa-post. Data omfattar alla resor år 2013 och finns i följande två datauppsättningar för varje månad:

1. Trip_data CSV innehåller resa information, till exempel antal passagerare, hämtning och dropoff punkter, resa varaktighet och resa längd. Här följer några Exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Trip_fare CSV innehåller information om avgiften betalat för varje förflyttning, till exempel betalningssätt, avgiften belopp, tillägg och skatter, tips och vägtullar, och det totala betald. Här följer några Exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Unik nyckel för att ansluta till resa\_data och resa\_avgiften består av fälten: medallion hackare\_licensen och hämtning\_datetime.

## <a name="mltasks"></a>Exempel på förutsägelse uppgifter
Vi kommer att formulera tre förutsägelse problem utifrån den *tips\_belopp*, nämligen:

1. Binär klassificering: förutsäga oavsett betalats ett tips för en resa i d.v.s. en *tips\_belopp* som är större än 0 är ett positivt exempel, när en *tips\_belopp* $0 är en negativt exempel.
2. Multiklass-baserad klassificering: att förutsäga intervallet för betald för resan-tips. Vi dela den *tips\_belopp* i fem lagerplatser eller klasser:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
3. Regression uppgiften: att förutsäga mängden tips för en resa.  

## <a name="setup"></a>Ställa in Azure datavetenskap miljö för avancerade analyser
Som du kan se den [planera din miljö](plan-your-environment.md) guide, det finns flera alternativ för att arbeta med NYC Taxi resor datauppsättningen i Azure:

* Arbeta med data i Azure BLOB sedan modellen i Azure Machine Learning
* Läsa in data i en SQL Server-databasen och sedan modellen i Azure Machine Learning

I den här kursen visar vi parallella massimport av data till en SQL Server datagranskning, funktion tekniker och ned provtagning med hjälp av SQL Server Management Studio som använder IPython bärbar dator. [Exempel på skript](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) och [IPython anteckningsböcker](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) delas i GitHub. Det finns också en bärbar dator IPython exempel att arbeta med data i Azure BLOB på samma plats.

Att ställa in Azure datavetenskap miljön:

1. [Skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md)
2. [Skapa en arbetsyta för Azure Machine Learning](../studio/create-workspace.md)
3. [Etablera en virtuell dator med datavetenskap](../data-science-virtual-machine/setup-sql-server-virtual-machine.md), som innehåller en SQL-Server och en bärbar dator IPython-server.
   
   > [!NOTE]
   > Exempel på skript och IPython bärbara datorer kommer att hämtas till den virtuella datorn datavetenskap under installationen. När skriptet VM efter installationen är klar blir exemplen i den Virtuella datorns dokumentbibliotek:  
   > 
   > * Exempel skript:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Exempel IPython bärbara datorer:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   där `<user_name>` är den Virtuella datorns Windows-inloggningsnamn. Vi kommer att referera till exempel mappar som **exempelskript** och **exempel IPython anteckningsböcker**.
   > 
   > 

Baserat på datamängden storlek, datakällplats och valda Azure målmiljön, det här scenariot liknar [scenariot \#5: stora datauppsättning i lokala filer, mål SQL Server i Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Hämta Data från offentliga källa
Att hämta den [NYC Taxi resor](http://www.andresmh.com/nyctaxitrips/) dataset från dess offentlig plats, kan du använda någon av metoderna som beskrivs i [flytta Data till och från Azure Blob Storage](move-azure-blob.md) att kopiera data till din nya virtuella datorn.

Att kopiera data med hjälp av AzCopy:

1. Logga in på den virtuella datorn (VM)
2. Skapa en ny katalog i datadisk för den virtuella datorn (Obs: Använd inte tillfällig disken som innehåller den virtuella datorn som en datadisk).
3. Kör följande Azcopy kommandoraden ersätta < path_to_data_folder > med datamappen som skapats i (2) i ett kommandotolksfönster:
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    När AzCopy är klar totalt av 24 zippade CSV-filer (12 för resa\_data och 12 för resa\_avgiften) ska vara i datamappen.
4. Packa upp de hämtade filerna. Kom ihåg vilken mapp där de okomprimerade filerna finns. Den här mappen kommer kallas den < sökväg\_till\_data\_filer\>.

## <a name="dbload"></a>Massinläsning importera Data till SQL Server-databas
Prestanda för inläsning/överföra stora mängder data till en SQL-databas och efterföljande frågor kan förbättras genom att använda *partitionerade tabeller och vyer*. I det här avsnittet kommer vi följer instruktionerna i [parallella Bulk Import med hjälp av SQL-Partition datatabeller](parallel-load-sql-partitioned-tables.md) att skapa en ny databas och läsa in data i partitionerade tabeller parallellt.

1. Logga in den virtuella datorn, starta **SQL Server Management Studio**.
2. Ansluta med Windows-autentisering.
   
    ![SSMS Anslut][12]
3. Om du har ännu inte ändra SQL Server-autentiseringsläget och skapa en ny SQL-inloggning, öppna skriptfilen med namnet **ändra\_auth.sql** i den **exempelskript** mapp. Ändra standardanvändarnamn och lösenord. Klicka på **! Köra** i verktygsfältet för att köra skriptet.
   
    ![Kör skript][13]
4. Kontrollera och/eller ändra SQL Server standard databasen och loggfilerna mapparna så som nyligen skapats databaser kommer att lagras i en datadisk. SQL Server-VM-avbildning som är optimerad för datawarehousing belastningar är förkonfigurerad med data och loggfilen diskar. Om den virtuella datorn inte innehöll en datadisk och du har lagt till nya virtuella hårddiskar under installationen VM, ändra standardmappar på följande sätt:
   
   * Högerklicka på SQL Server-namnet i den vänstra rutan och klicka på **egenskaper**.
     
       ![Egenskaper för SQL Server][14]
   * Välj **databasinställningar** från den **Välj en sida** listan till vänster.
   * Kontrollera och/eller ändra den **databasen standardplatserna** till den **datadisk** platser för ditt val. Detta är där nya databaser finns om skapas med standardinställningar för platsen.
     
       ![Standardvärdet för SQL-databasen][15]  
5. Öppna exempelskript för att skapa en ny databas och en uppsättning filgrupper för partitionerade tabeller, **skapa\_db\_default.sql**. Skriptet skapar en ny databas med namnet **TaxiNYC** och 12 filgrupper på standardplatsen för data. Varje filgrupp innehåller en månad resa\_data och resa\_färdavgiften data. Ändra namnet på databasen, om så önskas. Klicka på **! Köra** att köra skriptet.
6. Skapa sedan två partitionstabeller, en för resan\_data och en för resan\_avgiften. Öppna exempelskriptet **skapa\_partitionerade\_table.sql**, som:
   
   * Skapa en partitionsfunktion om du vill dela data per månad.
   * Skapa ett partitionsschema mappa varje månad data till en annan filgrupp.
   * Skapa två partitionerade tabeller som är mappad till partitionsschemat: **nyctaxi\_resa** innehåller resan\_data och **nyctaxi\_avgiften** innehåller resan\_färdavgiften data.
     
     Klicka på **! Köra** att köra skriptet och skapa partitionerade tabeller.
7. I den **exempelskript** mapp, det finns två exempel PowerShell-skript som demonstrerar parallella bulk import av data till SQL Server-tabeller.
   
   * **BCP\_parallella\_generic.ps1** är ett Allmänt skript till parallella bulk importera data till en tabell. Ändra det här skriptet för att ange indata- och variabler som anges i kommentarrader i skriptet.
   * **BCP\_parallella\_nyctaxi.ps1** är en förkonfigurerad version av skript och kan användas till att läsa in båda tabellerna för NYC Taxi resor data.  
8. Högerklicka på den **bcp\_parallella\_nyctaxi.ps1** skriptets namn och klicka på **redigera** att öppna den i PowerShell. Granska de förinställda variablerna och ändra enligt din valda databasens namn, mappen inkommande data, log målmappen och sökvägar till exempel format-filer **nyctaxi_trip.xml** och **nyctaxi\_fare.xml** (anges i den **exempelskript** mapp).
   
    ![Massinläsning importera Data][16]
   
    Du kan också markera autentiseringsläget, standardvärdet är Windows-autentisering. Klicka på den gröna pilen i verktygsfältet för att köra. Skriptet startas 24 import massåtgärder parallell 12 för varje partitionerade tabellen. Du kan övervaka data import förloppet genom att öppna SQL Server data standardmappen som ovan.
9. PowerShell-skriptet rapporterar start- och sluttid. När alla Massredigera importen slutförts rapporteras sluttiden. Kontrollera loggen målmappen för att kontrollera att flesta importerar lyckades, dvs, inga fel rapporteras i målmappen för loggen.
10. Databasen är nu redo för undersökning, funktionen tekniker och andra åtgärder efter behov. Eftersom tabellerna partitioneras enligt den **hämtning\_datetime** fältet frågor som omfattar **hämtning\_datetime** villkor i den **där** satsen drar nytta av partitionsschemat.
11. I **SQL Server Management Studio**, utforska det tillhandahållna exempelskriptet **exempel\_queries.sql**. Om du vill köra någon av exempelfrågor fokusera på linjerna i fråga och klicka sedan på **! Köra** i verktygsfältet.
12. NYC Taxi resor data har lästs in i två olika tabeller. För att förbättra kopplingsåtgärder, rekommenderas att indexera tabellerna. Exempelskriptet **skapa\_partitionerade\_index.sql** skapar partitionerade index i sammansatta koppling nyckeln **medallion hackare\_licens och hämtning\_ datetime**.

## <a name="dbexplore"></a>Datagranskning och funktionen tekniker i SQLServer
I det här avsnittet ska vi utföra data från kartläggning av naturresurser och funktionen generation genom att köra SQL-frågor direkt i den **SQL Server Management Studio** använda SQL Server-databasen som skapats tidigare. Ett exempelskript som heter **exempel\_queries.sql** har angetts i den **exempelskript** mapp. Ändra skriptet för att ändra namnet på databasen, om det skiljer sig från standard: **TaxiNYC**.

I den här övningen ska du:

* Ansluta till **SQL Server Management Studio** med hjälp av antingen Windows-autentisering eller SQL-autentisering och SQL-inloggningsnamn och lösenord.
* Utforska data distributioner av ett fåtal fält i olika tidsfönster.
* Undersök data quality longitud och latitud fält.
* Generera binära och multiklass-baserad klassificeringsetiketter baserat på de **tips\_belopp**.
* Generera funktioner och beräkning eller jämförelse resa avstånd.
* Koppla två tabeller och extrahera ett slumpmässigt prov som ska användas för att bygga modeller.

När du är redo att fortsätta till Azure Machine Learning, kan du antingen:  

1. Spara slutliga SQL-frågan om du vill extrahera den exempeldata och kopiera / klistra in frågan direkt i en [importera Data] [ import-data] modul i Azure Machine Learning, eller
2. Spara den provade och bakåtkompilerade data som du planerar att använda för modellen bygga i en ny databas tabell och använda den nya tabellen i den [importera Data] [ import-data] modul i Azure Machine Learning.

I det här avsnittet sparar vi sista frågan för att extrahera och den exempeldata. Den andra metoden visas i den [Datagranskning och funktionen Engineering i IPython anteckningsbok](#ipnb) avsnitt.

En snabb kontroll av antalet rader och kolumner i tabellerna fyllts i tidigare med hjälp av parallella massimport

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Undersökning: Resa distribution av medallion
Det här exemplet identifierar medallion (taxi numbers) med mer än 100 resor inom en viss tidsperiod. Frågan skulle dra nytta av partitionerad tabellåtkomst eftersom den är villkorad av partitionsschemat för **hämtning\_datetime**. Frågar den fullständiga datauppsättningen blir också användning av partitionerad tabell och/eller index-sökning.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Undersökning: Resa distribution av medallion och hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Data utvärdering: Verifiera poster med felaktigt longituden eller latituden
Det här exemplet undersöker om något av fälten longituden eller latituden antingen innehåller ett ogiltigt värde (radian grader ska vara mellan-90 och 90), eller ha (0, 0) koordinater.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Undersökning: Lutad jämfört med Inte lutad resor distribution
Det här exemplet hittar antalet turer som har lutad kontra lutad inte i en given tidpunkt tidsperiod (eller i den fullständiga datauppsättningen om täcker hela året). Den här distributionen visar binära etikett distributionen senare användas för binär klassificering modellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Undersökning: Tips klass-intervallet Distribution
Det här exemplet beräknar fördelningen av tips intervall i en viss tidsperiod (eller den fullständiga datauppsättningen om täcker hela året). Detta är distributionen av klasserna etiketten som ska användas senare för multiklass-baserad klassificering modellering.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Undersökning: Beräkna och jämföra resa avstånd
Det här exemplet konverterar hämtning och Samlingsbibliotek longitud och latitud till SQL geografi pekar beräknar resa avståndet med SQL geografi punkter skillnaden och returnerar ett slumpvist urval av resultaten för jämförelse. I exempel begränsar resultat till giltiga koordinater endast med data quality assessment frågan omfattas tidigare.

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

#### <a name="feature-engineering-in-sql-queries"></a>Funktionen teknikerna i SQL-frågor
Etikett generation och geografi konvertering utforskning frågorna kan också användas för att generera etiketter och funktioner genom att ta bort cykliska. Ytterligare funktionen engineering SQL-exempel finns i den [Datagranskning och funktionen Engineering i IPython anteckningsbok](#ipnb) avsnitt. Det är mer effektivt att köra funktionen generation frågor i den fullständiga datauppsättningen eller en stor del av den med hjälp av SQL-frågor som körs direkt på den SQL Server-databasinstansen. Frågor kan köras i **SQL Server Management Studio**, IPython bärbar dator eller en verktyget/utvecklingsmiljö som har åtkomst till databasen lokalt eller fjärranslutet.

#### <a name="preparing-data-for-model-building"></a>Förbereder Data för Modellskapandet
Följande fråga kopplingar i **nyctaxi\_resa** och **nyctaxi\_avgiften** tabeller, genererar en binär klassificering etikett **lutad**, flera klassen klassificering etikett **tips\_klassen**, och extraherar ett slumpmässigt prov 1% från den kopplade fullständiga datauppsättningen. Den här frågan kan kopieras och klistras in direkt i den [Azure Machine Learning Studio](https://studio.azureml.net) [importera Data] [ import-data] -modulen för direkt datapåfyllning från SQL Server-databasen instans i Azure. Frågan utesluter poster med fel (0, 0) koordinater.

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


## <a name="ipnb"></a>Datagranskning och funktionen tekniker i IPython anteckningsbok
I det här avsnittet ska vi utföra datagranskning och funktion som genereras med hjälp av Python- och SQL-frågor mot SQL Server-databasen som skapats tidigare. En exempel IPython bärbar dator med namnet **machine-Learning-data-science-process-sql-story.ipynb** har angetts i den **exempel IPython anteckningsböcker** mapp. Den här anteckningsboken finns också på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Den rekommenderade sekvensen när du arbetar med stordata är följande:

* Läs i ett litet antal data till en ram i minnet data.
* Utföra vissa visualiseringar och explorations med hjälp av samplade data.
* Experimentera med funktionen tekniker med hjälp av samplade data.
* För större datagranskning, databehandling och funktionen tekniker Använd Python för att utfärda SQL-frågor direkt mot SQL Server-databas i Azure-VM.
* Bestäm provtagning för Azure Machine Learning modellskapandet.

När det är dags att gå vidare till Azure Machine Learning, kan du antingen:  

1. Spara slutliga SQL-frågan om du vill extrahera den exempeldata och kopiera / klistra in frågan direkt i en [importera Data] [ import-data] modul i Azure Machine Learning. Den här metoden visas i den [bygga modeller i Azure Machine Learning](#mlmodel) avsnitt.    
2. Spara provade och bakåtkompilerade data som du planerar att använda för modellen bygga i en ny databastabell och sedan använda den nya tabellen i den [importera Data] [ import-data] modul.

Följande är några datagranskning datavisualisering och funktionen engineering exempel. Fler exempel finns i exemplet SQL IPython anteckningsboken i den **exempel IPython anteckningsböcker** mapp.

#### <a name="initialize-database-credentials"></a>Initiera Databasautentiseringsuppgifter
Initiera anslutningsinställningarna databasen i följande variabler:

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
Är nu redo att utforska samplade data. Vi börjar med att titta på beskrivande statistik för den **resa\_avstånd** (eller andra) nyckelfälten:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualiseringen: Exempel på ritytans
Vi titta på Låddiagram för resa avståndet visualisera quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Rita #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualiseringen: Distribution ritytans exempel
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Rita #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisering: Fältet och rad områden
I det här exemplet vi bin resa avståndet i fem lagerplatser och visualisera binning resultat.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan ritas ovan bin-distribution i ett fält eller rad ritytans enligt nedan

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Rita #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Rita #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualiseringen: Scatterplot exempel
Visar vi punktdiagram ritytans mellan **resa\_tid\_i\_sek** och **resa\_avstånd** om det finns några korrelation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Rita #6][6]

På liknande sätt kan vi kontrollerar relationen mellan **hastighet\_kod** och **resa\_avstånd**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Rita #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Underordnad Datasampling i SQL
När du förbereder data för modellen bygga i [Azure Machine Learning Studio](https://studio.azureml.net), kan du antingen välja på den **SQL-frågan ska använda direkt i modulen importera Data** eller spara bakåtkompilerade och samplade data i en ny tabellen som du kan använda i den [importera Data] [ import-data] modulen med en enkel **Välj * FROM < din\_nya\_tabell\_namn >** .

I det här avsnittet skapar vi en ny tabell för att lagra data provade och bakåtkompilerade. Ett exempel på en direkt SQL-fråga för skapande av modellen som har angetts i den [Datagranskning och funktionen Engineering i SQL Server](#dbexplore) avsnitt.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Skapa ett exempel på en tabell och fylla med 1% av de kopplade tabellerna. Ta bort den första tabellen om den finns.
I det här avsnittet vi koppla tabellerna **nyctaxi\_resa** och **nyctaxi\_avgiften**Extrahera ett slumpmässigt prov 1% och bevara samplade data i ett nytt tabellnamn  **nyctaxi\_en\_procent**:

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Datagranskning med hjälp av SQL-frågor i IPython anteckningsbok
I det här avsnittet förklarar vi data distributioner med hjälp av 1% provtagning data som sparas i den nya tabellen som vi skapade ovan. Observera att liknande explorations kan utföras med hjälp av de ursprungliga tabeller, vid behov kan **TABLESAMPLE** att begränsa utforskning exempel eller genom att begränsa resultaten till en given tidpunkt period med hjälp av den **hämtning\_datetime** partitioner, enligt beskrivningen i den [Datagranskning och funktionen Engineering i SQL Server](#dbexplore) avsnitt.

#### <a name="exploration-daily-distribution-of-trips"></a>Undersökning: Dagliga distribution av resor
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Undersökning: Resa distribution per medallion
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Funktionen Generation med hjälp av SQL-frågor i IPython anteckningsbok
I det här avsnittet kommer vi att generera nya etiketter och funktioner som direkt med SQL-frågor körs på 1% exempeltabell vi skapade i föregående avsnitt.

#### <a name="label-generation-generate-class-labels"></a>Etikettgenerering: Skapa klassen etiketter
I följande exempel skapar vi två uppsättningar med etiketter ska användas för modellering:

1. Binär klassen etiketter **lutad** (förutsäga om ett tips ges)
2. Multiklass-baserad etiketter **tips\_klassen** (förutsäga tips bin eller intervall)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funktionen tekniker: Antal funktioner för Kategoriska kolumner
Det här exemplet transformerar ett kategoriska fält i ett numeriskt fält genom att ersätta varje kategori med antalet dess förekomster i data.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funktionen tekniker: Bin funktioner för numeriska kolumner
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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funktionen tekniker: Extrahera plats funktioner från Decimal latitud/longitud
Det här exemplet uppdelad decimal-representation av ett latitud och longitud fält till flera region fält i olika detaljnivå som, land, ort, staden, block och så vidare. Observera att nya geo-fält inte har mappats till faktiska platser. Information om geocode kartläggning finns [Bing Maps REST Services](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Kontrollera den slutliga utformning av tabellen featurized
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Vi är nu redo att fortsätta att modellskapandet och distribution av modellen i [Azure Machine Learning](https://studio.azureml.net). Data är redo för någon av de förutsägelse problem som konstaterats tidigare, nämligen:

1. Binär klassificering: för att förutsäga om huruvida ett tips har betalat för en resa.
2. Multiklass-baserad klassificering: att förutsäga intervallet för tips betald enligt de tidigare definierade klasserna.
3. Regression uppgiften: att förutsäga mängden tips för en resa.  

## <a name="mlmodel"></a>Skapa modeller i Azure Machine Learning
Om du vill börja modellering du logga in på Azure Machine Learning-arbetsytan. Om du inte har skapat machine learning-arbetsytan finns [skapa en arbetsyta för Azure Machine Learning](../studio/create-workspace.md).

1. Om du vill komma igång med Azure Machine Learning finns [vad är Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net).
3. Sidan Studio innehåller en mängd information, videor, självstudier, länkar till moduler referens och andra resurser. Mer information om Azure Machine Learning finns i [Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

En typisk träningsexperiment består av följande:

1. Skapa en **+ ny** experiment.
2. Hämta data till Azure Machine Learning.
3. Förbearbeta, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela data i utbildning / / verifieringstesterna datauppsättningar (eller ha separata datauppsättningar för varje).
6. Välj en eller flera maskininlärningsalgoritmer beroende på learning problemet att lösa. T.ex. binär klassificering, multiklass-baserad klassificering, regression.
7. Träna en eller flera modeller med hjälp av utbildning dataset.
8. Poängsätta validering datauppsättningen med den tränade modeller.
9. Utvärdera modeller för att beräkna relevanta mätvärden för learning problemet.
10. Bra finjustera modeller och välj den bästa modellen för distribution.

I den här övningen har vi redan utforskade och utformad data i SQL Server och valt exempelstorleken att mata in i Azure Machine Learning. Att skapa en eller flera av förutsägelse vi valt:

1. Hämta data till Azure Machine Learning med hjälp av den [importera Data] [ import-data] modulen är tillgängliga i den **Data ingående och utgående** avsnitt. Mer information finns i [importera Data] [ import-data] modulsida referens.
   
    ![Azure Machine Learning importera Data][17]
2. Välj **Azure SQL Database** som den **datakällan** i den **egenskaper** panelen.
3. Ange DNS-namnet för databasen i den **Databasservernamnet** fältet. Format:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange den **databasnamnet** i motsvarande fält.
5. Ange den **användarnamn för SQL** i den ** aqccount användarnamnet och lösenordet i den **serverlösenord**.
6. Kontrollera **acceptera alla servercertifikat** alternativet.
7. I den **databasfrågan** redigera texten, klistrar in frågan som extraherar nödvändiga databasfält (inklusive eventuella beräknade fält, till exempel etiketter) och ned exempel data till den önskade provtagning.

Ett exempel på en binär klassificering experiment läsning av data direkt från SQL Server-databasen är i bilden nedan. Liknande experiment kan konstrueras för multiklass-baserad klassificering och regressionsproblem.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> För modellering data extrahering och samlar frågan exempel som i föregående avsnitt **alla etiketter för de tre modellering övningarna ingår i frågan**. Ett viktigt steg i (obligatoriskt) i varje modellering övningarna är att **undanta** onödiga etiketterna för de andra två problem och andra **mål minnesläckor**. För t.ex. när du använder binär klassificering, använda etiketten **lutad** och utelämna fälten **tips\_klassen**, **tips\_belopp**, och **totala\_belopp**. Dessa är målet minnesläckor eftersom de innebär tips betald.
> 
> Om du vill exkludera onödiga kolumner och/eller mål minnesläckor, kan du använda den [Välj kolumner i datauppsättning] [ select-columns] modul eller [redigera Metadata][edit-metadata]. Mer information finns i [Välj kolumner i datauppsättning] [ select-columns] och [redigera Metadata] [ edit-metadata] referera sidor.
> 
> 

## <a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När modellen är klar, kan du enkelt distribuera det som en webbtjänst direkt från experimentet. Mer information om hur du distribuerar Azure Machine Learning-webbtjänster finns [distribuera en Azure Machine Learning-webbtjänst](../studio/publish-a-machine-learning-web-service.md).

Om du vill distribuera en ny webbtjänst, måste du:

1. Skapa ett bedömningsprofil experiment.
2. Distribuera webbtjänsten.

Så här skapar du ett bedömningsprofil experiment från en **avslutad** utbildning experiment, klickar du på **skapa BEDÖMNINGEN EXPERIMENTERA** i Åtgärdsfältet lägre.

![Azure bedömningen][18]

Azure Machine Learning försöker skapa ett bedömningsprofil experiment som bygger på komponenterna för utbildning experimentet. I synnerhet att:

1. Spara den tränade modellen och ta bort modellen utbildningsmoduler.
2. Identifiera en logisk **inkommande port** som representerar det förväntade indata-schemat.
3. Identifiera en logisk **utgående port** att representera utdataschema förväntade web service.

När bedömningsprofil experiment skapas, granska den och justera efter behov. En typisk justering är att ersätta inkommande dataset och/eller fråga med en vilket utesluter etikett fält, eftersom dessa inte är tillgänglig när tjänsten har anropats. Det är också en bra idé att minska storleken på inkommande dataset och/eller fråga till några poster bara tillräckligt för att ange det inkommande schemat. För den utgående porten, är det vanligt att undanta alla inmatningsfält och bara ta med den **poängsatta etiketter** och **bedömas sannolikhet** i utdata med den [Välj kolumner i datauppsättning] [ select-columns] modul.

Ett exempel på en bedömningen experiment är i bilden nedan. När du är klar att distribuera klickar du på den **publicera WEBBTJÄNSTEN** knappen i det nedre Åtgärdsfältet.

![Publicera Azure Machine Learning][11]

Om du vill Sammanfattningsvis i den här genomgången självstudiekursen har du skapat ett Azure datavetenskap miljö, arbetat med en stor offentliga datauppsättning allt från datainsamling modellera träning och distributionen av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licensinformationen
Den här genomgången exempel och dess tillhörande skript och IPython notebook(s) delas av Microsoft under MIT-licensen. Kontrollera filen LICENSE.txt i katalogen exempelkoden på GitHub för mer information.

### <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi resor hämtningssidan](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxitransport resa Data av Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi och Limousine kommissionens forskning och statistik](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

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
