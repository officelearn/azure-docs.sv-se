---
title: Metodtips för SQL on-demand (förhandsversion) i Azure Synapse Analytics
description: Rekommendationer och metodtips som du bör känna till när du arbetar med SQL on-demand (förhandsversion).
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429075"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Metodtips för SQL on-demand (förhandsversion) i Azure Synapse Analytics

I den här artikeln hittar du en samling metodtips för att använda SQL on-demand (preview). SQL on-demand är en ytterligare resurs i Azure Synapse Analytics.

## <a name="general-considerations"></a>Generella saker att tänka på

Med SQL on-demand kan du fråga filer i dina Azure-lagringskonton. Den har inte lokala lagrings- eller intagsfunktioner. Därför är alla filer som frågemålen externa till SQL på begäran. Allt som är relaterat till att läsa filer från lagring kan påverka frågeprestanda.

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Colocate Azure Storage-konto och SQL på begäran

För att minimera svarstiden samlokalisera ditt Azure-lagringskonto och din SQL-slutpunkt på begäran. Lagringskonton och slutpunkter som etablerats när arbetsytan skapas finns i samma region.

Om du använder andra lagringskonton med SQL på begäran för bästa prestanda måste du se till att de finns i samma region. Om de inte finns i samma region kommer det att finnas ökad svarstid för datas nätverksöverföring mellan fjärr- och slutpunktens regioner.

## <a name="azure-storage-throttling"></a>Azure Storage begränsning

Flera program och tjänster kan komma åt ditt lagringskonto. Lagringsbegränsning sker när det kombinerade IOPS- eller dataflödet som genereras av program, tjänster och SQL-arbetsbelastning på begäran överskrider gränserna för lagringskontot. Därför får du en betydande negativ effekt på frågeprestanda.

När begränsning har identifierats har SQL on-demand inbyggd hantering av det här scenariot. SQL on-demand kommer att göra begäranden till lagring i långsammare takt tills begränsningen är löst.

> [!TIP]
> För optimal frågekörning bör du inte betona lagringskontot med andra arbetsbelastningar under frågekörning.

## <a name="prepare-files-for-querying"></a>Förbereda filer för frågor

Om möjligt kan du förbereda filer för bättre prestanda:

- Konvertera CSV till Parkett - Parkett är columnar format. Eftersom den är komprimerad är filstorlekarna mindre än CSV-filer med samma data. SQL on-demand behöver mindre tid och lagringsbegäranden för att läsa den.
- Om en fråga riktar sig till en enda stor fil kan du dra nytta av att dela upp den i flera mindre filer.
- Försök att hålla din CSV filstorlek under 10 GB.
- Det är bättre att ha lika stora filer för en enda OPENROWSET-sökväg eller en extern tabellPLATS.
- Partitionera dina data genom att lagra partitioner till olika mappar eller filnamn - kontrollera [använd filnamn och filsökvägsfunktioner för att rikta specifika partitioner](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Använda filinfo- och filsökvägar för att rikta specifika partitioner

Data är ofta ordnade i partitioner. Du kan instruera SQL på begäran att fråga vissa mappar och filer. Den här funktionen minskar antalet filer och mängden data som frågan behöver läsa och bearbeta. En extra bonus är att du kommer att uppnå bättre prestanda.

Mer information finns i [filnamns-](develop-storage-files-overview.md#filename-function) och [filsökvägsfunktioner](develop-storage-files-overview.md#filepath-function) och exempel på hur [du frågar specifika filer](query-specific-files.md).

Om dina lagrade data inte partitioneras kan du överväga att partitionera dem så att du kan använda dessa funktioner för att optimera frågor som är inriktade på dessa filer. När [du frågar partitionerade Spark-tabeller](develop-storage-files-spark-tables.md) från SQL on-demand, kommer frågan automatiskt att rikta endast de filer som behövs.

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Använd CETAS för att förbättra frågeprestanda och kopplingar

[CETAS](develop-tables-cetas.md) är en av de viktigaste funktionerna i SQL on-demand. CETAS är en parallell åtgärd som skapar externa tabellmetadata och exporterar SELECT-frågeresultatet till en uppsättning filer i ditt lagringskonto.

Du kan använda CETAS för att lagra ofta använda delar av frågor, till exempel kopplade referenstabeller, till en ny uppsättning filer. Därefter kan du gå med i den här enskilda externa tabellen i stället för att upprepa vanliga kopplingar i flera frågor.

När CETAS genererar parquet-filer skapas statistik automatiskt när den första frågan inriktas på den här externa tabellen, vilket resulterar i bättre prestanda.

## <a name="next-steps"></a>Nästa steg

Läs [felsökningsartikeln](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) för vanliga problem och lösningar. Om du arbetar med SQL-pool i stället för SQL on-demand läser du artikeln [Bästa praxis för SQL-pool](best-practices-sql-pool.md) för specifik vägledning.
