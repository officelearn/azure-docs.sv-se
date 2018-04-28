---
title: Lösa problem med en förskjutning av data med hjälp av Azure Data Lake-verktyg för Visual Studio | Microsoft Docs
description: Felsökning av möjliga lösningar för data-förskjutning av problem med Azure Data Lake-verktyg för Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: ''
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
ms.openlocfilehash: 2e1d33b5d2392832899fd30636e9d40231fc74ee
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Lösa problem med en förskjutning av data med hjälp av Azure Data Lake-verktyg för Visual Studio

## <a name="what-is-data-skew"></a>Vad är data skeva?

Kort anges är data förskjutning ett mycket representeras värde. Anta att du har tilldelat 50 skatt granskarna att granska deklarationer, en förarprövaren för varje USA tillstånd. Granskaren Wyoming har eftersom det populationen är liten, liten eller göra. I California, men sparas granskaren hårt på grund av stora ifyllning av det tillståndet.
    ![Förskjutning av data problemet exempel](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

I vårt scenario, är data ojämnt fördelad över alla skatt granskare, vilket innebär att vissa granskare måste arbeta mer än andra. Situationer som exempel skatt förarprövaren här uppstår ofta i dina egna jobbet. Hämtar mycket mer data än dess peer-datorer för ett hörn i mer tekniska villkor, en situation som gör vertex arbeta mer än andra och som slutligen långsammare ett hela jobb. Vad är försämras kan jobbet misslyckas eftersom formhörnen kanske till exempel en begränsning av 5 timmar runtime och en begränsning med 6 GB minne.

## <a name="resolving-data-skew-problems"></a>Lösa problem för förskjutning av data

Azure Data Lake-verktyg för Visual Studio kan hjälpa dig att upptäcka om jobbet har problem med en förskjutning av data. Om det finns ett problem, kan du lösa det. genom lösningarna i det här avsnittet.

## <a name="solution-1-improve-table-partitioning"></a>Lösning 1: Förbättra Tabellpartitionering

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Alternativ 1: Filtrera skeva nyckelvärdet i förväg

Om det inte påverkar affärslogik, kan du filtrera högre frekvens värden i förväg. Om det finns en mängd 000 000 000 i kolumnen GUID, kan du t.ex, inte vill att sammanställa värdet. Innan du sammanställd, du kan skriva ”var GUID! =” 000 – 000 000 ”” till filtervärde hög frekvens.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Alternativ 2: Välj en annan partition eller distribution nyckel

I föregående exempel, om du vill bara kontrollera skatt audit arbetsbelastningen över hela landet, kan du förbättra Datadistributionen genom att välja ett ID-nummer som din nyckel. Välja en annan partition eller distribution tangent kan ibland distribuera data jämnare, men du måste se till att detta val inte påverkar din affärslogik. Till exempel för att beräkna moms summan för respektive tillstånd, kanske du vill ange _tillstånd_ som partitionsnyckel. Om du fortsätter att problemet kvarstår kan du prova att använda alternativ 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Alternativ 3: Lägg till flera nycklar för partitionen eller distribution

I stället för endast _tillstånd_ som en partitionsnyckel kan du använda mer än en nyckel för partitionering. Anta till exempel att lägga till _postnummer_ som en ytterligare partitionsnyckel att minska data partitionsstorlek och distribuera data mer jämnt.

### <a name="option-4-use-round-robin-distribution"></a>Alternativ 4: Använd resursallokering

Om du inte hittar en lämplig nyckel för partition och distribution, kan du försöka använda resursallokering. Resursallokering behandlas alla rader lika och placerar slumpmässigt dem i motsvarande buckets. Hämtar data jämnt fördelad, men det förlorar ort information nackdelen som kan också minska jobbet prestandan för vissa åtgärder. Om du gör sammanställning för nyckeln skeva ändå, behålls dessutom problemet förskjutning av data. Mer information om resursallokering finns i distributioner för U-SQL-tabellen i [CREATE TABLE (U-SQL): skapa en tabell med schemat](https://msdn.microsoft.com/library/mt706196.aspx#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Lösning 2: Förbättra frågeplanen

### <a name="option-1-use-the-create-statistics-statement"></a>Alternativ 1: Använd instruktionen CREATE STATISTICS

U-SQL ger instruktionen CREATE STATISTICS på tabeller. Den här instruktionen ger mer information till Frågeoptimeringen om, dataegenskaper som till exempel värdet distribution, som lagras i en tabell. För de flesta frågor genererar Frågeoptimeringen redan nödvändiga statistik för en frågeplan med hög kvalitet. Ibland kan behöva du förbättra frågeprestanda genom att skapa ytterligare statistik med CREATE STATISTICS eller genom att ändra frågans design. Mer information finns i [CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/library/azure/mt771898.aspx) sidan.

Exempel:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistikinformation uppdateras inte automatiskt. Om du uppdaterar data i en tabell utan att återskapa statistik kan frågeprestandan neka.

### <a name="option-2-use-skewfactor"></a>Alternativ 2: Använd SKEWFACTOR

Om du vill beräkna moms för varje tillstånd måste du använda GROUP BY tillstånd, en metod som inte undviker problemet förskjutning av data. Men kan du ange en ledtråd för data i din fråga att identifiera data förskjutning i nycklar så att optimering kan förbereda en plan för körning av du.

Vanligtvis kan du ange parametern som 0,5 och 1, med 0,5 vilket innebär inte mycket skeva och 1 betydelse tunga förskjutning. Eftersom tipset påverkar åtgärdsplan optimering för den aktuella instruktionen och alla underordnade instruktioner, måste du lägga till tipset innan risken förvrängd key-wise aggregering.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Exempel:

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
Förutom SKEWFACTOR ser för specifika förvrängd nyckeln koppling fall om du vet att den andra uppsättningen för domänanslutna raden är liten, du optimering genom att lägga till ett RADANTAL tips i U-SQL-instruktionen innan koppling. Det här sättet optimering kan välja en broadcast join-strategi för att förbättra prestanda. Tänk på att ROWCOUNT inte löser problemet förskjutning av data, men den kan erbjuda ytterligare hjälp.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Exempel:

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

Ibland kan du skriva en användardefinierad operatorn för att hantera komplicerad Processlogik och en bra reducer och combiner kan minska problem förskjutning av data i vissa fall.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Alternativ 1: Använd om möjligt en rekursiv reducer

Som standard körs en användardefinierad reducer i icke-rekursiva läge, vilket innebär att minskar arbete för en nyckel har distribuerats till en enda nod. Men om dina data är förvrängd stora datauppsättningar kan bearbetas i en enda nod och kör under lång tid.

Du kan lägga till ett attribut i koden för att definiera reducer för rekursiv läget för att förbättra prestanda. Stora datauppsättningar kan sedan distribueras till flera formhörnen och köras parallellt, vilket förbättrar ditt jobb.

Om du vill ändra en icke-rekursiva reducer rekursiva, måste du se till att din algoritmen är kopplat. Till exempel summan är kopplat och medianvärdet är inte. Du måste också kontrollera att indata och utdata för reducer behåller samma schema.

Attributet för rekursiv reducer:

    [SqlUserDefinedReducer(IsRecursive = true)]

Exempel:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Alternativ 2: Använd om möjligt radnivå combiner läge

Liknar ROWCOUNT tips för specifika förvrängd nyckeln koppling fall, combiner läge försöker distribuera stora förvrängd nyckel värde anger att flera formhörnen så att arbetet som kan köras samtidigt. Combiner läge inte kan lösa problem med förskjutning av data, men den kan erbjuda för stora förvrängd nyckel värde anger ytterligare hjälp.

Som standard är combiner läget Full, vilket innebär att den raden vänstra och högra raden uppsättningen inte kan skiljas. Anger läget som inre-vänster/höger kan radnivå koppling. Systemet avgränsar motsvarande rad uppsättningar och distribuerar dem till flera formhörnen som körs parallellt. Innan du konfigurerar combiner läge, Tänk på att säkerställa att motsvarande rad uppsättningar kan delas.

Följande exempel visar en uppsättning åtskilda vänstra raden. Varje rad i utdata beror på en inkommande rad från vänster, och eventuellt på alla rader från höger med samma nyckelvärde. Om du anger combiner-läge som vänster systemet avgränsar enorma vänster-raden i små och tilldelar dem till flera formhörnen.

![Combiner läge bild](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Om du har angett fel combiner läget kombinationen är mindre effektivt och resultaten kan vara felaktig.

Attribut för combiner läge:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Varje rad i utdata beror på en rad från vänster (och potentiellt alla rader från höger med samma nyckelvärde) som indata.

- qlUserDefinedCombiner(Mode=CombinerMode.Right): varje rad i utdata är beroende av en rad från höger (och potentiellt alla rader från vänster med samma nyckelvärde) som indata.

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Varje rad i utdata beror på en rad från vänster och höger med samma värde som indata.

Exempel:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
