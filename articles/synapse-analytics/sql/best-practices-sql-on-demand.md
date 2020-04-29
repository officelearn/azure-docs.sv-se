---
title: Metod tips för SQL på begäran (för hands version) i Azure Synapse Analytics
description: Rekommendationer och metod tips du bör känna till när du arbetar med SQL på begäran (för hands version).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429075"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Metod tips för SQL på begäran (för hands version) i Azure Synapse Analytics

I den här artikeln hittar du en samling bästa metoder för att använda SQL på begäran (för hands version). SQL på begäran är en ytterligare resurs i Azure Synapse Analytics.

## <a name="general-considerations"></a>Generella saker att tänka på

Med SQL på begäran kan du söka efter filer i dina Azure Storage-konton. Den har inte funktioner för lokal lagring eller inmatning. Därför är alla filer som fråge målen är externa för SQL på begäran. Allt som rör läsning av filer från lagring kan påverka frågans prestanda.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Samplacera Azure Storage konto och SQL på begäran

Du kan minimera svars tiden genom att samplacera ditt Azure Storage-konto och din SQL-slutpunkt på begäran. Lagrings konton och slut punkter som tillhandahålls när arbets ytan skapas befinner sig i samma region.

För optimala prestanda bör du kontrol lera att de finns i samma region om du har åtkomst till andra lagrings konton med SQL på begäran. Om de inte finns i samma region ökar svars tiden för data överföring mellan den fjärranslutna och slut punktens regioner.

## <a name="azure-storage-throttling"></a>Azure Storage begränsning

Flera program och tjänster kan komma åt ditt lagrings konto. Lagrings begränsning sker när den sammanlagda IOPS eller data flödet som genereras av program, tjänster och SQL på begäran-arbetsbelastning överskrider lagrings kontots gränser. Därför får du en betydande negativ effekt på frågans prestanda.

När begränsningen har identifierats har SQL på begäran inbyggd hantering av det här scenariot. SQL på begäran kommer att göra begär anden till lagringen i en långsammare takt tills begränsningen har lösts.

> [!TIP]
> För optimal frågekörning bör du inte stressa lagrings kontot med andra arbets belastningar under frågekörningen.

## <a name="prepare-files-for-querying"></a>Förbered filer för frågor

Om möjligt kan du förbereda filer för bättre prestanda:

- Konvertera CSV till Parquet-Parquet är kolumn format. Eftersom fil storleken är komprimerad är fil storleken mindre än CSV-filer med samma data. SQL på begäran behöver mindre tids-och lagrings begär Anden för att kunna läsa det.
- Om en fråga är riktad mot en enda stor fil, drar du nytta av att dela upp den i flera mindre filer.
- Försök att behålla storleken på CSV-filen under 10 GB.
- Det är bättre att ha lika stora filer för en enskild OpenRowSet-sökväg eller en extern tabell plats.
- Partitionera dina data genom att lagra partitioner i olika mappar eller fil namn – kontrol lera [Använd fil namn och fil Sök väg funktioner för att ange specifika partitioner](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Använda fileinfo-och fil Sök vägar för att fokusera på specifika partitioner

Data är ofta ordnade i partitioner. Du kan instruera SQL på begäran att fråga specifika mappar och filer. Funktionen kommer att minska antalet filer och mängden data som frågan behöver läsa och bearbeta. En extra bonus är att du får bättre prestanda.

Mer information finns i funktioner för [fil namn](develop-storage-files-overview.md#filename-function) och fil [Sök väg](develop-storage-files-overview.md#filepath-function) och exempel på hur du [frågar efter vissa filer](query-specific-files.md).

Om dina lagrade data inte är partitionerade bör du överväga att partitionera dem så att du kan använda dessa funktioner för att optimera frågor som riktar sig mot dessa filer. När du [frågar partitionerade Spark-tabeller](develop-storage-files-spark-tables.md) från SQL på begäran, kommer frågan automatiskt att rikta in sig på de filer som behövs.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Använd CETAS för att förbättra frågornas prestanda och kopplingar

[CETAS](develop-tables-cetas.md) är en av de viktigaste funktionerna som är tillgängliga i SQL på begäran. CETAS är en parallell åtgärd som skapar externa tabellens metadata och exporterar URVALs resultatet till en uppsättning filer i ditt lagrings konto.

Du kan använda CETAS för att lagra ofta använda delar av frågor som kopplade referens tabeller till en ny uppsättning filer. Sedan kan du koppla till den här enskilda externa tabellen i stället för att upprepa vanliga kopplingar i flera frågor.

När CETAS genererar Parquet-filer skapas statistik automatiskt när den första frågan riktar sig till den externa tabellen, vilket resulterar i förbättrade prestanda.

## <a name="next-steps"></a>Nästa steg

Läs artikeln om [fel sökning](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för vanliga problem och lösningar. Om du arbetar med SQL-pool i stället för SQL på begäran, kan du läsa mer i artikeln [metod tips för SQL-pooler](best-practices-sql-pool.md) .
