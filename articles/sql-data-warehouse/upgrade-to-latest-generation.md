---
title: Uppgradera till den senaste generationen i Azure SQL Data Warehouse | Microsoft Docs
description: Uppgradera Azure SQL Data Warehouse till senaste generationen i Azure maskin- och storage-arkitektur.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/22/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1e2993e1f4d28fd5d281ea510121686d3bc37a8c
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746970"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimera prestanda genom att uppgradera SQL Data Warehouse
Uppgradera Azure SQL Data Warehouse till senaste generationen i Azure maskin- och storage-arkitektur.

## <a name="why-upgrade"></a>Varför uppgradera?
Du kan nu sömlöst uppgradera till SQL Data Warehouse Compute Optimized Gen2 nivån i Azure-portalen. Om du har ett informationslager för Compute Optimized Gen1 nivå, rekommenderas uppgradera. Genom att uppgradera, du kan använda den senaste generationen i Azure-maskinvaran och förbättrad lagringsarkitektur. Du kan dra nytta av bättre prestanda, högre skalbarhet och obegränsad kolumnformad lagring. 

## <a name="applies-to"></a>Gäller
Den här uppgraderingen gäller för informationslager för Compute Optimized Gen1 nivå.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="before-you-begin"></a>Innan du börjar
> [!NOTE]
> Om ditt befintliga Compute Optimized Gen1 nivån data warehouse inte är i en region där Compute Optimized Gen2-nivån är tillgänglig, kan du [geo-återställning](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) via PowerShell till en region som stöds.
> 
>

1. Om datalagret Compute Optimized Gen1 nivå som ska uppgraderas är pausad, [återuppta datalagret](pause-and-resume-compute-portal.md).
2. Förberedas för ett par minuter stillestånd. 



## <a name="start-the-upgrade"></a>Starta uppgraderingen

1. Gå till din Compute Optimized Gen1 nivån datalager i Azure-portalen och klicka på **uppgradera till Gen2**: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Som standard **väljer den föreslagna prestandanivån** för datalagret baserat på din aktuella prestandanivå på Compute Optimized Gen1 nivån med hjälp av mappningen nedan:
    
   | Optimerad Gen1 Beräkningsnivån | Beräkningsnivån optimerade Gen2 |
   | :----------------------: | :-------------------: |
   |      DW100 – DW1000      |        DW1000c        |
   |          DW1200          |        DW1500c        |
   |          DW1500          |        DW1500c        |
   |          DW2000          |        DW2000c        |
   |          DW3000          |        DW3000c        |
   |          DW6000          |        DW6000c        |

3. Se till att din arbetsbelastning har slutförts körs och offline innan du uppgraderar. Du får driftstopp för ett par minuter innan datalagret är online igen som ett informationslager för Compute Optimized Gen2 nivå. **Klicka på Uppgradera**. Priset för prestandanivån för Compute Optimized Gen2 nivå är hälften för närvarande inaktiverad i förhandsversionen:
    
   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Övervaka uppgraderingen** genom att kontrollera statusen i Azure portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Det första steget i uppgraderingen går igenom skalningsåtgärden (”uppgradera - Offline”) där alla sessioner kommer att avslutas och anslutningar kommer att tas bort. 
   
   Det andra steget i uppgraderingsprocessen är datamigrering (”uppgradera - Online”). Migrering av data är en online takt bakgrundsprocess som långsamt flyttar kolumner data från den gamla lagringsarkitekturen till den nya storage-arkitekturen som att använda en lokal SSD-cache. Under denna tid kommer ditt informationslager att vara online för att fråga och läsa in. Alla dina data blir tillgängliga för frågor, oavsett om den har migrerats eller inte. Migrering av data sker på en varierande avgift beroende på datastorleken på din, din prestandanivån och antalet columnstore-segment. 

5. **Valfri rekommendation:** för att bearbeta data bakgrund migreringsprocessen kan du direkt tvinga dataförflyttning genom att köra [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) på alla primära columnstore-tabeller som du skulle köras mot vid en större Servicenivåmål och resursklass. Den här åtgärden är **offline** jämfört med den takt bakgrundsprocess som kan ta timmar att slutföra beroende på antalet och storleken på dina tabeller, migrering av data är dock mycket snabbare där du sedan kan dra full nytta Förbättrad lagringsarkitektur när du är klar med högkvalitativa radgrupper för den nya. 

Följande fråga genererar nödvändiga Alter Index Rebuild kommandon att påskynda migreringen data:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>Nästa steg
Uppgraderade datalagret är online. Om du vill dra nytta av förbättrad arkitektur, se [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).
 
