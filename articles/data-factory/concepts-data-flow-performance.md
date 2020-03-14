---
title: Mappa prestanda-och justerings guide för data flöde
description: Lär dig mer om viktiga faktorer som påverkar prestanda för att mappa data flöden i Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 1a6b50456a5dc3ff89fe7b513f406dc68bd2401e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246297"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Prestanda-och justerings guiden för att mappa data flöden

Att mappa data flöden i Azure Data Factory tillhandahålla ett kod fritt gränssnitt för att utforma, distribuera och dirigera data transformationer i stor skala. Om du inte är bekant med att mappa data flöden kan du läsa [Översikt över kart data flödet](concepts-data-flow-overview.md).

När du utformar och testar data flöden från ADF-UX måste du växla till fel söknings läge för att köra dina data flöden i real tid utan att vänta på att ett kluster ska värmas upp. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

I den här videon visas några exempel på tids inställningar för omvandling av data med data flöden:
> [!VIDEO https://www.youtube.com/watch?v=6CSbWm4lRhw]

## <a name="monitoring-data-flow-performance"></a>Prestanda för övervakning av data flöde

När du skapar mappnings data flöden kan du Unit testa varje omvandling genom att klicka på fliken Data förhands granskning på konfigurations panelen. När du har verifierat din logik kan du testa ditt data flöde från slut punkt till slut punkt som en aktivitet i en pipeline. Lägg till aktiviteten kör data flöde och Använd knappen Felsök för att testa data flödets prestanda. Öppna körnings planen och prestanda profilen för ditt data flöde genom att klicka på glasögon-ikonen under "åtgärder" på fliken utmatning i pipelinen.

![Data flödes övervakare](media/data-flow/mon002.png "Data flödes övervakare 2")

 Du kan använda den här informationen för att uppskatta prestanda för ditt data flöde mot olika data källor. Mer information finns i [övervaka mappning av data flöden](concepts-data-flow-monitoring.md).

![Dataflödesövervakning](media/data-flow/mon003.png "Data flödes övervakare 3")

 För pipeliniska fel söknings körningar krävs en minut i kluster konfigurations tiden i de övergripande prestanda beräkningarna för ett varmt kluster. Om du initierar standard Azure Integration Runtime kan tiden ta cirka 5 minuter.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Ökande beräknings storlek i Azure Integration Runtime

En Integration Runtime med fler kärnor ökar antalet noder i beräknings miljöerna för Spark och ger mer processor kraft för att läsa, skriva och transformera dina data.
* Prova ett **Compute-optimerat** kluster om du vill att din bearbetnings takt ska vara högre än din takt.
* Försök med **ett minnesoptimerade** kluster om du vill cachelagra mer data i minnet. Minnesoptimerade har en högre pris nivå per kärna än beräkning optimerad, men kommer troligen att resultera i snabbare omvandlings hastigheter.

![Ny IR](media/data-flow/ir-new.png "Ny IR")

Mer information om hur du skapar en Integration Runtime finns [i integration runtime i Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Öka storleken på ditt fel söknings kluster

Som standard använder fel söknings programmet standard Azure integration runtime som skapas automatiskt för varje data fabrik. Standardvärdet Azure IR har angetts till åtta kärnor, fyra för en driver-nod och fyra för en arbetsnoden, med hjälp av allmänna beräknings egenskaper. När du testar med större data kan du öka storleken på ditt fel söknings kluster genom att skapa en Azure IR med större konfigurationer och välja den nya Azure IR när du växlar vid fel sökning. Detta instruerar ADF att använda den här Azure IR för för hands versionen av data och pipeline-felsökning med data flöden.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimering för Azure SQL Database och Azure SQL Data Warehouse

### <a name="partitioning-on-source"></a>Partitionering på källa

1. Gå till fliken **optimera** och välj **Ange partitionering**
1. Välj **källa**.
1. Under **antal partitioner**anger du det maximala antalet anslutningar till din Azure SQL-databas. Du kan prova en högre inställning för att få parallella anslutningar till databasen. Vissa fall kan dock leda till snabbare prestanda med ett begränsat antal anslutningar.
1. Välj om du vill partitionera med en speciell tabell kolumn eller en fråga.
1. Om du har valt **kolumn**väljer du kolumnen partition.
1. Om du har valt **fråga**anger du en fråga som matchar partitionerings schemats databas tabell. Med den här frågan kan käll databas motorn utnyttja partition Eli minering. Käll databas tabellerna behöver inte partitioneras. Om källan inte redan är partitionerad använder ADF fortfarande data partitionering i miljön Spark-omvandling baserat på den nyckel som du väljer i käll omvandlingen.

![Käll del](media/data-flow/sourcepart3.png "Käll del")

> [!NOTE]
> En praktisk guide som hjälper dig att välja antalet partitioner för din källa baseras på antalet kärnor som du har angett för din Azure Integration Runtime och multiplicerar det talet med fem. Om du till exempel omvandlar en serie filer i dina ADLS-mappar och du ska använda en Azure IR på 32 kärnor, är antalet partitioner som du skulle använda som mål 32 x 5 = 160 partitioner.

### <a name="source-batch-size-input-and-isolation-level"></a>Käll grupps storlek, Indatatyp och isolerings nivå

Under **käll alternativ** i käll omvandlingen kan följande inställningar påverka prestanda:

* Batch-storlek instruerar ADF att lagra data i mängder i minnet i stället för rad för rad. Batchstorleken är en valfri inställning och du kan få slut på resurser på datornoderna om de inte ändras korrekt.
* Genom att ställa in en fråga kan du filtrera rader på källan innan de anländer till data flödet för bearbetning. Detta kan göra den första data hämtningen snabbare. Om du använder en fråga kan du lägga till valfria frågeuttryck för din Azure SQL DB, till exempel READ uncommitted.
* Vid Läs behörighet får du snabbare frågeresultat om käll omvandling

![Källa](media/data-flow/source4.png "Källa")

### <a name="sink-batch-size"></a>Grupp storlek för mottagare

Om du vill undvika rad-för-rad-bearbetning av dina data flöden ställer du in **batchstorleken** på fliken Inställningar för Azure SQL DB och Azure SQL DW-mottagare. Om batchstorleken anges, bearbetar ADF databas skrivningar i batchar baserat på den storlek som angetts.

![Sjönk](media/data-flow/sink4.png "Kanalmottagare")

### <a name="partitioning-on-sink"></a>Partitionering på handfat

Även om du inte har dina data partitionerade i mål tabellerna rekommenderar vi att du har dina data partitionerade i Sink-omvandlingen. Partitionerade data resulterar ofta i mycket snabbare inläsningar för att tvinga alla anslutningar att använda en enda nod/partition. Gå till fliken optimera i din mottagare och *Välj partitionering med resursallokering för* att välja det idealiska antalet partitioner som ska skrivas till din mottagare.

### <a name="disable-indexes-on-write"></a>Inaktivera index vid skrivning

I din pipeline lägger du till en [lagrad procedur aktivitet](transform-data-using-stored-procedure.md) innan data flödes aktiviteten som inaktiverar index i mål tabellerna som skrivs från din mottagare. Efter din data flödes aktivitet lägger du till en annan lagrad procedur aktivitet som aktiverar dessa index. Eller använda skriptet för för bearbetning och bearbetning efter bearbetning i en databas mottagare.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Öka storleken på din Azure SQL DB och DW

Schemalägg en storleks ändring för din källa och mottagar Azure SQL DB och DW innan din pipeline kör för att öka data flödet och minimera Azure-begränsningen när du når DTU-gränser. När din pipeline-körning har slutförts ändrar du storlek på databaserna till normal körnings frekvens.

* SQL DB-källdokument med 887k-rader och 74-kolumner till en SQL DB-tabell med en enda härledd kolumn-omvandling tar cirka tre minuter från slut punkt till slut punkt med minnesoptimerade 80-Core fel sökning Azure IRs.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Endast Azure Synapse SQL DW] Använd mellanlagring för att läsa in data i bulk via PolyBase

Om du vill undvika rad-för-rad-infogningar i din DW kontrollerar du **Aktivera mellanlagring** i mottagar inställningarna så att ADF kan använda [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase gör att ADF kan läsa in data i bulk.
* När du kör data flödes aktiviteten från en pipeline måste du välja en BLOB eller ADLS Gen2 lagrings plats för att mellanlagra dina data under Mass inläsning.

* Fil källan för 421Mb-filen med 74 kolumner till en Synapse-tabell och en enda härledd kolumn-omvandling tar cirka 4 minuter från slut punkt till slut punkt med minnesoptimerade 80-Core-felsökning Azure IRs.

## <a name="optimizing-for-files"></a>Optimera för filer

Vid varje omvandling kan du ange det partitionerings schema som du vill att Data Factory ska använda på fliken optimera. Det är en bra idé att först testa filbaserade Sinks och behålla standardpartitionering och optimering.

* För mindre filer kan det hända att du väljer färre partitioner kan ibland fungera bättre och snabbare än att be Spark att partitionera dina små filer.
* Om du inte har tillräckligt med information om dina källdata väljer du *resursallokering* partitionering och anger antalet partitioner.
* Om dina data innehåller kolumner som kan vara användbara hash-nycklar väljer du *hash-partitionering*.

* Fil källan med filsink för en 421Mb-fil med 74 kolumner och en enda härledd kolumn-omvandling tar cirka 2 minuter från slut punkt till slut punkt med minnesoptimerade 80-Core-felsökning Azure IRs.

Vid fel sökning i data förhands granskning och fel sökning av pipelinen gäller gräns-och samplings storlekarna för filbaserade käll data uppsättningar endast för det antal rader som returneras, inte antalet rader som läses. Detta kan påverka prestandan för dina fel söknings körningar och möjligen orsaka att flödet slutar fungera.
* Fel söknings kluster är små kluster med en nod som standard och vi rekommenderar att du använder små exempel filer för fel sökning. Gå till fel söknings inställningar och peka på en liten delmängd av dina data med en temporär fil.

    ![Fel söknings inställningar](media/data-flow/debugsettings3.png "Fel söknings inställningar")

### <a name="file-naming-options"></a>Fil namns alternativ

Det vanligaste sättet att skriva transformerade data i mappnings data flöden skriver BLOB eller ADLS File Store. I din mottagare måste du välja en data uppsättning som pekar på en behållare eller mapp, inte en namngiven fil. När data flödet för mappning använder Spark för körning, delas utdata ut över flera filer baserat på ditt partitionerings schema.

Ett gemensamt partitionerings schema är att välja _utdata till en fil_, som sammanfogar alla filer i utdatafilen till en enda fil i din mottagare. Den här åtgärden kräver att utdata minskar till en enda partition på en enskild klusternod. Du kan ta bort resurser för klusternoden om du kombinerar många stora källfiler till en enda utdatafil.

För att undvika att beräkna resurser för beräknings resurser, Behåll standardvärdet optimerat schema i data flöde och Lägg till en kopierings aktivitet i din pipeline som sammanfogar alla delfiler från mappen utdata till en ny fil. Den här tekniken separerar åtgärden för omvandling från fil sammanslagning och ger samma resultat som _att ange utdata till en enskild fil_.

### <a name="looping-through-file-lists"></a>Loopa igenom fil listor

Ett data flöde för mappning kommer att köras bättre när käll omvandlingen upprepas över flera filer i stället för slingor via för varje aktivitet. Vi rekommenderar att du använder jokertecken eller fil listor i din käll omvandling. Data flödes processen körs snabbare genom att tillåta att slingor uppstår i Spark-klustret. Mer information finns i [jokertecken i käll omvandling](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Om du till exempel har en lista med datafiler från 2019 juli som du vill bearbeta i en mapp i Blob Storage, kan du använda ett jokertecken i din käll omvandling.

```DateFiles/*_201907*.txt```

Genom att använda jokertecken innehåller pipelinen bara en data flödes aktivitet. Detta kommer att utföra bättre än en sökning mot BLOB Store som sedan itererar över alla matchade filer med hjälp av en förvarsin aktivitet med data flödes aktiviteten Kör i.

### <a name="optimizing-for-cosmosdb"></a>Optimering för CosmosDB

Inställning av data flödes-och batch-egenskaper på CosmosDB-mottagare börjar bara gälla när data flödet körs från en pipeline-dataflöde-aktivitet. De ursprungliga samlings inställningarna kommer att hanteras av CosmosDB när data flödet har körts.

* Batchstorlek: beräkna den grova rad storleken för dina data och se till att rowSize * batchstorleken är mindre än 2 000 000. Om så är fallet ökar du batchstorleken för att få bättre data flöde
* Data flöde: Ange en högre data flödes inställning här så att dokument kan skrivas snabbare till CosmosDB. Kom ihåg de högre RU-kostnaderna baserat på en hög data flödes inställning.
*   Budget för Skriv data flöde: Använd ett värde som är mindre än det totala antalet ru: er per minut. Om du har ett data flöde med ett stort antal Spark-partitioner, kan du ange en budget genom strömning för att öka balansen mellan dessa partitioner.

## <a name="join-performance"></a>Anslut till prestanda

Att hantera prestanda för kopplingar i ditt data flöde är en mycket vanlig åtgärd som du kommer att utföra under hela livs cykeln för dina data transformationer. I ADF kräver data flöden inte data som ska sorteras före koppling när de här åtgärderna utförs som hash-kopplingar i Spark. Du kan dock dra nytta av förbättrad prestanda med anslutnings optimeringen "sändning". Detta undviker att blanda genom att trycka ned innehållet på någon av de båda sidorna av kopplings relationen till Spark-noden. Detta fungerar bra för mindre tabeller som används för referens sökningar. Större tabeller som kanske inte passar i nodens minne är inte lämpliga kandidater för sändnings optimering.

En annan kopplings optimering är att bygga dina kopplingar på ett sådant sätt att det gör att Spark är tendenser att implementera kors kopplingar. Om du till exempel inkluderar litterala värden i dina kopplings villkor kan Spark se att som ett krav för att utföra en fullständig kartesiska-produkt först och sedan filtrera ut de kopplade värdena. Men om du ser till att du har kolumn värden på båda sidor om ditt kopplings villkor kan du undvika den här Spark-inducerade kartesiska-produkten och förbättra prestanda för dina anslutningar och data flöden.

## <a name="next-steps"></a>Nästa steg

Se andra data flödes artiklar relaterade till prestanda:

- [Fliken optimera data flöde](concepts-data-flow-overview.md#optimize)
- [Data flödes aktivitet](control-flow-execute-data-flow-activity.md)
- [Övervaka data flödes prestanda](concepts-data-flow-monitoring.md)
