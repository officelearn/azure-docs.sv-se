---
title: Optimera din Gen2-cache
description: Lär dig hur du övervakar Gen2-cachen med hjälp av Azure Portal.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 924705b7ce1d324583077797714491bdf3fc5cc9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721225"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Så här övervakar du Gen2-cachen
Lagrings arkitekturen Gen2 använder automatiskt de mest efterfrågade columnstore-segmenten i en cache som finns på NVMe-baserade SSD som är utformade för Gen2 Data Warehouse. Bättre prestanda realiseras när dina frågor hämtar segment som finns i cacheminnet. I den här artikeln beskrivs hur du övervakar och felsöker långsamma frågeresultat genom att fastställa om din arbets belastning optimalt utnyttjar Gen2-cachen.  
## <a name="troubleshoot-using-the-azure-portal"></a>Felsöka med hjälp av Azure Portal
Du kan använda Azure Monitor för att Visa Gen2-cache-mått för att felsöka frågans prestanda. Gå först till Azure Portal och klicka på övervakaren:

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Välj knappen mått och fyll i data lagrets **prenumeration**, **resurs** **grupp**, **resurs typ**och **resurs namn** .

Nyckel måtten för fel sökning av Gen2-cachen är **cache träff procent** och **cache som används i procent**. Konfigurera Azure Metric-diagrammet så att de här två måtten visas.

![Cache-mått](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Cacheträffar och Använd procent andel
I matrisen nedan beskrivs scenarier som baseras på värdena för cache-måtten:

|                                | **Procent andel vid hög cacheträffar** | **Procent andel låg cacheträffar** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Procent andel Använd hög cache** |          Scenario 1           |          Scenario 2          |
| **Procent andel låg cache som används**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** Du använder din cache optimalt. [Felsöka](sql-data-warehouse-manage-monitor.md) andra områden som kan vara långsamma för dina frågor.

**Scenario 2:** Din aktuella arbets data uppsättning får inte plats i cachen, vilket orsakar en procent andel av cacheträffar på grund av fysiska läsningar. Överväg att skala upp prestanda nivån och kör arbets belastningen igen för att fylla i cacheminnet.

**Scenario 3:** Det är troligt att frågan körs långsamt på grund av orsaker som inte är relaterade till cacheminnet. [Felsöka](sql-data-warehouse-manage-monitor.md) andra områden som kan vara långsamma för dina frågor. Du kan också välja att [skala ned din instans](sql-data-warehouse-manage-monitor.md) för att minska storleken på cacheminnet för att spara kostnader. 

**Scenario 4:** Du hade ett kallt cacheminne som kan vara orsaken till att din fråga var långsam. Överväg att köra frågan igen eftersom din arbets data uppsättning nu ska cachelagras. 

> [!IMPORTANT]
> Om antalet träffar i procent eller i cachen som används inte uppdateras efter att du har kört om din arbets belastning, kan din arbets minne redan finnas i minnet. Endast grupperade columnstore-tabeller cachelagras.

## <a name="next-steps"></a>Nästa steg
Mer information om prestanda justering för allmänna frågor finns i [övervaka frågekörningen](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution).
