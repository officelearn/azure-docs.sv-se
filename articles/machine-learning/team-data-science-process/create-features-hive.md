---
title: Skapa funktioner för data i ett Azure HDInsight Hadoop-kluster - Team Data Science Process
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721786"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Skapa funktioner för data i ett Hadoop-kluster med Hive-frågor
Det här dokumentet visar hur du skapar funktioner för data som lagras i ett Azure HDInsight Hadoop-kluster med Hive-frågor. Dessa Hive-frågor använder inbäddade Hive-användardefinierade funktioner (UDFs), de skript som tillhandahålls.

De åtgärder som behövs för att skapa funktioner kan vara minnesintensiva. Prestandan för Hive-frågor blir mer kritiska i sådana fall och kan förbättras genom att justera vissa parametrar. Justeringen av dessa parametrar diskuteras i det sista avsnittet.

Exempel på de frågor som presenteras är specifika för [NYC Taxi Trip Data-scenarierna](https://chriswhong.com/open-data/foil_nyc_taxi/) finns också i [GitHub-databasen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Dessa frågor har redan dataschemat angivet och är redo att skickas för att köras. I det sista avsnittet diskuteras också parametrar som användare kan ställa in så att prestanda för Hive-frågor kan förbättras.

Den här uppgiften är ett steg i [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* Skapade ett Azure-lagringskonto. Om du behöver instruktioner läser du [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md)
* Etablerat ett anpassat Hadoop-kluster med HDInsight-tjänsten.  Om du behöver instruktioner läser du [Anpassa Azure HDInsight Hadoop-kluster för avancerad analys](customize-hadoop-cluster.md).
* Data har överförts till Hive-tabeller i Azure HDInsight Hadoop-kluster. Om den inte har gjort det följer du [Skapa och läs in data till Hive-tabeller](move-hive-tables.md) för att ladda upp data till Hive-tabeller först.
* Aktiverad fjärråtkomst till klustret. Om du behöver instruktioner läser du [Komma åt huvudnoden för Hadoop-kluster](customize-hadoop-cluster.md).

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Generering av funktioner
I det här avsnittet beskrivs flera exempel på hur funktioner kan genereras med Hjälp av Hive-frågor. När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primärnyckel, som sedan kan sammanfogas med den ursprungliga tabellen. Här är de exempel som presenteras:

1. [Frekvensbaserad funktionsgenerering](#hive-frequencyfeature)
2. [Risker för kategoriska variabler i binär klassificering](#hive-riskfeature)
3. [Extrahera funktioner från datetime-fältet](#hive-datefeatures)
4. [Extrahera funktioner från textfält](#hive-textfeatures)
5. [Beräkna avståndet mellan GPS-koordinater](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Frekvensbaserad funktionsgenerering
Det är ofta användbart att beräkna frekvenserna för nivåerna för en kategorisk variabel, eller frekvenserna för vissa kombinationer av nivåer från flera kategoriska variabler. Användare kan använda följande skript för att beräkna dessa frekvenser:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Risker för kategoriska variabler i binär klassificering
I binär klassificering måste icke-numeriska kategoriska variabler konverteras till numeriska funktioner när de modeller som används endast tar numeriska funktioner. Den här konverteringen görs genom att ersätta varje icke-numerisk nivå med en numerisk risk. I det här avsnittet visas några allmänna Hive-frågor som beräknar riskvärdena (loggodds) för en kategorisk variabel.

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

I det här `smooth_param1` exemplet `smooth_param2` är variabler och inställda på att jämna ut de riskvärden som beräknas från data. Riskerna har ett intervall mellan -Inf och Inf. En risk > 0 indikerar att sannolikheten för att målet är lika med 1 är större än 0,5.

När risktabellen har beräknats kan användare tilldela riskvärden till en tabell genom att sammanfoga den med risktabellen. Hive-anslutningsfrågan angavs i föregående avsnitt.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Extrahera funktioner från datetime-fält
Hive levereras med en uppsättning UDF:er för bearbetning av datetime-fält. I Hive är standardformatet för datetime 'yyyy-MM-dd 00:00:00' ('1970-01-01 12:21:32'till exempel). I det här avsnittet visas exempel som extraherar dagen i en månad, månaden från ett datetime-fält och andra exempel som konverterar en datetime-sträng i ett annat format än standardformatet till en datetime-sträng i standardformat.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Den här Hive-frågan förutsätter att * \<datetime-fältet>* är i standardformatet för datetime.

Om ett datetime-fält inte är i standardformatet måste du först konvertera datetime-fältet till Unix tidsstämpel och sedan konvertera Unix tidsstämpeln till en datetime-sträng som är i standardformatet. När datumtiden är i standardformat kan användare använda inbäddade UDFs för datetime för att extrahera funktioner.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Om * \<datetime-fältet>* har mönstret som *2015-03-26 12:04:39*bör * \<mönstret för datetime-fältet> vara* `'MM/dd/yyyy HH:mm:ss'`. Om du vill testa det kan användare köra

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* i den här frågan förinstalleras på alla Azure HDInsight Hadoop-kluster som standard när klustren etableras.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Extrahera funktioner från textfält
När tabellen Hive har ett textfält som innehåller en sträng med ord som avgränsas av blanksteg extraherar följande fråga strängens längd och antalet ord i strängen.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Beräkna avstånd mellan uppsättningar av GPS-koordinater
Frågan som anges i det här avsnittet kan tillämpas direkt på NYC Taxi Trip Data. Syftet med den här frågan är att visa hur du använder en inbäddad matematisk funktion i Hive för att generera funktioner.

Fälten som används i den här frågan är GPS-koordinaterna för upphämtnings- och avlämningsplatser, namngiven *pickup\_longitud,* *\_pickup-latitud,* *avlämnings\_longitud*och *avlämnings\_latitud*. De frågor som beräknar det direkta avståndet mellan upphämtnings- och avlämningskoordinaterna är:

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

De matematiska ekvationer som beräknar avståndet mellan två GPS-koordinater finns på webbplatsen <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts,</a> skapad av Peter Lapisu. I detta Javascript `toRad()` är funktionen bara *lat_or_lon*pi/180, som konverterar grader till radianer. Här är *lat_or_lon* latitud eller longitud. Eftersom Hive inte ger `atan2`funktionen , `atan`men `atan2` ger funktionen , `atan` funktionen genomförs av funktionen i ovanstående Hive fråga med hjälp av definitionen i <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Skapa arbetsyta](./media/create-features-hive/atan2new.png)

En fullständig lista över Hive inbäddade UDFs finns i avsnittet **Inbyggda funktioner** på <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a>Avancerade ämnen: Justera Hive-parametrar för att förbättra frågehastigheten
Standardparameterinställningarna för Hive-klustret kanske inte är lämpliga för Hive-frågorna och de data som frågorna bearbetar. I det här avsnittet beskrivs några parametrar som användare kan ställa in för att förbättra prestanda för Hive-frågor. Användare måste lägga till parameterjusteringsfrågorna innan frågorna om bearbetning av data.

1. **Java heap utrymme:** För frågor som innebär att ansluta stora datamängder, eller bearbetning av långa poster, **slut på heap utrymme** är ett av de vanligaste felen. Det här felet kan undvikas genom att ställa in parametrar *mapreduce.map.java.opts* och *mapreduce.task.io.sort.mb* till önskade värden. Här är ett exempel:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Den här parametern allokerar 4 GB-minne till Java-heap-utrymme och gör också sortering effektivare genom att allokera mer minne för det. Det är en bra idé att spela med dessa allokeringar om det finns några fel i samband med heap-utrymme.

1. **DFS-blockstorlek**: Den här parametern anger den minsta dataenheten som filsystemet lagrar. Om DFS-blockstorleken är 128 MB lagras till exempel alla data av storlek som är mindre än och upp till 128 MB i ett enda block. Data som är större än 128 MB tilldelas extra block. 
2. Om du väljer en liten blockstorlek får stora omkostnader i Hadoop att bearbeta många fler begäranden för att hitta det relevanta blocket som hör till filen. En rekommenderad inställning när det handlar om gigabyte (eller större) data är:

        set dfs.block.size=128m;

2. **Optimera kopplingsåtgärden i Hive**: Medan koppling i kartan /reducer ram vanligtvis sker i reduceringsfasen, ibland kan enorma vinster uppnås genom schemaläggningskopplingar i kartfasen (även kallad "mapjoins"). Ange det här alternativet:
   
       set hive.auto.convert.join=true;

3. **Ange antalet mappers till Hive**: Medan Hadoop tillåter användaren att ställa in antalet reducerare, antalet mappers är vanligtvis inte anges av användaren. Ett trick som tillåter en viss grad av kontroll på detta nummer är att välja Hadoop variabler *mapred.min.split.size* och *mapred.max.split.size* som storleken på varje karta uppgift bestäms av:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalt är standardvärdet för:
    
   - *mapred.min.split.size* är 0, den för
   - *mapred.max.split.size* är **Long.MAX** och den av 
   - *dfs.block.size* är 64 MB.

     Som vi kan se, med tanke på datastorleken, tuning dessa parametrar genom att "ställa in" dem tillåter oss att ställa in antalet mappers som används.

4. Här är några andra mer **avancerade alternativ** för att optimera Hive prestanda. Med de här alternativen kan du ställa in det minne som allokerats för att mappa och minska uppgifter, och kan vara användbart för att justera prestanda. Tänk på att *mapreduce.reduce.memory.mb* inte kan vara större än den fysiska minnesstorleken för varje arbetsnod i Hadoop-klustret.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

