---
title: Mappa dataflöde prestanda och justering för i Azure Data Factory | Microsoft Docs
description: Läs mer om viktiga faktorer som påverkar prestanda av data i Azure Data Factory när du använder mappning dataflöden.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: bbbc2bc5c47821469ecf15a27195b1bf0c12e6e5
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190633"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mappa data flöden prestanda- och justeringsguide

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory mappning Data flödar ger en kodfria Webbläsargränssnittet för att utforma, distribuera och samordna dataomvandlingar i stor skala.

> [!NOTE]
> Om du inte är bekant med ADF mappning Data flödar i allmänhet kan du läsa [Data flödar översikt](concepts-data-flow-overview.md) innan du läser den här artikeln.
>

> [!NOTE]
> När du utformar och testar dataflöden från ADF UI, se till att aktivera växeln felsökning så att du kan köra dina dataflöden i realtid utan att behöva vänta ett kluster värmt upp.
>

![Felsöka knappen](media/data-flow/debugb1.png "felsöka")

## <a name="monitor-data-flow-performance"></a>Övervaka prestanda för data-flöde

När du utformar din mappningsdata som flödar i webbläsaren, kan du enhetstest varje enskild transformering genom att klicka på fliken data förhandsversion längst ned i fönstret inställningar för varje transformering. Du bör ta nästa steg är att testa din data flow slutpunkt till slutpunkt i pipelinedesignern. Lägg till en aktivitet kör dataflöde och Använd Debug-knappen för att testa prestanda hos ditt dataflöde. I rutan längst ned i fönstret pipeline visas en ikon för eyeglass under ”åtgärder”:

![Dataflöde övervakaren](media/data-flow/mon002.png "dataflöde övervakaren 2")

Klicka på ikonen visas Körningsplan och efterföljande prestanda profilen för ditt dataflöde. Du kan använda den här informationen för att uppskatta prestanda för ditt dataflöde mot olika storlekar datakällor. Observera att kan du anta att 1 minut körningstid för installation på klustret jobbet i din övergripande prestanda beräkningar och om du använder standard Azure Integration Runtime kan du behöva lägga till 5 minuter från klustret snurra upp samt tid.

![Övervakning av data Flow](media/data-flow/mon003.png "dataflöde övervakaren 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Optimera för Azure SQL Database och Azure SQL Data Warehouse

![Käll-del](media/data-flow/sourcepart3.png "käll-delen")

### <a name="partition-your-source-data"></a>Partitionera dina källdata

* Gå till ”optimera” och Välj ”källa”. Ange antingen en specifik tabellkolumn eller en typ i en fråga.
* Om du väljer ”kolumn”, väljer du partitionskolumnen.
* Ange dessutom det maximala antalet anslutningar till din Azure SQL-databas. Du kan prova en högre inställning för att få parallella anslutningar till databasen. Ibland kan dock leda snabbare prestanda med ett begränsat antal anslutningar.
* Dina tabeller behöver inte vara partitionerade.
* Ställa en fråga i din käll-omvandling som matchar partitioneringsschemat för databastabellen kan databasmotorn källkod utnyttja partitionseliminering.
* Om källan redan inte är partitionerad ADF fortfarande att använda i Spark-omvandling miljö baserat på den nyckel som du väljer i käll-transformering för partitionering.

### <a name="set-batch-size-and-query-on-source"></a>Ange batchstorlek och fråga på källan

![Source](media/data-flow/source4.png "Source")

* Ange batchstorlek instruerar ADF om du vill lagra data i uppsättningar i minnet i stället för rad för rad. Det är en valfri inställning och du får slut på resurser på beräkningsnoderna om de inte är korrekt storlek.
* Ställa en fråga kan du filtrera rader höger vid källan innan de även tas emot för dataflöde för bearbetning, vilket kan göra att ursprungliga data förvärvet snabbare.
* Om du använder en fråga kan du lägga till valfria frågetips för din Azure SQL-databas, d.v.s. READ UNCOMMITTED

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Ange Isoleringsnivån för omvandling av källinställningar för SQL-datauppsättningar

* Ogenomförd ger snabbare frågeresultat för transformering av källa

![Isoleringsnivån](media/data-flow/isolationlevel.png "isoleringsnivå")

### <a name="set-sink-batch-size"></a>Ange mottagare batchstorlek

![Mottagare](media/data-flow/sink4.png "mottagare")

* Ange ”batchstorlek” i mottagarinställningarna för för Azure SQL DB för att undvika rad för rad bearbetning av dina data. Detta talar om ADF om du vill bearbeta databasen skriver i skalningsuppsättningarna baserat på storleken anges.

### <a name="set-partitioning-options-on-your-sink"></a>Ange partitionering alternativ på dina mottagare

* Även om du inte har dina data partitioneras på din Azure SQL DB måltabellerna, gå till fliken Optimize och ange partitionering.
* Mycket ofta bara uppmanar ADF om du vill använda resursallokering partitionering i Spark-körningen kluster resulterar i mycket snabbare i stället för att tvinga alla anslutningar från en enda nod/partition för inläsning av data.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Öka storleken på din databearbetningsmotor i Azure Integration Runtime

![Ny IR](media/data-flow/ir-new.png "nya IR")

* Öka antalet kärnor som ökar antalet noder och du får mer processorkraft att fråga och skriva till din Azure SQL-databas.
* Prova ”Compute Optimized” och ”Minnesoptimerad” alternativ för att tillämpa fler resurser till beräkningsnoderna.

### <a name="unit-test-and-performance-test-with-debug"></a>Enhetstest och prestandatest med felsökning

* Enhet testning dataflöden inställt när knappen ”Data flöda felsöka” på på.
* Inuti dataflöde kan du använda fliken Dataförhandsgranskning på transformeringar för att visa resultatet av omvandling logik.
* Enhetstest dina data som flödar från pipelinedesignern genom att placera en aktivitet för dataflöde på pipeline-design arbetsytan och använda knappen ”Felsökning” för att testa.
* Testa i felsökningsläge fungerar mot en uppvärmning livekluster miljö utan att behöva vänta tills en just-in-time-kluster snurra upp.

### <a name="disable-indexes-on-write"></a>Inaktivera index vid skrivning
* Använd en ADF lagrade proceduren pipelineaktivitet före ditt dataflöde aktivitet som inaktiverar index i din måltabeller som skrivs in från dina mottagare.
* Lägg till en annan aktivitet för lagrad procedur som aktiverats dessa index efter ditt dataflöde aktivitet.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Öka storleken på din Azure SQL-databas
* Schemalägg en storleksändring av din källa och mottagare Azure SQL DB innan din körning som begränsar din pipeline för att öka dataflödet och minimera Azure begränsning när du når DTU.
* Du kan ändra storlek databaserna tillbaka till sina kör normalt när pipeline-åtgärd har slutförts.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Optimera för Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Använd mellanlagring att läsa in data i bulk via Polybase

* Ange alternativet ”mellanlagring” i inställningarna för mottagare för att undvika rad för rad bearbetning av dina data, så att ADF kan dra nytta av Polybase för att undvika rad för rad infogningar till DW. Detta instruerar ADF om du vill använda Polybase så att data kan läsas samtidigt.
* När du kör din flödesaktivitet för data från en pipeline med mellanlagring aktiverat, du måste välja Blob-lagringsplatsen för dina mellanlagring massinläsning.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Öka storleken på din Azure SQL DW

* Schemalägg en storleksändring av din källa och mottagare Azure SQL DW innan du kör din pipeline för att öka dataflödet och minimera Azure begränsning när du når DWU-gränserna.

* Du kan ändra storlek databaserna tillbaka till sina kör normalt när pipeline-åtgärd har slutförts.

## <a name="optimize-for-files"></a>Optimera för filer

* Du kan styra hur många partitioner som ADF ska använda. På varje källa och mottagare transformering, samt varje enskild transformering, kan du ange ett partitioneringsschema. För mindre filer kan det hända att välja ”enskild Partition” kan ibland fungerar bättre och snabbare än ber Spark att partitionera dina små filer.
* Om du inte har tillräckligt med information om dina källdata kan välja du ”resursallokering” partitionering och ange antalet partitioner.
* Om du utforska dina data och upptäcker att du har kolumner som kan vara bra hash-nycklar, Använd Hash partitionering alternativet.

### <a name="file-naming-options"></a>Alternativ för filnamn

* Standard natur skriva transformerade data i ADF mappning Data flödar är att skriva till en datauppsättning som har en Blob eller ADLS-länkade tjänsten. Du bör ange den datauppsättningen så att den pekar till en mapp eller behållare, inte en namngiven fil.
* Flöden dataanvändning Azure Databricks Spark för körning, vilket innebär att dina utdata kan delas över flera filer baserat på antingen standard Spark partitionering eller partitionering system som du har valt.
* En mycket vanlig åtgärd i ADF Data flödar är att välja ”Spara till fil” så att alla dina filer för en del av utdata slås samman tillsammans i en enda utdatafilen.
* Den här åtgärden kräver dock att utdata minskar till en enskild partition på en enskild klusternod.
* Tänk på det när du väljer det här alternativet för populära. Du kan köra utanför klusterresurser för noden om du kombinerar många stora källfiler i en enda fil partition.
* För att undvika att få slut beräkningsresurser för noden kan du hålla standard eller explicit partitioneringsschema i ADF som optimeras för prestanda, och sedan lägga till en efterföljande Kopieringsaktivitet i pipelinen som slår samman alla för en del filer från den utgående mappen till en ny enda filen. I princip skiljer åtgärden av omvandling från filen sammanslagning denna teknik och ger samma resultat som om du anger ”utdata till fil”.

## <a name="next-steps"></a>Nästa steg
Se andra dataflöde artiklar rör prestanda:

- [Dataflödet optimera fliken](concepts-data-flow-optimize-tab.md)
- [Data flödesaktivitet](control-flow-execute-data-flow-activity.md)
- [Övervaka prestanda för dataflöde](concepts-data-flow-monitoring.md)
