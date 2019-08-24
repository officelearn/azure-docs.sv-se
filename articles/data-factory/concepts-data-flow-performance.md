---
title: Kartlägger prestanda-och justerings guiden för data flöde i Azure Data Factory | Microsoft Docs
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data flöden i Azure Data Factory när du använder mappnings data flöden.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 8eb244a0eff1569ac27feae68104db613373463a
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992360"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Prestanda-och justerings guiden för att mappa data flöden

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mappa data flöden innehåller ett kod fritt webb läsar gränssnitt för att utforma, distribuera och dirigera data transformationer i skala.

> [!NOTE]
> Om du inte är bekant med ADF-mappning av data flöden i allmänhet, se [Översikt över data flöden](concepts-data-flow-overview.md) innan du läser den här artikeln.
>

> [!NOTE]
> När du utformar och testar data flöden från ADF-gränssnittet, se till att aktivera fel söknings växeln så att du kan köra dina data flöden i real tid utan att vänta på att ett kluster ska värmas upp.
>

![Knappen Felsök](media/data-flow/debugb1.png "Felsök")

## <a name="monitor-data-flow-performance"></a>Övervaka data flödes prestanda

När du designar dina mappnings data flöden i webbläsaren kan du testa varje enskild omvandling genom att klicka på fliken Data förhands granskning i det nedre inställnings fönstret för varje omvandling. Nästa steg du bör vidta är att testa ditt data flöde från slut punkt till slut punkt i pipeline-designern. Lägg till aktiviteten kör data flöde och Använd knappen Felsök för att testa data flödets prestanda. I det nedre fönstret i pipeline-fönstret ser du en eyeglass-ikon under "åtgärder":

![Data flödes övervakare](media/data-flow/mon002.png "Data flödes övervakare 2")

Om du klickar på ikonen visas körnings planen och efterföljande prestanda profiler för ditt data flöde. Du kan använda den här informationen för att uppskatta prestanda för ditt data flöde mot olika storleks data källor. Observera att du kan anta att du tar med 1 minut kluster jobb körnings konfiguration i dina övergripande prestanda beräkningar och om du använder standard Azure Integration Runtime kan du behöva lägga till fem minuter klustrets uppslags tid.

![Övervakning av data flöde](media/data-flow/mon003.png "Data flödes övervakare 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimering för Azure SQL Database och Azure SQL Data Warehouse

![Käll del](media/data-flow/sourcepart3.png "Käll del")

### <a name="partition-your-source-data"></a>Partitionera dina källdata

* Gå till "optimera" och välj "källa". Ange antingen en enskild tabell kolumn eller en typ i en fråga.
* Välj kolumnen partition om du väljer kolumn.
* Ange också det maximala antalet anslutningar till din Azure SQL-databas. Du kan prova en högre inställning för att få parallella anslutningar till databasen. Vissa fall kan dock leda till snabbare prestanda med ett begränsat antal anslutningar.
* Käll databas tabellerna behöver inte vara partitionerade.
* Om du ställer in en fråga i din käll omvandling som matchar partitionerings schemat i databas tabellen kan käll databas motorn utnyttja partition Eli minering.
* Om källan inte redan är partitionerad använder ADF fortfarande data partitionering i miljön Spark-omvandling baserat på den nyckel som du väljer i käll omvandlingen.

### <a name="set-batch-size-and-query-on-source"></a>Ange batchstorlek och fråga för källa

![Källa](media/data-flow/source4.png "Källa")

* Om du ställer in batch-storlek instrueras ADF att lagra data i mängder i minnet i stället för rad för rad. Det är en valfri inställning och du kan få slut på resurser på datornoderna om de inte har rätt storlek.
* Genom att ställa in en fråga kan du filtrera rader direkt vid källan innan de tar emot data flödet för bearbetning, vilket kan göra det första data förvärvet snabbare.
* Om du använder en fråga kan du lägga till valfria frågeuttryck för din Azure SQL DB, t. ex. Läs behörighet

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Ange isolerings nivå för käll omvandlings inställningar för SQL-datauppsättningar

* Vid Läs behörighet får du snabbare frågeresultat om käll omvandling

![Isolerings nivå](media/data-flow/isolationlevel.png "Isolerings nivå")

### <a name="set-sink-batch-size"></a>Ange grupp storlek för mottagare

![Mottagare](media/data-flow/sink4.png "Mottagare")

* För att undvika rad-för-rad-bearbetning av dina data flöden anger du "batchstorlek" i mottagar inställningarna för Azure SQL DB. Detta meddelar ADF att bearbeta databas skrivningar i batchar baserat på den angivna storleken.

### <a name="set-partitioning-options-on-your-sink"></a>Ange partitionerings alternativ för din mottagare

* Även om du inte har dina data partitionerade i dina mål Azure SQL DB-tabeller går du till fliken optimera och ställer in partitionering.
* Det är mycket ofta att du bara berättar ADF för att använda resursallokering i Spark-kluster, vilket resulterar i mycket snabbare data inläsning i stället för att tvinga alla anslutningar från en enda nod/partition.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Öka storleken på din beräknings motor i Azure Integration Runtime

![Ny IR](media/data-flow/ir-new.png "Ny IR")

* Öka antalet kärnor, vilket ökar antalet noder och ger dig mer processor kraft för att fråga och skriva till din Azure SQL-databas.
* Prova alternativen "Compute Optimized" och "Minnesoptimerade" för att lägga till fler resurser till dina datornoder.

### <a name="unit-test-and-performance-test-with-debug"></a>Test av enhets test och prestanda med fel sökning

* När enhets test data flödar anger du knappen "fel sökning av data Flow" till på.
* I data flödes designern använder du fliken Data förhands granskning i omvandlingar för att visa resultatet av din omvandlings logik.
* Enhet testa dina data flödar från pipeline-designern genom att placera en data flödes aktivitet på pipelinens design arbets yta och använda knappen "Felsök" för att testa.
* Testning i fel söknings läge fungerar mot en aktiv kluster miljö utan att behöva vänta på ett just-in-Time-kluster.

### <a name="disable-indexes-on-write"></a>Inaktivera index vid skrivning
* Använd en lagrad procedur i ADF-pipeline innan data flödes aktiviteten som inaktiverar index i mål tabellerna som skrivs till från din mottagare.
* Efter din data flödes aktivitet lägger du till en annan lagrad proc-aktivitet som aktiverade dessa index.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Öka storleken på din Azure SQL DB
* Schemalägg en storleks ändring för din källa och mottagare av Azure SQL DB innan du kör din pipeline för att öka data flödet och minimera Azure-begränsningen när du når DTU-gränser.
* När din pipeline-körning har slutförts kan du ändra storlek på databaserna till normal körnings hastighet.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optimering för Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Använd mellanlagring för att läsa in data i bulk via PolyBase

* För att undvika rad-för-rad-bearbetning av dina data flöden ställer du in alternativet "mellanlagring" i mottagar inställningarna så att ADF kan använda PolyBase för att undvika infogningar rad för rad i DW. Detta gör att ADF använder PolyBase så att data kan läsas in i bulk.
* När du kör data flödes aktiviteten från en pipeline och mellanlagringen är aktive rad måste du välja BLOB Store-platsen för dina mellanlagrings data för Mass inläsning.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Öka storleken på din Azure SQL DW

* Schemalägg en storleks ändring av källan och sinka Azure SQL DW innan du kör din pipeline för att öka data flödet och minimera Azure-begränsningen när du når DWU-gränser.

* När din pipeline-körning har slutförts kan du ändra storlek på databaserna till normal körnings hastighet.

## <a name="optimize-for-files"></a>Optimera för filer

* Du kan kontrol lera hur många partitioner som används i ADF. På varje källa för källa & Sink, och varje enskild omvandling, kan du ange ett partitionerings schema. För mindre filer kan det hända att du kan välja "enskild partition" och kan fungera bättre och snabbare än att be Spark att partitionera dina små filer.
* Om du inte har tillräckligt med information om dina källdata kan du välja partitionering med resursallokering (Round Robin) och ange antalet partitioner.
* Om du utforskar dina data och märker att du har kolumner som kan vara användbara hash-nycklar använder du alternativet för hash-partitionering.
* När du felsöker i data förhands granskning och fel sökning av pipeline, Observera att gräns-och provtagnings storlekarna för filbaserade käll data uppsättningar endast gäller för det antal rader som returneras, inte antalet rader som läses. Detta är viktigt att observera eftersom det kan påverka prestandan för dina fel söknings körningar och möjligen orsaka att flödet slutar fungera.
* Kom ihåg att fel söknings kluster är små kluster med en nod som standard, så Använd tillfälliga små filer för fel sökning. Gå till fel söknings inställningar och peka på en liten delmängd av dina data med en temporär fil.

![Fel söknings inställningar](media/data-flow/debugsettings3.png "Fel söknings inställningar")

### <a name="file-naming-options"></a>Fil namns alternativ

* Standard typen för skrivning av transformerade data i data flöden för ADF-mappning är att skriva till en data uppsättning som har en länkad BLOB-eller ADLS-tjänst. Du bör ange att data uppsättningen ska peka till en mapp eller behållare, inte en namngiven fil.
* Data flöden använder Azure Databricks Spark för körning, vilket innebär att dina utdata delas upp över flera filer baserat på antingen standard-Spark-partitionering eller partitionerings schema som du uttryckligen har valt.
* En mycket vanlig åtgärd i ADF-dataflöden är att välja "utdata till en enskild fil" så att alla dina utdatafiler slås samman i en enda utdatafil.
* Den här åtgärden kräver dock att utdata minskar till en enda partition på en enskild klusternod.
* Tänk på detta när du väljer det här populära alternativet. Du kan ta bort resurser för klusternoder om du kombinerar många stora källfiler till en partition med en utdatafil.
* Om du vill undvika att beräkna resurser för Compute-noden, kan du behålla standardvärdet eller explicit partitionerings schema i ADF, vilket optimerar prestanda och lägger sedan till en efterföljande kopierings aktivitet i pipelinen som sammanfogar alla delfiler från mappen utdata till en ny enskild Arkiv. I grunden separerar den här tekniken omvandlingen av åtgärder från fil sammanslagning och ger samma resultat som inställningen "utdata till en enskild fil".

### <a name="looping-through-file-lists"></a>Loopa igenom fil listor

I de flesta fall kommer data flöden i ADF att köras bättre från en pipeline som gör det möjligt för data flödes källans omvandling att iterera över flera filer. Med andra ord är det lämpligt att använda jokertecken eller fil listor i din källa i data flödet än att iterera över en stor lista med filer med hjälp av förloppet i pipelinen och anropa ett kör data flöde på varje iteration. Data flödes processen körs snabbare genom att tillåta att loopen inträffar i data flödet.

Om jag till exempel har en lista med datafiler från 2019 juli som jag vill bearbeta i en mapp i Blob Storage, skulle det vara mer bra att anropa en kör data flödes aktivitet en gång från din pipeline och använda ett jokertecken i din källa som detta :

```DateFiles/*_201907*.txt```

Detta kommer att utföra bättre än en sökning mot BLOB-arkivet i en pipeline som sedan itererar över alla matchade filer med hjälp av en försvarad aktivitet med data flödes aktiviteten Kör i.

## <a name="next-steps"></a>Nästa steg

Se andra data flödes artiklar relaterade till prestanda:

- [Fliken optimera data flöde](concepts-data-flow-optimize-tab.md)
- [Data flödes aktivitet](control-flow-execute-data-flow-activity.md)
- [Övervaka data flödes prestanda](concepts-data-flow-monitoring.md)
