---
title: Skapa funktioner för data i ett Azure HDInsight Hadoop kluster – team data science process
description: Exempel på Hive-frågor som genererar funktioner i data som lagras i ett Azure HDInsight Hadoop-kluster.
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
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721786"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Skapa funktioner för data i ett Hadoop-kluster med hjälp av Hive-frågor
Det här dokumentet visar hur du skapar funktioner för data som lagras i ett Azure HDInsight Hadoop-kluster med hjälp av Hive-frågor. De här Hive-frågor använder inbäddade Hive User-Defined funktioner (UDF), skript som tillhandahålls.

Nödvändiga åtgärder för att skapa funktioner kan vara minnesintensiva. Prestanda för Hive-frågor blir mer kritiska i sådana fall och kan förbättras genom att justera vissa parametrar. Justering av dessa parametrar beskrivs i det sista avsnittet.

Exempel på frågor som presenteras är särskilt för [NYC taxi-data](https://chriswhong.com/open-data/foil_nyc_taxi/) scenarierna finns också i [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). De här frågorna har dataschema som angetts redan och är redo att skickas för att köra. I det sista avsnittet diskuteras också parametrar som användare kan justera så att prestanda för Hive-frågor kan förbättras.

Den här uppgiften är ett steg i [TDSP (Team data science process)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver instruktioner, se [skapa ett Azure Storage konto](../../storage/common/storage-account-create.md)
* Etablerat en anpassade Hadoop-kluster med HDInsight-tjänsten.  Om du behöver instruktioner, se [anpassa Azure HDInsight Hadoop kluster för avancerad analys](customize-hadoop-cluster.md).
* Data har överförts till Hive-tabeller i Azure HDInsight Hadoop-kluster. Om den inte har det följer du först [skapa och läsa in data till Hive-tabeller](move-hive-tables.md) för att ladda upp data till Hive-tabeller.
* Aktivera fjärråtkomst till klustret. Om du behöver instruktioner, se [åtkomst till Head-noden i Hadoop-klustret](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Generering av funktioner
I det här avsnittet beskrivs flera exempel på hur där funktioner kan genererar med hjälp av Hive-frågor. När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primär nyckel, vilket kan sedan kopplas till den ursprungliga tabellen. Här följer exempel som visas:

1. [Frekvens-baserad funktions generering](#hive-frequencyfeature)
2. [Risker med kategoriska-variabler i binära klassificering](#hive-riskfeature)
3. [Extrahera funktioner från datetime-fältet](#hive-datefeatures)
4. [Extrahera funktioner från textfält](#hive-textfeatures)
5. [Beräkna avståndet mellan GPS-koordinater](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Frekvens-baserad funktions generering
Det är ofta bra att beräkna frekvenser av en kategoriska variabel eller frekvenser för vissa kombinationer av nivåer från flera kategoriska variabler. Användare kan använda följande skript för att beräkna dessa frekvenser:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Risker med kategoriska-variabler i binära klassificering
I binär klassificering måste icke-numeriska kategoriska variabler konverteras till numeriska funktioner när modeller som används endast numeriska funktioner. Den här konverteringen görs genom att ersätta varje icke-numeriska-nivå med en numerisk risk. Det här avsnittet visar vissa allmänna Hive-frågor som beräknar riskvärden (log oddsen) för en kategoriska variabel.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

I det här exemplet är variabler `smooth_param1` och `smooth_param2` inställda på att utjämna de risk värden som beräknas utifrån data. Risker har ett intervall mellan -Inf och Inf. En risk > 0 anger att sannolikheten att målet är lika med 1 är större än 0,5.

Efter risken beräknas tabell, användare kan tilldela riskvärden till en tabell genom att gå med tabellen risk. Slå samman Hive-frågan har angetts i föregående avsnitt.

### <a name="hive-datefeatures"></a>Extrahera funktioner från DATETIME-fält
Hive levereras med en uppsättning UDF: er för bearbetning av datetime-fält. I Hive, standardformatet för datum/tid är ”åååå-MM-dd 00:00:00 ' ('1970-01-01 12:21:32, till exempel). Det här avsnittet visas exempel som extraherar dagen i månaden, månaden från ett datetime-fält och andra exempel som konverteras en datetime-sträng i formatet andra än standardformatet till ett datetime-sträng i formatet.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Den här Hive-frågan förutsätter att *fältet\<datetime >* är i standardformatet för datum/tid.

Om ett datetime-fält inte är i standardformatet, måste du konvertera datum / tidsfält till Unix-tidsstämpel först och sedan konvertera Unix-tidsstämpel till en datetime-sträng i standardformat. När datum/tid är i formatet kan använda användare embedded datum/tid UDF: er att extrahera funktioner.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

I den här frågan, om *fältet\<datetime >* har mönstret som *03/26/2015 12:04:39*, måste *\<mönstret för datetime-fältet >* vara `'MM/dd/yyyy HH:mm:ss'`. Om du vill testa den kan användare som köra

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* i den här frågan är förinstallerat på alla Azure HDInsight Hadoop kluster som standard när klustren är etablerade.

### <a name="hive-textfeatures"></a>Extrahera funktioner från textfält
När Hive-tabellen har ett textfält som innehåller en ordsträng som avgränsas med blanksteg, extraherar följande fråga längden på strängen och antalet ord i strängen.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Beräkna avstånd mellan uppsättningar av GPS-koordinater
Frågan som anges i det här avsnittet kan tillämpas direkt på NYC Taxi Resedata. Syftet med den här frågan är att visa hur du använder en inbäddad matematisk funktion i Hive för att generera funktioner.

Fälten som används i den här frågan är GPS-koordinaterna för pickup-och DropOff-platser, med namnet *pickup\_longitud*, *pickup\_latitud*, *dropoff\_longitud*och *DropOff\_Latitude*. Frågorna som beräknar direkt avståndet mellan koordinaterna hämtning och dropoff är:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

De matematiska ekvationer som beräknar avståndet mellan två GPS-koordinater finns på den <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">flyttbara typ skript</a> platsen som skapats av Peter Lapisu. I det här java script är funktionen `toRad()` bara *lat_or_lon*PI/180, vilket konverterar grader till radianer. Här *lat_or_lon* är latitud eller longitud. Eftersom Hive inte tillhandahåller funktionen `atan2`, men innehåller funktionen `atan`, implementeras `atan2`-funktionen av `atan` funktion i Hive-frågan med den definition som anges i <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Skapa arbetsyta](./media/create-features-hive/atan2new.png)

En fullständig lista över inbäddade Hive-UDF: er finns i avsnittet **inbyggda funktioner** på <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a>Avancerade ämnen: finjustera Hive-parametrar för att förbättra frågans hastighet
Standardinställning för Hive-kluster är kanske inte lämpligt för Hive-frågor och data som bearbetar frågor. Det här avsnittet beskrivs vissa parametrar som användare kan justera för att förbättra prestanda för Hive-frågor. Användare måste lägga till parametern justering frågor innan frågor för bearbetning av data.

1. **Java heap-utrymme**: för frågor som rör anslutning av stora data uppsättningar, eller bearbetning av långa poster, är det ett av de vanliga felen att **köra utanför heap-utrymmet** . Det här felet kan undvikas genom att ange parametrarna *MapReduce. map. java.* Välj och *MapReduce. Task. io. sort. MB* till önskade värden. Här är ett exempel:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Med den här parametern allokeras 4 GB minne till ett Java-heap-utrymme och du kan också sortera mer effektivt genom att allokera mer minne för den. Det är en bra idé att experimentera med dessa allokeringar om det finns jobb felen som rör heap utrymme.

1. **Storlek på DFS-block**: den här parametern anger den minsta data enheten som fil systemet lagrar. Till exempel om DFS-blockstorlek är 128 MB, sedan valfria data med storlek mindre än och upp till lagras 128 MB i ett enda block. Data som är större än 128 MB tilldelas extra block. 
2. Välja en liten blockstorlek gör stora kostnaderna i Hadoop eftersom noden namn har att bearbeta många fler begäranden för att hitta relevant block som hör till filen. En rekommenderad inställning när gäller gigabyte (eller större) data är:

        set dfs.block.size=128m;

2. **Optimering av kopplings åtgärd i Hive**: vid kopplings åtgärder i kart-och minsknings ramverket sker vanligt vis i minsknings fasen, men enorma vinster kan uppnås genom att schemalägga kopplingar i kart fasen (kallas även "mapjoins"). Ange det här alternativet:
   
       set hive.auto.convert.join=true;

3. **Ange antalet mappningar till Hive**: medan Hadoop tillåter att användaren anger antalet avreducerare, anges inte antalet mappningar av användaren. Ett stick som tillåter viss kontroll av det här antalet är att välja Hadoop-variablerna *mapred. min. Split. size* och *mapred. max. Split. storlek* eftersom storleken på varje kart uppgift bestäms av:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalt standardvärdet:
    
   - *mapred. min. Split. storlek* är 0, det vill säga
   - *mapred. max. Split. storlek* är **långt. Max** och för 
   - *DFS. block. storlek* är 64 MB.

     Som vi kan se, beroende på datastorleken, justera parametrarna genom att ”inställningen” dem kan vi justera antalet Mappningskomponenter används.

4. Här följer några fler **Avancerade alternativ** för att optimera Hive-prestanda. Med de här alternativen kan du ange hur mycket minne som allokeras för att mappa och minska aktiviteter, och det kan vara användbart när du vill ändra prestanda. Tänk på att *MapReduce. reduce. Memory. MB* får inte vara större än storleken på det fysiska minnet för varje arbetsnod i Hadoop-klustret.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

