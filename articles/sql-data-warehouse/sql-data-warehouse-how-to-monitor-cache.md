---
title: Optimera din Gen2 cache | Microsoft Docs
description: Lär dig hur du övervakar din Gen2 cache med hjälp av Azure portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: how-to
ms.component: monitor and tune
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1cf2fcb2ce99d4c6c670e5afdb1c4208158ea4de
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44096343"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Så här övervakar du Gen2-cache
Lagringsarkitektur Gen2 fördelar automatiskt dina mest efterfrågade columnstore-segment i ett cacheminne på NVMe SSD-baserade enheter som har utformats för informationslager Gen2. Bättre prestanda realiseras när dina frågor hämtar segment som är bosatta i cacheminnet. Den här artikeln beskriver hur du övervakar och felsöker långsam frågeprestanda genom att fastställa om din arbetsbelastning optimalt utnyttjar Gen2 cache.  
## <a name="troubleshoot-using-the-azure-portal"></a>Felsök med hjälp av Azure-portalen
Du kan använda Azure Monitor för att visa Gen2 cache-mått om du vill felsöka frågeprestanda. Först gå till Azure-portalen och klicka på Övervakare:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Välj knappen mått och Fyll i den **prenumeration**, **Resource** **grupp**, **resurstyp**, och **resurs namn på** för ditt informationslager.

Viktiga mått för att felsöka Gen2 cachen är **Cacheträff procent** och **Cache används procent**. Konfigurera Azure måttdiagram om du vill visa de här två måtten.

![Cache-mått](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Cache träffar och används procent
Matrisen nedan beskrivs scenarier som baseras på värden för cache-mått:

|                                | **Hög procentandel av Cacheträff** | **Låg Cacheträff procent** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Hög procentandel av Cache som används** |          Scenario 1           |          Scenario 2          |
| **Låg Cache används procent**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** du optimalt använder din cache. [Felsöka](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) andra områden som kan sänka dina frågor.

**Scenario 2:** din aktuella arbetsminnet för data kan inte placeras i cacheminnet som orsakar en låg Cacheträff procent på grund av fysiska läsningar. Överväg att skala upp din prestandanivå och kör din arbetsbelastning för att fylla i cachen.

**Scenario 3:** är det troligt att frågan är långsamt på grund av orsaker som inte är relaterade till cachen. [Felsöka](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) andra områden som kan sänka dina frågor. Du kan även överväga [skalar ned din instans](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor) att minska cachestorleken på din för att spara kostnader. 

**Scenario 4:** du hade en kalla cache som kan vara orsaken till varför din fråga gick långsamt. Överväg att köra frågan eftersom fungerande datauppsättningen ska nu i cachelagras. 

**Viktigt: Om Cacheträff procent eller Använd cache procent uppdateras inte när du kör igen din arbetsbelastning, din arbetsminnet kan redan som finns i minnet. Observera endast grupperade tabeller cachelagras.**

## <a name="next-steps"></a>Nästa steg
Mer information om finjustering av allmän fråga prestanda finns i [övervaka Frågekörningen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor#monitor-query-execution).


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: http://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: http://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: http://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: http://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: http://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: http://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: http://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
