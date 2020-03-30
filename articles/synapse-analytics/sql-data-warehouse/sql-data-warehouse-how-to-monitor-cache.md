---
title: Optimera din Gen2-cache
description: Lär dig hur du övervakar din Gen2-cache med Azure-portalen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bb5560164af2b573e6aaffd4e4c62bbe0dc24a51
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350410"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Så här övervakar du Gen2-cachen

I den här artikeln beskrivs hur du övervakar och felsöker långsamma frågeprestanda genom att avgöra om din arbetsbelastning är optimalt utnyttja Gen2-cachen.

Gen2-lagringsarkitekturen nivåer automatiskt dina mest efterfrågade columnstore-segment i en cache som finns på NVMe-baserade SSD-enheter som utformats för Gen2-datalager. Större prestanda realiseras när dina frågor hämtar segment som finns i cacheminnet.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Felsöka med Azure-portalen

Du kan använda Azure Monitor för att visa Gen2-cachemått för att felsöka frågeprestanda. Gå först till Azure-portalen och klicka på **Övervaka,** **Mått** och **+ Välj ett scope:**

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Använd sök- och listningsfälten för att hitta ditt informationslager. Välj sedan apply.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

De viktigaste måtten för felsökning av Gen2-cachen är **cachehitprocent** och **cache använd procentandel**. Välj **Cache träffprocent** och använd sedan knappen Lägg till **mått** för att lägga till cache **använd procentsats**. 

![Cachemått](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Cache träff och använd procent

Matrisen nedan beskriver scenarier baserat på värdena för cachemåtten:

|                                | **Hög träffprocent för cache** | **Låg träffprocent för cache** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Hög cache använd procentandel** |          Scenario 1           |          Scenario 2          |
| **Låg cache använd procentandel**  |          Scenario 3           |          Scenario 4          |

**Scenario 1:** Du använder cacheminnet optimalt. [Felsöka](sql-data-warehouse-manage-monitor.md) andra områden som kan göra dina frågor långsammare.

**Scenario 2:** Den aktuella arbetsdatauppsättningen får inte plats i cacheminnet, vilket ger en låg träffprocent på grund av fysiska läsningar. Överväg att skala upp din prestandanivå och köra din arbetsbelastning igen för att fylla i cacheminnet.

**Scenario 3:** Det är troligt att frågan körs långsamt på grund av orsaker som inte är relaterade till cachen. [Felsöka](sql-data-warehouse-manage-monitor.md) andra områden som kan göra dina frågor långsammare. Du kan också överväga [att skala ner din instans](sql-data-warehouse-manage-monitor.md) för att minska cachestorleken för att spara kostnader. 

**Scenario 4:** Du hade en kall cache som kan vara orsaken till att frågan var långsam. Överväg att köra frågan igen eftersom arbetsdatauppsättningen nu ska cachelagras. 

> [!IMPORTANT]
> Om cacheträffprocenten eller cacheminnet inte uppdateras efter att din arbetsbelastning har körts om kan arbetsminnet redan finnas i minnet. Endast klustrade columnstore-tabeller cachelagras.

## <a name="next-steps"></a>Nästa steg
Mer information om allmän frågeprestandajustering finns i [Övervaka körning av frågor](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
