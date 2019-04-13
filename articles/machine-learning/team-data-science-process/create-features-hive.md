---
title: Skapa funktioner för data i ett Hadoop-kluster - Team Data Science Process
description: Exempel på Hive-frågor som genererar funktioner i data som lagras i ett Azure HDInsight Hadoop-kluster.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a491f923d7755513d84adfe765d595a3a7a80715
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59524913"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Skapa funktioner för data i ett Hadoop-kluster med hjälp av Hive-frågor
Det här dokumentet visar hur du skapar funktioner för data som lagras i ett Azure HDInsight Hadoop-kluster med hjälp av Hive-frågor. De här Hive-frågor använder inbäddade Hive User-Defined funktioner (UDF), skript som tillhandahålls.

Nödvändiga åtgärder för att skapa funktioner kan vara minnesintensiva. Prestanda för Hive-frågor blir mer kritiska i sådana fall och kan förbättras genom att justera vissa parametrar. Justering av dessa parametrar beskrivs i det sista avsnittet.

Exempel på de frågor som visas är specifika för den [NYC Taxi Resedata](https://chriswhong.com/open-data/foil_nyc_taxi/) scenarier finns också i [GitHub-lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). De här frågorna har dataschema som angetts redan och är redo att skickas för att köra. I det sista avsnittet diskuteras också parametrar som användare kan justera så att prestanda för Hive-frågor kan förbättras.

Den här uppgiften är ett steg i den [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Skapa ett Azure storage-konto. Om du behöver anvisningar läser [skapa ett Azure Storage-konto](../../storage/common/storage-quickstart-create-account.md)
* Etablerat en anpassade Hadoop-kluster med HDInsight-tjänsten.  Om du behöver mer information, se [anpassa Azure HDInsight Hadoop-kluster för Advanced Analytics](customize-hadoop-cluster.md).
* Data har överförts till Hive-tabeller i Azure HDInsight Hadoop-kluster. Om den inte har följer [skapa och läsa in data till Hive-tabeller](move-hive-tables.md) först överföra data till Hive-tabeller.
* Aktivera fjärråtkomst till klustret. Om du behöver mer information, se [åt Head noden av Hadoop-klustret](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Funktionen generation
I det här avsnittet beskrivs flera exempel på hur där funktioner kan genererar med hjälp av Hive-frågor. När du har genererat ytterligare funktioner kan du antingen lägga till dem som kolumner i den befintliga tabellen eller skapa en ny tabell med ytterligare funktioner och primär nyckel, vilket kan sedan kopplas till den ursprungliga tabellen. Här följer exempel som visas:

1. [Frekvens-baserad funktion Generation](#hive-frequencyfeature)
2. [Riskerna för Kategoriska variabler i binär klassificering](#hive-riskfeature)
3. [Extrahera funktioner från Datetime Field](#hive-datefeatures)
4. [Extrahera funktioner från Text](#hive-textfeatures)
5. [Beräkna avståndet mellan GPS-koordinater](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Frekvens-baserad funktion generation
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


### <a name="hive-riskfeature"></a>Riskerna för kategoriska variabler i binär klassificering
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

I det här exemplet, variabler `smooth_param1` och `smooth_param2` är inställda på att jämna ut riskvärden som beräknats från data. Risker har ett intervall mellan -Inf och Inf. En risk > 0 anger att sannolikheten att målet är lika med 1 är större än 0,5.

Efter risken beräknas tabell, användare kan tilldela riskvärden till en tabell genom att gå med tabellen risk. Slå samman Hive-frågan har angetts i föregående avsnitt.

### <a name="hive-datefeatures"></a>Extrahera funktioner från datetime-fält
Hive levereras med en uppsättning UDF: er för bearbetning av datetime-fält. I Hive, standardformatet för datum/tid är ”åååå-MM-dd 00:00:00 ' ('1970-01-01 12:21:32, till exempel). Det här avsnittet visas exempel som extraherar dagen i månaden, månaden från ett datetime-fält och andra exempel som konverteras en datetime-sträng i formatet andra än standardformatet till ett datetime-sträng i formatet.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Den här Hive-frågan förutsätter att den  *\<datetime-fält >* är i standardformatet för datum/tid.

Om ett datetime-fält inte är i standardformatet, måste du konvertera datum / tidsfält till Unix-tidsstämpel först och sedan konvertera Unix-tidsstämpel till en datetime-sträng i standardformat. När datum/tid är i formatet kan använda användare embedded datum/tid UDF: er att extrahera funktioner.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

I den här frågan, om den  *\<datetime-fält >* har mönstret som *03/26/2015 12:04:39*,  *\<mönstret för datetime-fält >'* bör vara `'MM/dd/yyyy HH:mm:ss'`. Om du vill testa den kan användare som köra

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

Den *hivesampletable* i den här frågan är förinstallerat på alla Azure HDInsight Hadoop-kluster som standard när klustren etableras.

### <a name="hive-textfeatures"></a>Extrahera funktioner från textfält
När Hive-tabellen har ett textfält som innehåller en ordsträng som avgränsas med blanksteg, extraherar följande fråga längden på strängen och antalet ord i strängen.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>Beräkna avstånd mellan uppsättningar GPS-koordinater
Frågan som anges i det här avsnittet kan tillämpas direkt på NYC Taxi Resedata. Syftet med den här frågan är att visa hur du använder en inbäddad matematisk funktion i Hive för att generera funktioner.

Fälten som används i den här frågan är GPS-koordinater för hämtning och dropoff platser, med namnet *upphämtning\_longitud*, *upphämtning\_latitud*,  *dropoff\_longitud*, och *dropoff\_latitud*. Frågorna som beräknar direkt avståndet mellan koordinaterna hämtning och dropoff är:

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

De matematiska formler som beräknar avståndet mellan två GPS-koordinater kan hittas på den <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">flyttbar typ skript</a> plats, med Peter Lapisu. I den här Javascript, funktionen `toRad()` är bara *lat_or_lon*pi/180, som konverterar grader till radianer. Här kan *lat_or_lon* är latitud och longitud. Eftersom Hive inte ger funktionen `atan2`, men innehåller funktionen `atan`, `atan2` funktion implementeras av `atan` funktion i Hive frågan ovan med hjälp av definitionen i <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Skapa arbetsyta](./media/create-features-hive/atan2new.png)

En fullständig lista över Hive embedded UDF: er finns i den **inbyggda funktioner** avsnittet på den <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="tuning"></a> Avancerade ämnen: Justera Hive parametrar för att fråga snabbare
Standardinställning för Hive-kluster är kanske inte lämpligt för Hive-frågor och data som bearbetar frågor. Det här avsnittet beskrivs vissa parametrar som användare kan justera för att förbättra prestanda för Hive-frågor. Användare måste lägga till parametern justering frågor innan frågor för bearbetning av data.

1. **Java heap utrymme**: För frågor som rör koppla stora datauppsättningar eller bearbetar långa poster **slut på utrymme heap** är en av vanliga fel. Det här felet kan undvikas genom att ange parametrar *mapreduce.map.java.opts* och *mapreduce.task.io.sort.mb* till önskade värden. Här är ett exempel:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Den här parametern allokerar 4GB minne till Java heap utrymme så att det blir sortering effektivare genom att allokera mer minne för den. Det är en bra idé att experimentera med dessa allokeringar om det finns jobb felen som rör heap utrymme.

1. **DFS-blockstorlek**: Den här parametern anger den minsta enheten av data som lagras i filsystemet. Till exempel om DFS-blockstorlek är 128 MB, sedan valfria data med storlek mindre än och upp till lagras 128 MB i ett enda block. Data som är större än 128 MB tilldelas extra block. 
2. Välja en liten blockstorlek gör stora kostnaderna i Hadoop eftersom noden namn har att bearbeta många fler begäranden för att hitta relevant block som hör till filen. En rekommenderad inställning när gäller gigabyte (eller större) data är:

        set dfs.block.size=128m;

2. **Optimera join-åtgärd i Hive**: Medan kopplingsåtgärder inom ramen för map/reduce vanligtvis ägde rum i minska fas, ibland kan enorma vinster uppnås genom att schemalägga kopplingar i fasen karta (kallas även ”mapjoins”). För att dirigera Hive för att göra det möjligt att ange:
   
       set hive.auto.convert.join=true;

3. **Anger antalet Mappningskomponenter till Hive**: Med Hadoop kan användaren att ange antalet reducerare, antalet Mappningskomponenter är vanligtvis inte anges av användaren. Ett tips som gör att en viss grad av kontroll för det här numret är att välja Hadoop-variabler *mapred.min.split.size* och *mapred.max.split.size* som storleken på varje kartan uppgift avgörs av:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normalt standardvärdet:
    
   - *mapred.min.split.size* är 0, för
   - *mapred.Max.split.size* är **Long.MAX** samt för 
   - *DFS.block.size* är 64 MB.

     Som vi kan se, beroende på datastorleken, justera parametrarna genom att ”inställningen” dem kan vi justera antalet Mappningskomponenter används.

4. Här följer några fler **avancerade alternativ** för att optimera prestanda för Hive. Dessa kan du ange det minne som allokerats för att mappa och minska uppgifter och kan vara användbart i justera prestanda. Tänk på att den *mapreduce.reduce.memory.mb* får inte vara större än den fysiska minnesstorleken på varje arbetsnod i Hadoop-kluster.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

