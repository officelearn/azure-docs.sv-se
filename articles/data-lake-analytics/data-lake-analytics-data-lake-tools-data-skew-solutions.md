---
title: Lösa datasnedställning problem med hjälp av Azure Data Lake Tools för Visual Studio
description: Felsökning av möjliga lösningar för datasnedställning problem med hjälp av Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: af55c161944447f2e6e2245fbb920803779984ca
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496205"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Lösa datasnedställning problem med hjälp av Azure Data Lake Tools för Visual Studio

## <a name="what-is-data-skew"></a>Vad är data förskjuta?

Datasnedställning är kort anges, ett felaktigt representeras värde. Anta att du har tilldelat 50 skatt granskarna att granska skattedeklarationer, en förarprövaren för respektive tillstånd i USA. Granskaren Wyoming har eftersom det populationen är liten, lite eller göra. I Kalifornien dock sparas granskaren hårt belastad på grund av stora regionbefolkning.
    ![Problem med datasnedställning exempel](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

I vårt scenario, är data ojämnt fördelade mellan alla skatt granskare, vilket innebär att vissa granskare måste arbetar mer än andra. Situationer som skatt förarprövaren exemplet här uppstår ofta i dina egna jobb. Mer teknisk uttryckt ett hörn får mycket mer data än dess peer-datorer, en situation som gör hörnet arbeta mer än de andra och att så småningom saktar ned en hela jobbet. Vad är sämre, kan jobbet misslyckas, eftersom hörn kanske till exempel en 5 timmar runtime-begränsning och en begränsning med 6 GB minne.

## <a name="resolving-data-skew-problems"></a>Lösa datasnedställning problem

Azure Data Lake Tools för Visual Studio kan hjälpa dig att identifiera om jobbet har ett datasnedställning problem. Om det finns ett problem, kan du lösa det genom att testa lösningar i det här avsnittet.

## <a name="solution-1-improve-table-partitioning"></a>Lösning 1: Förbättra Tabellpartitionering

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Alternativ 1: Filtrera skeva nyckelvärdet i förväg

Om den inte påverkar din affärslogik, kan du filtrera högre frekvens värdena i förväg. Till exempel om det finns en massa 000 000 000 i kolumnen GUID kan kanske du inte vill aggregera detta värde. Innan du aggregera, du kan skriva ”var GUID! =” 000 – 000 000 ”” till filtervärde hög frekvens.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Alternativ 2: Välj en annan partition eller distribution nyckel

I föregående exempel, om du vill bara kontrollera skatt granskning arbetsbelastningen över hela landet kan du förbättra Datadistribution genom att välja ID-numret som din nyckel. Välja en annan partition eller distributionsnyckeln kan ibland distribuera data jämnare, men du måste se till att det här valet inte påverkar din affärslogik. Till exempel för att beräkna moms summan för respektive tillstånd, kanske du vill ange _tillstånd_ som partitionsnyckel. Om du fortsätter att det här problemet uppstår kan du använda alternativ 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Alternativ 3: Lägg till flera nycklar för partition eller distribution

I stället för att endast använda _tillstånd_ som en partitionsnyckel, kan du använda fler än en nyckel för partitionering. Exempel: Överväg att lägga till _postnummer_ som en ytterligare partitionsnyckel för att minska data partitionsstorlekarna och distribuera data jämnare.

### <a name="option-4-use-round-robin-distribution"></a>Alternativ 4: Använd resursallokering

Om du inte hittar en lämplig nyckel för partition och distribution, kan du försöka använda resursallokering. Resursallokering behandlas alla rader som likvärdiga och placerar slumpmässigt dem i motsvarande buckets. Data fördelas jämnt, men det förlorar även information, en nackdel som kan också minska jobbet prestanda för vissa åtgärder. Om du genomför aggregering för nyckeln skeva ändå, behålls dessutom datasnedställning problemet. Mer information om tekniken finns i avsnittet distributioner för U-SQL-tabell i [CREATE TABLE (U-SQL): Skapa en tabell med schemat](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Lösning 2: Förbättra frågeplanen

### <a name="option-1-use-the-create-statistics-statement"></a>Alternativ 1: Använd instruktionen CREATE STATISTICS

U-SQL innehåller instruktionen CREATE STATISTICS för tabeller. Den här instruktionen ger mer information till Frågeoptimeringen om, dataegenskaper, till exempel värdedistribution, som lagras i en tabell. För de flesta frågor genererar Frågeoptimeringen redan nödvändig statistik för en frågeplan med hög kvalitet. Ibland kan behöva du förbättra frågeprestanda genom att skapa ytterligare statistik med CREATE STATISTICS eller genom att ändra frågans design. Mer information finns i den [CREATE STATISTICS (U-SQL)](/u-sql/ddl/statistics/create-statistics) sidan.

Kodexempel:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistikinformationen uppdateras inte automatiskt. Om du uppdaterar data i en tabell utan att återskapa statistik, skulle kunna minska prestanda för frågor.

### <a name="option-2-use-skewfactor"></a>Alternativ 2: Använd SKEWFACTOR

Om du vill beräkna moms för respektive tillstånd måste du använda GROUP BY tillstånd, en metod som inte undviker datasnedställning problem. Du kan dock ange en ledtråd för data i din fråga för att identifiera datasnedställning i nycklar så att optimering kan förbereda en Körningsplan för dig.

Vanligtvis kan du ange parametern som 0,5 och 1, med 0,5 vilket innebär inte mycket skeva och 1 betydelse tung skeva. Eftersom tipset påverkar Körningsplan optimering för den aktuella instruktionen och alla underordnade instruktioner, måste du lägga till tipset innan potentialen förvrängd key-wise aggregering.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Kodexempel:

    //Add a SKEWFACTOR hint.
    @Impressions =
        SELECT * FROM
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions =
        SELECT
            ClientId,
            Query,
            SUM(PageClicks) AS Clicks
        FROM
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    //Query 2 for Key: Query
    @Display =
        SELECT * FROM @Sessions
            INNER JOIN @Campaigns
                ON @Sessions.Query == @Campaigns.Query
        ;   

### <a name="option-3-use-rowcount"></a>Alternativ 3: Använd ROWCOUNT  
Förutom SKEWFACTOR berätta specifika förvrängd nyckel join fall, om du vet att den andra uppsättningen av domänanslutna raden är liten, optimeraren beror genom att lägga till ett RADANTAL tips i U-SQL-instruktionen före sammanfogning. På så sätt kan optimering kan välja en broadcast join-strategi för att förbättra prestanda. Tänk på att ROWCOUNT löses inte problemet datasnedställning, men den kan erbjuda ytterligare hjälp.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Kodexempel:

    //Unstructured (24-hour daily log impressions)
    @Huge   = EXTRACT ClientId int, ...
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    //Small subset (that is, ForgetMe opt out)
    @Small  = SELECT * FROM @Huge
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    //Result (not enough information to determine simple broadcast JOIN)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Lösning 3: Förbättra användardefinierade reducer och combiner

Ibland kan du skriva en användardefinierad operator utan komplicerad logik och en välskriven reducer och combiner kan minska datasnedställning problem i vissa fall.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Alternativ 1: Använda en rekursiv reducer om möjligt

Som standard körs en användardefinierad reducer i icke-rekursiva läge, vilket innebär att minskar arbetet för en nyckel som ska distribueras till en enskild brytpunkt. Men om dina data är förvrängd enorma datauppsättningar kan bearbetas i en enskild brytpunkt och kör under en längre tid.

Du kan lägga till ett attribut i din kod för att definiera reducer körs med rekursiv för att förbättra prestanda. Stora datamängder kan sedan distribueras till flera hörn och köra parallellt, vilket påskyndar arbetet.

Om du vill ändra en icke-rekursiva reducer till rekursiv, måste du se till att din algoritmen är kopplat. Summan är kopplat till exempel och medianvärdet är inte. Du måste också kontrollera att indata och utdata för reducer behåller samma schema.

Attributet för rekursiv reducer:

    [SqlUserDefinedReducer(IsRecursive = true)]

Kodexempel:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Alternativ 2: Använd om möjligt på radnivå combiner läge

Liknar ROWCOUNT tips för specifika förvrängd nyckel join fall, combiner läge försöker distribuera stora förvrängd nyckel-värde anger att flera hörn så att arbetet som kan köras samtidigt. Combiner läge inte kan lösa datasnedställning problem, men den kan erbjuda ytterligare hjälp för stor påverkas av nyckel-värde.

Som standard är fullständig, vilket innebär att den vänstra raden och högra raden inte kan separeras i combiner-läge. Anger läget som vänster/höger/inre kan på radnivå join. Systemet skiljer motsvarande rad uppsättningar och distribuerar dem till flera hörn som körs parallellt. Men innan du konfigurerar combiner-läge kan vara noga med att se till att motsvarande rad uppsättningarna kan delas upp.

Följande exempel visar en uppsättning avgränsas vänstra raden. Varje rad i utdata beror på en rad från vänster som indata och det beror potentiellt på alla rader från höger med samma nyckelvärde. Om du ställer in combiner-läge som vänster systemet skiljer stor vänster-raden i små och tilldelar dem till flera hörn.

![Combiner läge bild](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Om du har angett fel combiner läget kombinationen är mindre effektivt och resultaten kan vara fel.

Attribut för combiner läge:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Varje rad i utdata är beroende av en rad från vänster (och eventuellt alla rader från höger med samma nyckelvärde) som indata.

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Varje rad i utdata är beroende av en rad från höger (och eventuellt alla rader från vänster med samma nyckelvärde) som indata.

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Varje rad i utdata är beroende av en rad från vänster och höger med samma värde som indata.

Kodexempel:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
