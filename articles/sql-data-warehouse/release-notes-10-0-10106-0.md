---
title: Azure SQL Data Warehouse viktig information | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 0b0d302dcd86f18eef6bf7600dd93878d0d9bd99
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902696"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Viktig information om Azure SQL Data Warehouse
Den här artikeln sammanfattas de nya funktionerna och förbättringarna i de senaste versionerna av [Azure SQL Data Warehouse](sql-data-warehouse-overview-what-is.md). Artikeln innehåller också viktiga uppdateringar av innehållet som är inte direkt relaterade till versionen men som har publicerats i samma tidsram. Förbättringar av andra Azure-tjänster, se [uppdateringar av tjänsten](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse Version 10.0.10106.0 (januari)

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
|**Returnera Order By-optimering**|VÄLJ... ORDER BY-frågor får en prestandaökning i den här versionen.   Nu kan beräkningsresurser alla noder skickar resultaten till en enda beräkningsnod som sammanfogar och sorterar resultatet som returneras till användaren via Beräkningsnoden.  Sammanslagning via en enda beräkning noden leder till betydande prestandafördelar när frågeresultatet innehåller ett stort antal rader. Motorn för körning av fråga skulle tidigare, ordna resultaten på varje beräkningsnod och strömma dem till control-noden som skulle sedan Sammanfoga resultatet.|
|**Förbättringar för flytt av data för PartitionMove och BroadcastMove**|I Azure SQL Data Warehouse Gen2, data movement steg av typen ShuffleMove, använder du omedelbar data movement metoder som beskrivs i den [prestanda förbättringar blogg](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Med den här versionen drivs förflyttning datatyper PartitionMove och BroadcastMove nu också av samma rörelsetekniker för omedelbar data. Användarfrågor som använder dessa typer av data movement steg körs med förbättrad prestanda. Några ändringar i koden krävs för att dra nytta av dessa prestandaförbättringar.|
|**Viktiga buggar**|Felaktig version av Azure SQL Data Warehouse - ”Välj@VERSION” kan returnera fel version 10.0.9999.0. Rätt version för den aktuella versionen är 10.0.10106.0. Den här buggen har rapporterats och är under granskning.

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
|inga | |
| | |

## <a name="next-steps"></a>Nästa steg
- [skapar ett SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Mer information
- [-Bloggen – Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Customer Advisory Team-bloggar](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Kundernas framgångsberättelser](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow-forum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure-ordlistan](../azure-glossary-cloud-terminology.md)
