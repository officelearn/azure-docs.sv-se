---
title: Mappa dataflöde prestanda och justering för i Azure Data Factory | Microsoft Docs
description: Läs mer om viktiga faktorer som påverkar prestanda av data i Azure Data Factory när du använder mappning dataflöden.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 7fca586083f70e0b0f7e593d5203392260cd2136
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66172347"
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

## <a name="optimizing-for-azure-sql-database"></a>Optimera för Azure SQL Database

![Käll-del](media/data-flow/sourcepart2.png "käll-delen")

### <a name="you-can-match-spark-data-partitioning-to-your-source-database-partitioning-based-on-a-database-table-column-key-in-the-source-transformation"></a>Du kan matcha Spark Datapartitionering till källan Databaspartitionering baserat på en tabell kolumnen databasnyckeln i käll-transformering

* Gå till ”optimera” och Välj ”källa”. Ange antingen en specifik tabellkolumn eller en typ i en fråga.
* Om du väljer ”kolumn”, väljer du partitionskolumnen.
* Ange dessutom det maximala antalet anslutningar till din Azure SQL-databas. Du kan prova en högre inställning för att få parallella anslutningar till databasen. Ibland kan dock leda snabbare prestanda med ett begränsat antal anslutningar.

### <a name="set-batch-size-and-query-on-source"></a>Ange batchstorlek och fråga på källan

![Source](media/data-flow/source4.png "Source")

* Ange batchstorlek instruerar ADF om du vill lagra data i uppsättningar i minnet i stället för rad för rad. Det är en valfri inställning och du får slut på resurser på beräkningsnoderna om de inte är korrekt storlek.
* Ställa en fråga kan du filtrera rader höger vid källan innan de även tas emot för dataflöde för bearbetning, vilket kan göra att ursprungliga data förvärvet snabbare.
* Om du använder en fråga kan du lägga till valfria frågetips för din Azure SQL-databas, d.v.s. READ UNCOMMITTED

### <a name="set-sink-batch-size"></a>Ange mottagare batchstorlek

![Mottagare](media/data-flow/sink4.png "mottagare")

* Ange ”batchstorlek” i mottagarinställningarna för för Azure SQL DB för att undvika rad för rad bearbetning av data-floes. Detta talar om ADF om du vill bearbeta databasen skriver i skalningsuppsättningarna baserat på storleken anges.

### <a name="set-partitioning-options-on-your-sink"></a>Ange partitionering alternativ på dina mottagare

* Även om du inte har dina data partitioneras på din Azure SQL DB måltabellerna, gå till fliken Optimize och ange partitionering.
* Mycket ofta bara uppmanar ADF om du vill använda resursallokering partitionering i Spark-körningen kluster resulterar i mycket snabbare i stället för att tvinga alla anslutningar från en enda nod/partition för inläsning av data.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Öka storleken på din databearbetningsmotor i Azure Integration Runtime

![Ny IR](media/data-flow/ir-new.png "nya IR")

* Öka antalet kärnor som ökar antalet noder och du får mer processorkraft att fråga och skriva till din Azure SQL-databas.
* Prova ”Compute Optimized” och ”Minnesoptimerad” alternativ för att tillämpa fler resurser till beräkningsnoderna.

### <a name="disable-indexes-on-write"></a>Inaktivera index vid skrivning
* Använd en ADF lagrade proceduren pipelineaktivitet före ditt dataflöde aktivitet som inaktiverar index i din måltabeller som skrivs in från dina mottagare.
* Lägg till en annan aktivitet för lagrad procedur som aktiverats dessa index efter ditt dataflöde aktivitet.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Öka storleken på din Azure SQL-databas
* Schemalägg en storleksändring av din källa och mottagare Azure SQL DB innan din körning som begränsar din pipeline för att öka dataflödet och minimera Azure begränsning när du når DTU.
* Du kan ändra storlek databaserna tillbaka till sina kör normalt när pipeline-åtgärd har slutförts.

## <a name="next-steps"></a>Nästa steg
Se andra dataflöde artiklar:

- [Översikt över flödet av data](concepts-data-flow-overview.md)
- [Data flödesaktivitet](control-flow-execute-data-flow-activity.md)

