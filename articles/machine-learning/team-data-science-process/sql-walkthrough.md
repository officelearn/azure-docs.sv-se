---
title: Skapa och distribuera en modell i en VIRTUELL SQL Server - Team Data Science Process
description: Skapa och distribuera en maskininlärningsmodell med SQL Server på en Virtuell Azure med en offentligt tillgänglig datauppsättning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a47f30cf00624faf098c8b605534cf355eacadee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251588"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Teamdatavetenskapsprocessen i aktion: använda SQL Server
I den här självstudien går du igenom processen att skapa och distribuera en maskininlärningsmodell med SQL Server och en offentligt tillgänglig datauppsättning – NYC Taxi Trips-datauppsättningen. [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) Proceduren följer ett standardarbetsflöde för datavetenskap: inta och utforska data, teknikerfunktioner för att underlätta inlärning, sedan skapa och distribuera en modell.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi Resor Dataset Beskrivning
Den NYC Taxi Trip data är ca 20 GB komprimerade CSV filer (~ 48 GB okomprimerad), bestående av mer än 173 miljoner enskilda resor och de priser som betalas för varje resa. Varje resa post innehåller pickup och dropoff plats och tid, anonymiserade hacka (förarens) licensnummer och medaljong (taxi unika id) nummer. Uppgifterna omfattar alla resor år 2013 och finns i följande två datamängder för varje månad:

1. Csv:n "trip_data" innehåller reseinformation, till exempel antal passagerare, pickup- och avlämningsplatser, resans varaktighet och resans längd. Här är några exempelposter:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Csv:n "trip_fare" innehåller uppgifter om det pris som betalats för varje resa, såsom betalningstyp, biljettprisbelopp, tilläggsavgift och skatter, tips och vägtullar samt det totala belopp som betalats. Här är några exempelposter:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den unika nyckeln\_för att\_gå med resa data och resa\_biljettpris\_består av fälten: medaljong, hacka licens och pickup datetime.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exempel på förutsägelseuppgifter
Vi kommer att formulera tre förutsägelse problem baserat på *\_tips belopp,* nämligen:

* Binär klassificering: Förutsäga om ett tips har betalats för en resa, det vill än ett *tips\_belopp* som är större än $0 är ett positivt exempel, medan ett tips *\_belopp* på $0 är ett negativt exempel.
* Multiclass klassificering: För att förutsäga intervallet av tips betalas för resan. Vi delar *\_upp tipsbeloppet* i fem fack eller klasser:
   
        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20
* Regressionsuppgift: För att förutsäga hur mycket tips som betalats för en resa.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurera Azure-datavetenskapsmiljön för avancerad analys
Som du kan se i guiden [Planera din miljö](plan-your-environment.md) finns det flera alternativ för att arbeta med NYC Taxi Trips-datauppsättningen i Azure:

* Arbeta med data i Azure-blobbar och sedan modellera i Azure Machine Learning
* Läs in data i en SQL Server-databas och sedan modellera i Azure Machine Learning

I den här guiden kommer vi att visa parallell bulkimport av data till en SQL Server, datautforskning, funktionsteknik och nedsampling med HJÄLP AV SQL Server Management Studio samt använda IPython Notebook. [Exempelskript](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) och [IPython-anteckningsböcker](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) delas i GitHub. Ett exempel på IPython-anteckningsbok för att arbeta med data i Azure-blobbar är också tillgänglig på samma plats.

Så här konfigurerar du din Azure Data Science-miljö:

1. [skapar ett lagringskonto](../../storage/common/storage-account-create.md)
2. [Skapa en Azure Machine Learning-arbetsyta](../studio/create-workspace.md)
3. [Etablera en virtuell dator för datavetenskap](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)som tillhandahåller en SQL Server och en IPython Notebook-server.
   
   > [!NOTE]
   > Exempelskripten och IPython-anteckningsböckerna hämtas till den virtuella datorn Data Science under installationsprocessen. När vm-efterinstallationsskriptet är klart finns exemplen i vm:s dokumentbibliotek:  
   > 
   > * Exempel på skript:`C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Exempel på IPython-anteckningsböcker:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   var `<user_name>` är din virtuella dators Windows-inloggningsnamn. Vi kommer att hänvisa till exempelmapparna som **exempelskript** och **prov-IPython-anteckningsböcker**.
   > 
   > 

Baserat på datauppsättningsstorleken, datakällplatsen och den valda Azure-målmiljön liknar det här scenariot [ \#Scenario 5: Stor datauppsättning i en lokal fil, mål-SQL Server i Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Hämta data från offentlig källa
Om du vill hämta [NYC Taxi Trips-datauppsättningen](https://www.andresmh.com/nyctaxitrips/) från den offentliga platsen kan du använda någon av de metoder som beskrivs i [Flytta data till och från Azure Blob Storage](move-azure-blob.md) för att kopiera data till din nya virtuella dator.

Så här kopierar du data med AzCopy:

1. Logga in på din virtuella dator (VM)
2. Skapa en ny katalog i den virtuella datorns datadisk (Obs: Använd inte den temporära disken som medföljer den virtuella datorn som en datadisk).
3. I ett kommandotolksfönster kör du följande kommandorad för akopiering och ersätter <path_to_data_folder> med datamappen som skapats i (2):
   
        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
   
    När AzCopy är klar bör totalt 24 ZIPPade CSV-filer\_(12 för\_resedata och 12 för resebiljett) finnas i datamappen.
4. Packa upp de nedladdade filerna. Observera mappen där de okomprimerade filerna finns. Den här mappen kallas\_<sökvägen till\_datafiler\>\_.

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Massimportdata till SQL Server-databas
Prestanda för inläsning/överföring av stora mängder data till en SQL-databas och efterföljande frågor kan förbättras med hjälp av *partitionerade tabeller och vyer*. I det här avsnittet följer vi instruktionerna som beskrivs i [Parallel Bulk Data Import Med SQL Partition-tabeller](parallel-load-sql-partitioned-tables.md) för att skapa en ny databas och läsa in data i partitionerade tabeller parallellt.

1. När du är inloggad på den virtuella datorn startar du **SQL Server Management Studio**.
2. Anslut med Windows-autentisering.
   
    ![SSMS Anslut][12]
3. Om du ännu inte har ändrat SQL Server-autentiseringsläget och skapat en ny SQL-inloggningsanvändare öppnar du skriptfilen **change\_auth.sql** i mappen **Exempelskript.** Ändra standardanvändarnamn och lösenord. Klicka på **Kör** i verktygsfältet för att köra skriptet.
   
    ![Köra skript][13]
4. Verifiera och/eller ändra STANDARDDATABAS- och LOGGMAPPARNA för SQL Server för att säkerställa att nyskapade databaser lagras i en datadisk. SQL Server VM-avbildningen som är optimerad för datalagringsbelastningar är förkonfigurerad med data- och loggdiskar. Om den virtuella datorn inte innehöll en datadiskett och du lade till nya virtuella hårddiskar under installationen av den virtuella datorn ändrar du standardmapparna på följande sätt:
   
   * Högerklicka på SQL Server-namnet på den vänstra panelen och klicka på **Egenskaper**.
     
       ![Egenskaper för SQL Server][14]
   * Välj **Databasinställningar** i listan **Välj en sida** till vänster.
   * Verifiera och/eller ändra **standardplatserna för databasen** till de platser **för datadisk** som du väljer. Den här platsen är där nya databaser finns om de skapas med standardinställningarna.
     
       ![Standardinställningar för SQL-databas][15]  
5. Om du vill skapa en ny databas och en uppsättning filgrupper som ska innehålla de partitionerade tabellerna öppnar du exempelskriptet **create\_db\_default.sql**. Skriptet skapar en ny databas med namnet **TaxiNYC** och 12 filgrupper på standarddataplatsen. Varje filgrupp kommer att\_innehålla en\_månad av resedata och resebiljettbiljettdata. Ändra databasnamnet om så önskas. Klicka på **Kör** för att köra skriptet.
6. Skapa sedan två partitionstabeller,\_en för resedata och en annan för resepriset.\_ Öppna exempelskriptet **\_skapa\_partitionerad table.sql**, som kommer att:
   
   * Skapa en partitionsfunktion för att dela upp data efter månad.
   * Skapa ett partitionsschema för att mappa varje månads data till en annan filgrupp.
   * Skapa två partitionerade tabeller mappade till partitionsschemat:\_ **nyctaxi\_resa** kommer att hålla resan data och **nyctaxi\_biljettpriset** kommer att hålla resan\_biljettprisdata.
     
     Klicka på **Kör** om du vill köra skriptet och skapa de partitionerade tabellerna.
7. I mappen **Exempelskript** finns det två exempel på PowerShell-skript som ska demonstrera parallell massimport av data till SQL Server-tabeller.
   
   * **bcp\_\_parallel generic.ps1** är ett generiskt skript för parallella massimportdata till en tabell. Ändra skriptet för att ange indata- och målvariablera enligt kommentarsraderna i skriptet.
   * **bcp\_\_parallell nyctaxi.ps1** är en förkonfigurerad version av det generiska skriptet och kan användas för att ladda båda tabellerna för NYC Taxi Trips-data.  
8. Högerklicka på **bcp-namnet\_för parallella\_nyctaxi.ps1** och klicka på **Redigera** för att öppna det i PowerShell. Granska de förinställda variablerna och ändra enligt ditt valda databasnamn, indatamapp, målloggmapp och sökvägar till exempelformatfilerna **nyctaxi_trip.xml** och **\_nyctaxi fare.xml** (finns i mappen **Exempelskript).**
   
    ![Massimportdata][16]
   
    Du kan också välja autentiseringsläge, standard är Windows-autentisering. Klicka på den gröna pilen i verktygsfältet för att köras. Skriptet startar 24 massimportåtgärder parallellt, 12 för varje partitionerad tabell. Du kan övervaka dataimportförloppet genom att öppna standarddatamappen för SQL Server enligt ovan.
9. PowerShell-skriptet rapporterar start- och sluttider. När all massimport är klar rapporteras sluttiden. Kontrollera målloggmappen för att kontrollera att massimporten lyckades, det vill än inga fel som rapporterats i målloggmappen.
10. Databasen är nu klar för utforskning, funktionsteknik och andra åtgärder som önskas. Eftersom tabellerna är partitionerade enligt fältet **upphämtningsdatumtid,\_** kommer frågor som innehåller villkor för **upphämtningsdatumtid\_** i **WHERE-satsen** att dra nytta av partitionsschemat.
11. I **SQL Server Management Studio**undersöker du det medföljande exempelskriptets **exempelfrågor.sql\_**. Om du vill köra någon av exempelfrågorna markerar du frågeraderna och klickar sedan på **Kör** i verktygsfältet.
12. UPPGIFTERNA OM NYC Taxi Trips läses in i två separata tabeller. För att förbättra kopplingsåtgärderna rekommenderar vi starkt att tabellerna indexeras. Exempelskriptet **\_skapar\_partitionerade index.sql** skapar partitionerade index på den sammansatta kopplingsnyckelmedaljongen, **hacklicensen\_och upphämtningsdatumtiden\_**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Datautforskning och funktionsteknik i SQL Server
I det här avsnittet kommer vi att utföra datautforskning och funktionsgenerering genom att köra SQL-frågor direkt i **SQL Server Management Studio** med hjälp av SQL Server-databasen som skapats tidigare. Ett exempelskript med namnet **sample\_queries.sql** finns i mappen **Exempelskript.** Ändra skriptet för att ändra databasnamnet, om det skiljer sig från standard: **TaxiNYC**.

I denna övning kommer vi att:

* Anslut till **SQL Server Management Studio** med windowsautentisering eller sql-autentisering och SQL-inloggningsnamn och lösenord.
* Utforska datadistributioner av några fält i olika tidsfönster.
* Undersök datakvaliteten för fälten longitud och latitud.
* Generera binära klassificeringsetiketter och klassificeringsetiketter med flera klasser baserat på **tipsmängden\_**.
* Generera funktioner och beräkna/jämföra trippavstånd.
* Gå med i de två tabellerna och extrahera ett slumpmässigt urval som kommer att användas för att bygga modeller.

När du är redo att gå vidare till Azure Machine Learning kan du antingen:  

1. Spara den slutliga SQL-frågan för att extrahera och ta prov på data och kopiera klistra in frågan direkt i en [importdatamodul][import-data] i Azure Machine Learning, eller
2. Bevara de samplade och konstruerade data som du planerar att använda för modellbygge i en ny databastabell och använd den nya tabellen i modulen [Importera data][import-data] i Azure Machine Learning.

I det här avsnittet sparar vi den sista frågan för att extrahera och ta prov på data. Den andra metoden visas i avsnittet [Data Exploration and Feature Engineering i IPython Notebook.](#ipnb)

För en snabb verifiering av antalet rader och kolumner i tabellerna som fyllts i tidigare med parallell massimport,

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Utforskning: Trip distribution av medaljong
I det här exemplet identifieras medaljongen (taxinummer) med mer än 100 resor inom en viss tidsperiod. Frågan skulle dra nytta av den partitionerade tabellen åtkomst eftersom det är betingat av partitionen systemet **för\_pickup datetime**. Genom att fråga hela datauppsättningen används också den partitionerade tabellen och/eller indexgenomsökningen.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Utforskning: Resefördelning med medaljong och hack_license
    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Bedömning av datakvalitet: Verifiera poster med felaktig longitud och/eller latitud
I det här exemplet undersöks om något av longitud- och/eller latitudfälten antingen innehåller ett ogiltigt värde (radianexamina bör vara mellan -90 och 90) eller ha (0, 0) koordinater.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Prospektering: Tipped vs Inte Tipped Trips distribution
I det här exemplet hittas antalet resor som tippades jämfört med inte tippas under en viss tidsperiod (eller i hela datauppsättningen om de täcker hela året). Den här fördelningen återspeglar den binära etikettfördelning som senare ska användas för binär klassificeringsmodellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Utforskning: Tips klass / range distribution
I det här exemplet beräknas fördelningen av tipsintervall under en viss tidsperiod (eller i hela datauppsättningen om den täcker hela året). Den här fördelningen av etikettklasserna kommer att användas senare för klassificeringsmodellering av flera klasser.

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

#### <a name="exploration-compute-and-compare-trip-distance"></a>Utforskning: Beräkna och jämför resa avstånd
I det här exemplet konverteras hämtning och avlämnings longitud och latitud till SQL-geografipunkter, beräknar trippavståndet med SQL-geografipoängsskillnaden och returnerar ett slumpmässigt urval av resultaten för jämförelse. Exemplet begränsar resultaten till giltiga koordinater endast med hjälp av den datakvalitetsbedömningsfråga som tidigare behandlats.

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

#### <a name="feature-engineering-in-sql-queries"></a>Funktionsteknik i SQL-frågor
Etikettgenererings- och geografikonverteringsutforskningsfrågor kan också användas för att generera etiketter/funktioner genom att ta bort den räknedelen. Ytterligare funktionstekniker SQL-exempel finns i avsnittet [Data Exploration and Feature Engineering i IPython Notebook.](#ipnb) Det är mer effektivt att köra funktionsgenereringsfrågorna på den fullständiga datauppsättningen eller en stor delmängd av den med hjälp av SQL-frågor som körs direkt i SQL Server-databasinstansen. Frågorna kan köras i **SQL Server Management Studio**, IPython Notebook eller något utvecklingsverktyg eller en miljö som kan komma åt databasen lokalt eller på distans.

#### <a name="preparing-data-for-model-building"></a>Förbereda data för modellbyggnad
Följande fråga ansluter **\_nyctaxi resa** och **nyctaxi\_biljettpris** tabeller, genererar en binär klassificering etikett **tippade**, en multi-klass klassificering etikett spets **\_klass**, och extraherar en 1% slumpmässigt urval från hela kopplade datauppsättning. Den här frågan kan kopieras och sedan klistras in direkt i modulen [Azure Machine Learning Studio](https://studio.azureml.net) Import [Data][import-data] för direkt datainmatning från SQL Server-databasinstansen i Azure. Frågan utesluter poster med felaktiga (0, 0) koordinater.

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


## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Data exploration och funktionsteknik i IPython Notebook
I det här avsnittet kommer vi att utföra datautforskning och funktionsgenerering med både Python och SQL-frågor mot SQL Server-databasen som skapats tidigare. Ett exempel på IPython-anteckningsbok med namnet **machine-Learning-data-science-process-sql-story.ipynb** finns i mappen **Exempel på IPython-anteckningsböcker.** Den här anteckningsboken är också tillgänglig på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

När du arbetar med stordata följer du den här rekommenderade sekvensen:

* Läs i ett litet urval av data i en dataram i minnet.
* Utför vissa visualiseringar och utforskningar med hjälp av de utvalda data.
* Experimentera med funktionsteknik med hjälp av de utvalda data.
* För större datautforskning, datamanipulering och funktionsteknik använder du Python för att utfärda SQL-frågor direkt mot SQL Server-databasen i Azure VM.
* Bestäm vilken storlek som ska användas för Azure Machine Learning-modellbyggnad.

När du är redo att fortsätta till Azure Machine Learning kan du antingen:  

1. Spara den slutliga SQL-frågan för att extrahera och exempel på data och kopiera klistra in frågan direkt i en [importdatamodul][import-data] i Azure Machine Learning. Den här metoden visas i avsnittet [Byggmodeller i Azure Machine Learning.](#mlmodel)    
2. Bevara de samplade och konstruerade data som du planerar att använda för modellbygge i en ny databastabell och använd sedan den nya tabellen i modulen [Importera data.][import-data]

Följande är några datautforskning, datavisualisering och funktionsteknikexempel. Fler exempel finns i exempelboken SQL IPython i mappen **Exempel på IPython-anteckningsböcker.**

#### <a name="initialize-database-credentials"></a>Initiera databasautentiseringsuppgifter
Initiera inställningarna för databasanslutningen i följande variabler:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Skapa databasanslutning
    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Rapportera antal rader och kolumner i tabell nyctaxi_trip
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

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Läsa in ett litet dataexempel från SQL Server-databasen
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

Tid att läsa exempeltabellen är 6,492000 sekunder  
Antal rader och kolumner som hämtats = (84952, 21)

#### <a name="descriptive-statistics"></a>Beskrivande statistik
Nu är redo att utforska de utvalda data. Vi börjar med att titta på beskrivande statistik för **resan\_avstånd** (eller någon annan) fält (s):

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualisering: Exempel på rutart
Nästa vi tittar på rutan tomten för resan avstånd för att visualisera quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Tomt #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisering: Exempel på distributionsdiagram
    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tomt #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisering: Stapel- och linjediagram
I det här exemplet lagerplatserar vi resans avstånd till fem lagerplatser och visualiserar binningsresultaten.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan rita ovanstående bin distribution i en bar eller linje tomt enligt nedan

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Tomt #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Tomt #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualisering: Exempel på punktspridning
Vi visar scatter plot mellan **restid\_\_i\_sek** och resa **\_avstånd** för att se om det finns något samband

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Tomt #6][6]

På samma sätt kan vi kontrollera förhållandet mellan **hastighetskod\_** och **resans\_avstånd.**

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Tomt #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Sub-Sampling data i SQL
När du förbereder data för modellbygge i [Azure Machine Learning Studio](https://studio.azureml.net)kan du antingen bestämma dig för **SQL-frågan för att använda direkt i modulen Importera data** eller bevara de konstruerade och samhörda data i en ny tabell, som du kan använda i modulen Importera [data][import-data] med en enkel SELECT * FRÅN <**ditt\_nya\_tabellnamn\_>**.

I det här avsnittet skapar vi en ny tabell för att lagra de samplade och konstruerade data. Ett exempel på en direkt SQL-fråga för modelluppbyggnad finns i avsnittet [Data Exploration and Feature Engineering i SQL Server.](#dbexplore)

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Skapa en exempeltabell och fyll i med 1 % av de kopplade tabellerna. Släpp tabell först om den finns.
I det här avsnittet går vi med i tabellerna **nyctaxi\_resa** och **nyctaxi\_biljettpris,** extrahera en 1% slumpmässigt urval, och kvarstår de samplade data i ett nytt tabellnamn **nyctaxi\_en\_procent:**

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

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Datautforskning med SQL-frågor i IPython Notebook
I det här avsnittet utforskar vi datadistributioner med hjälp av 1 % samplade data som sparas i den nya tabellen som vi skapade ovan. Liknande utforskningar kan utföras med hjälp av de ursprungliga tabellerna, eventuellt med **TABLESAMPLE** för att begränsa prospekteringsexempelet eller genom att begränsa resultaten till en viss tidsperiod med hjälp av **\_upphämtningsdatumtidspartitionerna,** vilket illustreras i avsnittet [Data Exploration and Feature Engineering i SQL Server.](#dbexplore)

#### <a name="exploration-daily-distribution-of-trips"></a>Prospektering: Daglig distribution av resor
    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Utforskning: Resefördelning per medaljong
    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Funktionsgenerering med SQL-frågor i IPython Notebook
I det här avsnittet kommer vi att generera nya etiketter och funktioner direkt med hjälp av SQL-frågor, som arbetar på 1% exempeltabell vi skapade i föregående avsnitt.

#### <a name="label-generation-generate-class-labels"></a>Etikettgenerering: Generera klassetiketter
I följande exempel genererar vi två uppsättningar etiketter som ska användas för modellering:

1. Binära klassetiketter **tippade** (förutsäga om ett tips kommer att ges)
2. **Tipsklass\_** med flera klasser etiketter (förutsäga tipsfacket eller intervallet)
   
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

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Feature Engineering: Räkna funktioner för kategoriska kolumner
I det här exemplet omvandlas ett kategoriskt fält till ett numeriskt fält genom att ersätta varje kategori med antalet förekomster i data.

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

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funktionsteknik: Lagerplatsfunktioner för numeriska kolumner
I det här exemplet omvandlas ett kontinuerligt numeriskt fält till förinställda kategoriintervall, det vill säga omvandla numeriskt fält till ett kategoriskt fält.

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

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funktionsteknik: Extrahera platsfunktioner från decimal latitud/longitud
I det här exemplet delas decimalrepresentationen av ett latitud- och/eller longitudfält till flera områdesfält med olika granularitet, till exempel land/region, stad, stad, block osv. De nya geofälten mappas inte till faktiska platser. Information om hur du mappar geokodplatser finns i [Bing Maps REST Services](https://msdn.microsoft.com/library/ff701710.aspx).

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

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifiera den slutliga formen av det medarbest bord
    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Vi är nu redo att gå vidare till modellbygge och modelldistribution i [Azure Machine Learning](https://studio.azureml.net). Uppgifterna är redo för något av de förutsägelseproblem som identifierats tidigare, nämligen:

1. Binär klassificering: För att förutsäga om ett tips betalades för en resa eller inte.
2. Multiclass klassificering: För att förutsäga intervallet av tips betalas, enligt de tidigare definierade klasserna.
3. Regressionsuppgift: För att förutsäga hur mycket tips som betalats för en resa.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Skapa modeller i Azure Machine Learning
För att starta modelleringsövningen loggar du in på din Azure Machine Learning-arbetsyta. Om du ännu inte har skapat en arbetsyta för maskininlärning läser du [Skapa en Azure Machine Learning-arbetsyta](../studio/create-workspace.md).

1. Information om hur du kommer igång med Azure Machine Learning finns i [Vad är Azure Machine Learning Studio?](../studio/what-is-ml-studio.md)
2. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net).
3. Studio-startsidan innehåller en mängd information, videor, självstudier, länkar till modulens referens och andra resurser. Mer information om Azure Machine Learning finns i [Azure Machine Learning Documentation Center](https://azure.microsoft.com/documentation/services/machine-learning/).

Ett typiskt utbildningsexperiment består av följande steg:

1. Skapa ett **+NYTT** experiment.
2. Hämta data till Azure Machine Learning.
3. Förbehandla, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela upp data i utbildnings-/validerings-/testdatauppsättningar(eller har separata datauppsättningar för varje).
6. Välj en eller flera maskininlärningsalgoritmer beroende på vilket inlärningsproblem som ska lösas. Binär klassificering, multiklassklassificering, regression.
7. Träna en eller flera modeller med hjälp av träningsdatauppsättningen.
8. Poängsätta valideringsdatauppsättningen med hjälp av de tränade erna.
9. Utvärdera modellen/erna för att beräkna relevanta mått för inlärningsproblemet.
10. Ställ in modellen/erna och välj den bästa modellen att distribuera.

I den här övningen har vi redan utforskat och konstruerat data i SQL Server och beslutat om exempelstorleken för att inta i Azure Machine Learning. För att bygga en eller flera av förutsägelsemodellerna bestämde vi oss:

1. Hämta data till Azure Machine Learning med modulen [Importera data,][import-data] som är tillgänglig i avsnittet **Datainmatning och utdata.** Mer information finns på referenssidan [för importdatamodulen.][import-data]
   
    ![Importdata för Azure Machine Learning][17]
2. Välj **Azure SQL Database** som **datakälla** på **egenskapspanelen.**
3. Ange databasens DNS-namn i **fältet Databasservernamn.** Format:`tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange **databasnamnet** i motsvarande fält.
5. Ange **SQL-användarnamnet** i **serveranvändarkontots namn**och **lösenordet** i **lösenordet för serveranvändarkontot**.
7. I textområdet Redigera text för **databasfråga** klistrar du in frågan som extraherar nödvändiga databasfält (inklusive alla beräknade fält som etiketterna) och nedexempel data till önskad exempelstorlek.

Ett exempel på en binär klassificering experiment läsa data direkt från SQL Server databasen är i figuren nedan. Liknande experiment kan konstrueras för multiklassklassificerings- och regressionsproblem.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> I exempel på modelleringsdataextrahering och samplingsfråga i föregående avsnitt **inkluderas alla etiketter för de tre modelleringsövningarna i frågan**. Ett viktigt (obligatoriskt) steg i var och en av modelleringsövningarna är att **utesluta** onödiga etiketter för de andra två problemen och alla andra **målläckor**. När du använder binär klassificering använder du till exempel etiketten **tippad** och utesluter **fältspetsklassen,\_** **dricksmängden\_** och **det\_totala beloppet**. De senare är målläckor eftersom de antyder att tipset betalas.
> 
> Om du vill utesluta onödiga kolumner och/eller målläckor kan du använda modulen [Välj kolumner i datauppsättning][select-columns] eller [redigera metadata][edit-metadata]. Mer information finns [i Välj kolumner i referenssidor för datauppsättning][select-columns] och Redigera [metadata.][edit-metadata]
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När din modell är klar kan du enkelt distribuera den som en webbtjänst direkt från experimentet. Mer information om hur du distribuerar Azure Machine Learning-webbtjänster finns i [Distribuera en azure Machine Learning-webbtjänst](../studio/deploy-a-machine-learning-web-service.md).

Om du vill distribuera en ny webbtjänst måste du:

1. Skapa ett bedömningsexperiment.
2. Distribuera webbtjänsten.

Om du vill skapa ett bedömningsexperiment från ett **färdigt** träningsexperiment klickar du på **SKAPA POÄNGSÄTTNINGSEXPERIment** i det nedre åtgärdsfältet.

![Azure-poängsättning][18]

Azure Machine Learning försöker skapa ett bedömningsexperiment baserat på komponenterna i utbildningsexperimentet. I synnerhet kommer den att

1. Spara den tränade modellen och ta bort modellutbildningsmodulerna.
2. Identifiera en logisk **indataport** som representerar det förväntade indataschemat.
3. Identifiera en logisk **utdataport** som representerar det förväntade webbtjänstutdataschemat.

När bedömningsexperimentet skapas granskar du det och justerar efter behov. En typisk justering är att ersätta indatauppsättningen och/eller frågan med en som utesluter etikettfält, eftersom dessa etiketter inte är tillgängliga i schemat när tjänsten anropas. Det är också en bra idé att minska storleken på indatauppsättningen och/eller frågan till några få poster, tillräckligt för att ange indataschemat. För utdataporten är det vanligt att utesluta alla indatafält och endast inkludera **poängsatta etiketter** och **poängsatta sannolikheter** i utdata med modulen [Välj kolumner i datauppsättning.][select-columns]

Ett provbedömningsexperiment finns i figuren nedan. När du är klar att distribuera klickar du på knappen **PUBLICERA WEBBTJÄNST** i det nedre åtgärdsfältet.

![Azure Machine Learning Publicera][11]

För att sammanfatta, i den här genomgången självstudiekurs, har du skapat en Azure data science-miljö, arbetat med en stor offentlig datauppsättning hela vägen från datainsamling till modellutbildning och distribution av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licensinformation
Det här exemplet genomgång och dess medföljande skript och IPython-anteckningsböcker delas av Microsoft under MIT-licensen. Mer information finns i filen LICENSE.txt i katalogen för exempelkoden på GitHub.

### <a name="references"></a>Referenser
• [Andrés Monroy NYC Taxi Trips Ladda ner sida](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC:s taxiresa data av Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi och Limousine kommissionens forskning och statistik](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

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
