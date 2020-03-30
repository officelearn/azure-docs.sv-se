---
title: Lösa datasnedställning – Azure Data Lake Tools för Visual Studio
description: Felsöka potentiella lösningar för datasnedvridningsproblem med hjälp av Azure Data Lake Tools för Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71802557"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Lösa problem med datasnedställning med hjälp av Azure Data Lake Tools för Visual Studio

## <a name="what-is-data-skew"></a>Vad är datasnedställning?

Kort angiven är datasnedställning ett överrepresenterat värde. Föreställ dig att du har tilldelat 50 skattegranskare för att granska skattedeklarationer, en examinator för varje amerikansk stat. Wyoming examinator, eftersom befolkningen där är liten, har lite att göra. I Kalifornien är dock examinator hålls mycket upptagen på grund av statens stora befolkning.
    ![Exempel på datasnedställningsproblem](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

I vårt scenario är uppgifterna ojämnt fördelade på alla examinatorer, vilket innebär att vissa examinatorer måste arbeta mer än andra. I ditt eget jobb upplever du ofta situationer som skattegranskarens exempel här. Mer tekniskt sett får ett hörn mycket mer data än sina kollegor, en situation som gör att hörnen fungerar mer än de andra och som så småningom saktar ner ett helt jobb. Vad värre är, jobbet kan misslyckas, eftersom hörn kan ha, till exempel, en 5-timmars körning begränsning och en 6 GB minnesbegränsning.

## <a name="resolving-data-skew-problems"></a>Lösa problem med datasnedställning

Azure Data Lake Tools för Visual Studio kan hjälpa dig att identifiera om jobbet har ett problem med datasnedställning. Om det finns ett problem kan du lösa det genom att prova lösningarna i det här avsnittet.

## <a name="solution-1-improve-table-partitioning"></a>Lösning 1: Förbättra tabellpartitionering

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Alternativ 1: Filtrera det skeva nyckelvärdet i förväg

Om det inte påverkar affärslogiken kan du filtrera de högre frekvensvärdena i förväg. Om det till exempel finns många 000-000-000 i kolumn-GUID kanske du inte vill aggregera det värdet. Innan du aggregat kan du skriva "WHERE GUID != "000-000-000"" för att filtrera högfrekvensvärdet.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Alternativ 2: Välj en annan partition eller distributionsnyckel

Om du bara vill kontrollera arbetsbelastningen för skatterevisionen i hela landet/regionen kan du förbättra datadistributionen genom att välja ID-numret som nyckel. Att välja en annan partition eller distributionsnyckel kan ibland fördela data jämnare, men du måste se till att det här valet inte påverkar affärslogiken. Om du till exempel vill beräkna skattesumman för varje delstat kanske du vill ange _tillstånd_ som partitionsnyckel. Om problemet kvarstår kan du prova att använda alternativ 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Alternativ 3: Lägga till fler partitions- eller distributionsnycklar

I stället för att bara använda _tillstånd_ som en partitionsnyckel kan du använda mer än en nyckel för partitionering. Överväg till exempel att lägga till _postnummer_ som en extra partitionsnyckel för att minska datapartitionsstorlekar och fördela data jämnare.

### <a name="option-4-use-round-robin-distribution"></a>Alternativ 4: Använd distribution av rödhake

Om du inte kan hitta en lämplig nyckel för partition och distribution kan du försöka använda distribution av avrundning. Round-robin distribution behandlar alla rader lika och slumpmässigt sätter dem i motsvarande hinkar. Data distribueras jämnt, men den förlorar lokalinformation, en nackdel som också kan minska jobbprestanda för vissa åtgärder. Dessutom, om du gör aggregering för den skeva nyckeln ändå, data-skeva problemet kvarstår. Mer information om distribution av round-robin finns i avsnittet U-SQL Table Distributions i [SKAPA TABELL (U-SQL): Skapa en tabell med schema](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Lösning 2: Förbättra frågeplanen

### <a name="option-1-use-the-create-statistics-statement"></a>Alternativ 1: Använd uttrycket SKAPA STATISTIK

U-SQL tillhandahåller CREATE STATISTICS-uttrycket i tabeller. Den här uttryckssatsen ger mer information till frågeoptimeraren om de dataegenskaper, till exempel värdefördelning, som lagras i en tabell. För de flesta frågor genererar frågeoptimeraren redan nödvändig statistik för en frågeplan av hög kvalitet. Ibland kan du behöva förbättra frågeprestanda genom att skapa ytterligare statistik med SKAPA STATISTIK eller genom att ändra frågedesignen. Mer information finns på sidan [SKAPA STATISTIK (U-SQL).](/u-sql/ddl/statistics/create-statistics)

Exempel på kod:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistikinformationen uppdateras inte automatiskt. Om du uppdaterar data i en tabell utan att återskapa statistiken kan frågeprestandan försämras.

### <a name="option-2-use-skewfactor"></a>Alternativ 2: Använd SKEVHET

Om du vill summera skatten för varje delstat måste du använda delstat GROUP BY, en metod som inte undviker datasnedvringsproblemet. Du kan dock ange en datatips i frågan för att identifiera datasnedställning i nycklar så att optimeraren kan förbereda en körningsplan åt dig.

Vanligtvis kan du ställa in parametern som 0,5 och 1, med 0,5 vilket innebär inte mycket skeva och 1 betyder tung skeva. Eftersom tipset påverkar optimering av körningsplan för den aktuella satsen och alla nedströmssatser måste du lägga till tipset innan den potentiella skeva nyckelvisaggregeringen.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Exempel på kod:

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
Förutom SKEWFACTOR, för specifika skeva nyckel kopplingsfall, om du vet att den andra kopplade raduppsättningen är liten, kan du berätta för optimeraren genom att lägga till en ROWCOUNT-ledtråd i U-SQL-uttrycket före JOIN. På så sätt kan optimizer välja en strategi för broadcast-koppling för att förbättra prestanda. Tänk på att ROWCOUNT inte löser datasnedv-problemet, men det kan ge ytterligare hjälp.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Exempel på kod:

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Lösning 3: Förbättra den användardefinierade reduceraren och sammanslutningsapparaten

Du kan ibland skriva en användardefinierad operator för att hantera komplicerad processlogik, och en välskriven reducerare och combiner kan minska ett datasnedvräckproblem i vissa fall.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Alternativ 1: Använd en rekursiv reducerare, om möjligt

Som standard körs en användardefinierad reducer i icke-rekursivt läge, vilket innebär att minska arbetet för en nyckel distribueras till ett enda hörn. Men om dina data är skeva kan de enorma datauppsättningarna bearbetas i ett enda hörn och köras under lång tid.

Om du vill förbättra prestanda kan du lägga till ett attribut i koden för att definiera reducer för att köras i rekursivt läge. Sedan kan de enorma datauppsättningarna distribueras till flera hörn och köras parallellt, vilket snabbar upp ditt jobb.

Om du vill ändra en icke-rekursiv reducer till rekursiv måste du se till att algoritmen är associativ. Summan är till exempel associativ och medianen är det inte. Du måste också se till att indata och utdata för reducer behåller samma schema.

Attribut för rekursiv reducerare:

    [SqlUserDefinedReducer(IsRecursive = true)]

Exempel på kod:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Alternativ 2: Använd kombinationsläge på radnivå, om möjligt

I likhet med ROWCOUNT-tipset för specifika skeva anslutningsfall försöker kombinerarläget distribuera enorma värden med skeva nyckel till flera hörn så att arbetet kan utföras samtidigt. Combiner-läget kan inte lösa problem med datasnedställning, men det kan ge ytterligare hjälp för enorma värden med sneda nyckeltal.

Som standard är kombinationsläget Fullt, vilket innebär att den vänstra raduppsättningen och den högra raduppsättningen inte kan separeras. Om du ställer in läget som vänster/höger/inre aktiveras kopplingen på radnivå. Systemet separerar motsvarande raduppsättningar och distribuerar dem till flera hörn som körs parallellt. Innan du konfigurerar kombinationsläget bör du dock vara noga med att se till att motsvarande raduppsättningar kan separeras.

Exemplet som följer visar en avgränsad vänsterraduppsättning. Varje utdatarad är beroende av en enda indatarad från vänster, och den kan bero på alla rader från höger med samma nyckelvärde. Om du ställer in kombinerarläget som vänster, separerar systemet den enorma vänsterraden som i små och tilldelar dem till flera hörn.

![Illustration av kombinerarläge](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Om du ställer in fel combiner-läge är kombinationen mindre effektiv och resultatet kan vara fel.

Attribut för kombinerarläge:

- SqlUserDefinedCombiner(Mode=CombinerMode.Full): Varje utdatarad beror eventuellt på alla indatarader från vänster och höger med samma nyckelvärde.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Varje utdatarad beror på en enda indatarad från vänster (och eventuellt alla rader från höger med samma nyckelvärde).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Varje utdatarad beror på en enda indatarad från höger (och eventuellt alla rader från vänster med samma nyckelvärde).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Varje utdatarad beror på en enda indatarad från vänster och höger med samma värde.

Exempel på kod:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
