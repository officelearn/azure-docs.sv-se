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
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245515"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Viktig information om Azure SQL Data Warehouse
Den här artikeln sammanfattas de nya funktionerna och förbättringarna i de senaste versionerna av [SQL Server på Azure virtual machines](sql-data-warehouse-overview-what-is.md). Artikeln innehåller också viktiga uppdateringar av innehållet som inte är direclty relaterade till versionen men publicerats i samma tidsram. Förbättringar av andra Azure-tjänster, se [uppdateringar av tjänsten](https://azure.microsoft.com/updates)

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse Version 10.0.10106.0 (januari)

### <a name="service-improvements"></a>Förbättringar av tjänsten

| Förbättringar av tjänsten | Information |
| --- | --- |
| **Fråga Restartability - CTAS och infoga/välja** | I sällsynta fall (det vill säga återkommande problem med nätverksanslutning, nodfel) kan frågan körs i Azure SQL DW misslyckas. Kör längre instruktioner, till exempel [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) och infoga-Välj operations exponeras mer för det här potentiella problemet. Med den här versionen implementerar Azure SQL DW logik för omprövning för CTAS och INSERT SELECT-uttryck, förutom SELECT-instruktioner som tidigare meddelats. Ändringarna att tjänsten kan hantera tillfälliga problem transparent och förhindra att frågorna misslyckas. Antal nya försök och listan över tillfälliga fel hanteras är system som har konfigurerats.|
|**Returnera Order By-optimering**|VÄLJ... ORDER BY-frågor får en prestandaökning i den här versionen.   Nu kan beräkningsresurser alla noder skickar resultaten till en enda beräkningsnod som sammanfogar och sorterar resultatet som returneras till användaren via Beräkningsnoden.  Sammanslagning via en enda beräkning noden leder till betydande prestandafördelar när frågeresultatet innehåller ett stort antal rader. Motorn för körning av fråga skulle tidigare, ordna resultaten på varje beräkningsnod och strömma dem till control-noden som skulle sedan Sammanfoga resultatet.|
|**Förbättringar för flytt av data för PartitionMove och BroadcastMove**|I Azure SQL Data Warehouse Gen2, data movement steg av typen ShuffleMove, använder du omedelbar data movement metoder som beskrivs i den [prestanda förbättringar blogg](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/). Med den här versionen drivs förflyttning datatyper PartitionMove och BroadcastMove nu också av samma rörelsetekniker för omedelbar data. Användarfrågor som använder dessa typer av data movement steg körs med förbättrad prestanda. Några ändringar i koden krävs för att dra nytta av dessa prestandaförbättringar.|

### <a name="documentation-improvements"></a>Dokumentation om förbättringar

| Dokumentation om förbättringar | Information |
| --- | --- |
|inga | |
| | |

## <a name="next-steps"></a>Nästa steg
[skapar ett SQL Data Warehouse](./create-data-warehouse-portal.md)

## <a name="more-information"></a>Mer information
- [-Bloggen – Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Customer Advisory Team-bloggar](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [Kundernas framgångsberättelser](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow-forum](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure-ordlistan](../azure-glossary-cloud-terminology.md)