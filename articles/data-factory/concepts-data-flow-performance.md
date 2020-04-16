---
title: Mappa dataflödesprestanda och justeringsguide
description: Lär dig mer om viktiga faktorer som påverkar prestanda för att mappa dataflöden i Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: c09a035c8994118b0fb116f357485766e05883ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418447"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mappa dataflödens prestanda och justeringsguide

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mappning av dataflöden i Azure Data Factory ger ett kodfritt gränssnitt för att utforma, distribuera och dirigera dataomvandlingar i stor skala. Om du inte är bekant med mappning av dataflöden läser du [översikten över dataflöde för mappning](concepts-data-flow-overview.md).

När du utformar och testar dataflöden från ADF UX, se till att slå på felsökningsläge för att köra dina dataflöden i realtid utan att vänta på att ett kluster ska värmas upp. Mer information finns i [Felsökningsläge](concepts-data-flow-debug-mode.md).

I det här videoklippet visas några exempeltider som omvandlar data med dataflöden:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Övervaka dataflödesprestanda

När du utformar mappningsdataflöden kan du enhetstesta varje omvandling genom att klicka på fliken förhandsgranskning av data på konfigurationspanelen. När du har verifierat logiken testar du dataflödet från på nytt som en aktivitet i en pipeline. Lägg till en körningsdataflödesaktivitet och använd knappen Felsökning för att testa dataflödets prestanda. Om du vill öppna körningsplanen och prestandaprofilen för ditt dataflöde klickar du på ikonen glasögon under "åtgärder" på utdatafliken för din pipeline.

![Övervakare av dataflöde](media/data-flow/mon002.png "Övervakare för dataflöde 2")

 Du kan använda den här informationen för att uppskatta dataflödets prestanda mot datakällor av olika storlek. Mer information finns i [Övervaka mappningsdataflöden](concepts-data-flow-monitoring.md).

![Övervakning av dataflöde](media/data-flow/mon003.png "Övervakare för dataflöde 3")

 För pipeline-felsökning körs krävs ungefär en minuts klusterkonfigurationstid i dina övergripande prestandaberäkningar för ett varmt kluster. Om du initierar standardkörningen för Azure Integration kan det ta cirka 5 minuter att avsluta på upptagningstiden.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Öka beräkningsstorleken i Azure Integration Runtime

En integrationskörning med fler kärnor ökar antalet noder i Spark-beräkningsmiljöerna och ger mer processorkraft för att läsa, skriva och omvandla dina data. ADF-dataflöden använder Spark för beräkningsmotorn. Spark-miljön fungerar mycket bra på minnesoptimerade resurser.
* Prova ett **beräkningsoptimerat** kluster om du vill att bearbetningshastigheten ska vara högre än inmatningshastigheten.
* Prova ett **minnesoptimerat** kluster om du vill cachelagra mer data i minnet. Minne optimerad har en högre prispunkt per kärna än beräkningsoptimerad, men kommer sannolikt att resultera i snabbare omvandlingshastigheter.

![Nya IR](media/data-flow/ir-new.png "Nya IR")

Mer information om hur du skapar en integrationskörning finns [i Integration Runtime i Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Öka storleken på felsökningsklustret

Som standard använder du standardkörningen för Azure Integration som skapas automatiskt för varje datafabrik. Den här standardvärdet för Azure IR är inställd på åtta kärnor, fyra för en drivrutinsnod och fyra för en arbetsnod med hjälp av egenskaper för allmän beräkning. När du testar med större data kan du öka storleken på felsökningsklustret genom att skapa en Azure IR med större konfigurationer och välja den här nya Azure IR när du aktiverar felsökning. Detta instruerar ADF att använda den här Azure IR för dataförhandsgranskning och pipeline-felsökning med dataflöden.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Minska starttiden för klusterberäkning med TTL

Det finns en egenskap i Azure IR under DataFlödesegenskaper som gör att du kan stå upp en pool med klusterberäkningsresurser för din fabrik. Med den här poolen kan du skicka dataflödesaktiviteter sekventiellt för körning. När poolen har upprättats tar varje efterföljande jobb 1-2 minuter för Spark-klustret på begäran att köra jobbet. Den första uppsättningen av resurspoolen tar cirka 6 minuter. Ange hur lång tid du vill underhålla resurspoolen i inställningen för tid att leva (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Optimera för Azure SQL Database och Azure SQL Data Warehouse Synapse

### <a name="partitioning-on-source"></a>Partitionering vid källa

1. Gå till fliken **Optimera** och välj **Ange partitionering**
1. Välj **Källa**.
1. Under **Antal partitioner**anger du det maximala antalet anslutningar till din Azure SQL DB. Du kan prova en högre inställning för att få parallella anslutningar till databasen. Vissa fall kan dock resultera i snabbare prestanda med ett begränsat antal anslutningar.
1. Välj om du vill partitionera efter en viss tabellkolumn eller en fråga.
1. Om du valde **Kolumn**väljer du partitionskolumnen.
1. Om du valde **Fråga**anger du en fråga som matchar partitioneringsschemat i databastabellen. Med den här frågan kan källdatabasmotorn utnyttja partitionseliminering. Källdatabastabellerna behöver inte partitioneras. Om källan inte redan är partitionerad använder ADF fortfarande datapartitionering i spark-omvandlingsmiljön baserat på den nyckel som du väljer i källomvandlingen.

![Källdel](media/data-flow/sourcepart3.png "Källdel")

> [!NOTE]
> En bra guide som hjälper dig att välja antal partitioner för din källa baseras på antalet kärnor som du har angett för din Azure Integration Runtime och multiplicera det antalet med fem. Så, till exempel, om du omvandlar en serie filer i dina ADLS-mappar och du kommer att använda en 32-kärnig Azure IR, antalet partitioner du skulle rikta är 32 x 5 = 160 partitioner.

### <a name="source-batch-size-input-and-isolation-level"></a>Batchstorlek, indata och isoleringsnivå för källar batch

Under **Källalternativ** i källomvandlingen kan följande inställningar påverka prestanda:

* Batchstorlek instruerar ADF att lagra data i uppsättningar i Spark-minne i stället för rad för rad. Batchstorlek är en valfri inställning och du kan få på resurser på beräkningsnoderna om de inte är rätt dimensionerade. Om du inte anger den här egenskapen används standardinställningar för Spark-cachelagring.
* Om du anger en fråga kan du filtrera rader vid källan innan de anländer till Dataflöde för bearbetning. Detta kan göra den första datainsamlingen snabbare. Om du använder en fråga kan du lägga till valfria frågetips för din Azure SQL DB, till exempel LÄS OENGAGERADE.
* Läsa oengagerad ger snabbare frågeresultat på Källa omvandling

![Källa](media/data-flow/source4.png "Källa")

### <a name="sink-batch-size"></a>Diskbänkssatsstorlek

Om du vill undvika bearbetning rad för rad av dina dataflöden anger du **Batch-storlek** på fliken Inställningar för Azure SQL DB och Azure SQL DW-sänkor. Om batchstorleken har angetts bearbetar ADF databasskrivningar i batchar baserat på den angivna storleken. Om du inte anger den här egenskapen används standardinställningar för Spark-cachelagring.

![Kanalmottagare](media/data-flow/sink4.png "Kanalmottagare")

### <a name="partitioning-on-sink"></a>Partitionering på diskbänk

Även om du inte har dina data partitionerade i måltabellerna, rekommenderas att dina data partitioneras i diskhon omvandlingen. Partitionerade data resulterar ofta i mycket snabbare inläsning över att tvinga alla anslutningar att använda en enda nod/partition. Gå till fliken Optimera i diskhon och välj Round Robin-partitionering för att välja det perfekta antalet partitioner att skriva till din diskbänk. *Round Robin*

### <a name="disable-indexes-on-write"></a>Inaktivera index vid skrivning

I pipelinen lägger du till en [aktivitet med lagrad procedur](transform-data-using-stored-procedure.md) före dataflödesaktiviteten som inaktiverar index på dina måltabeller som skrivits från diskbänken. När du har aktivitet med dataflöde lägger du till ytterligare en aktivitet för lagrad procedur som aktiverar dessa index. Eller använd skript för förbearbetning och efterbearbetning i en databasmottagare.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Öka storleken på din Azure SQL DB och DW

Schemalägg en storleksändring av din källa och sänka Azure SQL DB och DW innan pipeline-körningen för att öka dataflödet och minimera Azure-begränsningen när du når DTU-gränser. När pipelinekörningen är klar ändrar du storlek på databaserna till deras normala körningshastighet.

* SQL DB-källtabell med 887k rader och 74 kolumner till en SQL DB-tabell med en enda härledd kolumnomvandling tar cirka 3 minuter från på sluten tid med minne optimerad 80-kärnig felsökning Azure IRs.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Endast Azure Synapse SQL DW] Använd mellanlagring för att läsa in data i bulk via Polybase

Om du vill undvika infogade inlägg rad för rad i DW-dÃ¤ttningen kontrollerar du **Aktivera mellanlagring** i inställningarna för sink-instÃ¤llningar så att ADF kan antÃ¤lla [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase tillåter ADF att läsa in data i bulk.
* När du kör dataflödesaktiviteten från en pipeline måste du välja en Blob- eller ADLS Gen2-lagringsplats för att iscensätta dina data under massinläsning.

* Filkälla för 421 Mb-fil med 74 kolumner till en Synaps-tabell och en enda härledd kolumnomvandling tar cirka 4 minuter från på sluten tid med hjälp av minnesoptimerade 80-kärniga felsöknings Azure IRs.

## <a name="optimizing-for-files"></a>Optimera för filer

Vid varje omvandling kan du ställa in det partitioneringsschema som du vill att datafabriken ska använda på fliken Optimera. Det är en bra idé att först testa filbaserade sänkor som behåller standardpartitioneringen och optimeringarna.

* För mindre filer kan det hända att välja färre partitioner ibland kan fungera bättre och snabbare än att be Spark att partitionera dina små filer.
* Om du inte har tillräckligt med information om källdata väljer du *Partitionering* av round robin och anger antalet partitioner.
* Om dina data har kolumner som kan vara bra hash-nycklar väljer du *Hash-partitionering*.

* Filkälla med filmottagare för en 421 Mb-fil med 74 kolumner och en enda härledd kolumnomvandling tar cirka 2 minuter från på sluten tid med hjälp av minnesoptimerade 80-kärniga felsöknings Azure IRs.

När du felsöker i dataförhandsgranskning och pipelinefelsökning gäller endast gräns- och samplingsstorlekarna för filbaserade källdatauppsättningar för antalet rader som returneras, inte antalet rader som läses. Detta kan påverka prestanda för felsökningskörningar och eventuellt orsaka att flödet misslyckas.
* Felsökningskluster är små ennodskluster som standard och vi rekommenderar att du använder exempel på små filer för felsökning. Gå till Felsökningsinställningar och peka på en liten delmängd av dina data med hjälp av en temporär fil.

    ![Felsökningsinställningar](media/data-flow/debugsettings3.png "Felsökningsinställningar")

### <a name="file-naming-options"></a>Alternativ för filnamnsnamn

Det vanligaste sättet att skriva transformerade data vid mappning av dataflöden som skriver Blob eller ADLS-filarkiv. I diskhon måste du välja en datauppsättning som pekar på en behållare eller mapp, inte en namngiven fil. Eftersom mappningsdataflödet använder Spark för körning delas utdata över flera filer baserat på ditt partitioneringsschema.

Ett vanligt partitioneringsschema är att välja _Utdata till en enda fil_, som sammanfogar alla utdata-DEL-filer till en enda fil i diskhon. Den här åtgärden kräver att utdata reduceras till en enda partition på en enda klusternod. Du kan få på klusternodresurser om du kombinerar många stora källfiler till en enda utdatafil.

Om du vill undvika att uttömma beräkningsnodresurser behåller du standardschemat, optimerat schema i dataflödet och lägger till en kopieringsaktivitet i pipelinen som sammanfogar alla DEL-filer från utdatamappen till en ny enda fil. Den här tekniken skiljer omvandlingens verkan från filsammanryggning och uppnår samma resultat som att ange _utdata till en fil_.

### <a name="looping-through-file-lists"></a>Loopa via fillistor

Ett mappningsdataflöde körs bättre när källomvandlingen itererar över flera filer i stället för loopning via aktiviteten För varje. Vi rekommenderar att du använder jokertecken eller fillistor i källomvandlingen. Dataflödesprocessen körs snabbare genom att slingan tillåts i Spark-klustret. Mer information finns [i Jokertecken i Källomvandling](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Om du till exempel har en lista över datafiler från juli 2019 som du vill bearbeta i en mapp i Blob Storage finns nedan ett jokertecken som du kan använda i källomvandlingen.

```DateFiles/*_201907*.txt```

Genom att använda jokertecken innehåller pipelinen bara en dataflödesaktivitet. Detta kommer att fungera bättre än en sökning mot Blob Store som sedan itererar över alla matchade filer med hjälp av en ForEach med en Kör dataflödesaktivitet inuti.

### <a name="optimizing-for-cosmosdb"></a>Optimera för CosmosDB

Ange dataflöde och batchegenskaper på CosmosDB-sänkor börjar bara gälla under körningen av dataflödet från en pipeline-dataflödesaktivitet. De ursprungliga insamlingsinställningarna kommer att uppfyllas av CosmosDB efter att dataflödet har exekverats.

* Batchstorlek: Beräkna den grova radstorleken på dina data och se till att radstorleken * batchstorleken är mindre än två miljoner. Om så är fallet ökar du batchstorleken för att få bättre dataflöde
* Dataflöde: Ange en högre inställning för dataflöde här så att dokument kan skriva snabbare till CosmosDB. Tänk på de högre RU-kostnaderna baserat på en hög inställning för dataflöde.
*   Skrivdataflödesbudget: Använd ett värde som är mindre än det totala antalet ru:er per minut. Om du har ett dataflöde med ett stort antal Spark-partitioner, kommer om du anger ett budgetdataflöde att tillåta mer saldo över dessa partitioner.

## <a name="join-performance"></a>Gå med i prestanda

Att hantera prestanda för kopplingar i dataflödet är en mycket vanlig åtgärd som du utför under hela livscykeln för dina dataomvandlingar. I ADF kräver dataflöden inte att data sorteras före kopplingar eftersom dessa åtgärder utförs som hash-kopplingar i Spark. Du kan dock dra nytta av bättre prestanda med optimering av sändningskoppling. På så sätt undviks blandningar genom att trycka ned innehållet på vardera sidan av kopplingens relation till Spark-noden. Detta fungerar bra för mindre tabeller som används för referenssökningar. Större tabeller som kanske inte får plats i nodens minne är inte bra kandidater för broadcast-optimering.

En annan Join optimering är att bygga dina kopplingar på ett sådant sätt att det undviker Spark tendens att genomföra korskopplingar. När du till exempel inkluderar litterala värden i kopplingsvillkoren kan Spark se det som ett krav för att utföra en fullständig kartesisk produkt först och sedan filtrera bort de kopplade värdena. Men om du ser till att du har kolumnvärden på båda sidor av ditt kopplingsvillkor kan du undvika den här Gnist-inducerad cartesian-produkten och förbättra prestandan för dina kopplingar och dataflöden.

## <a name="next-steps"></a>Nästa steg

Se andra dataflödesartiklar relaterade till prestanda:

- [Fliken Optimera dataflödet](concepts-data-flow-overview.md#optimize)
- [Aktivitet för dataflöde](control-flow-execute-data-flow-activity.md)
- [Övervaka dataflödesprestanda](concepts-data-flow-monitoring.md)
