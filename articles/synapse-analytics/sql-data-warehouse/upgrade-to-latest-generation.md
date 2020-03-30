---
title: Uppgradera till den senaste generationen
description: Uppgradera Azure Synapse Analytics SQL-pool till den senaste generationen av Azure-maskinvaru- och lagringsarkitektur.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 637e377e469eeb1a82b6c0ad3a845d94ac09c7db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351204"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Optimera prestanda genom att uppgradera Azure Synapse Analytics SQL-pool

Uppgradera SQL-poolen till den senaste generationen av Azure-maskinvaru- och lagringsarkitektur.

## <a name="why-upgrade"></a>Varför uppgradera?

Du kan nu smidigt uppgradera till SQL-poolen Compute Optimized Gen2-nivån i Azure-portalen för [regioner som stöds](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Om din region inte stöder självuppgradering kan du uppgradera till en region som stöds eller vänta på att självuppgraderingen är tillgänglig i din region. Uppgradera nu för att dra nytta av den senaste generationen av Azure-maskinvara och förbättrad lagringsarkitektur, inklusive snabbare prestanda, högre skalbarhet och obegränsad kolumnlagring. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Gäller

Den här uppgraderingen gäller för SQL-pooler på beräkningsoptimerad gen1-nivå i [regioner som stöds](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Innan du börjar

1. Kontrollera om [din region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) stöds för GEN1 till GEN2-migrering. Observera de automatiska migreringsdatumen. Om du vill undvika konflikter med den automatiska processen planerar du den manuella migreringen före den automatiska processens startdatum.
2. Om du befinner dig i en region som ännu inte stöds fortsätter du att kontrollera att regionen ska läggas till eller [uppgraderas med återställning](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) till en region som stöds.
3. Om din region stöds [uppgraderar du via Azure-portalen](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Välj den föreslagna prestandanivån** för SQL-pool baserat på din aktuella prestandanivå på beräkningsoptimerad gen1-nivå med hjälp av mappningen nedan:

   | Beräkna optimerad Gen1-nivå | Beräkna optimerad Gen2-nivå |
   | :-------------------------: | :-------------------------: |
   |            DW100 (PÅ)            |           DW100c (på andra)            |
   |            DW200 (PÅ)            |           DW200c (på andra)            |
   |            DW300            |           DW300c (på andra)            |
   |            DW400            |           DW400c (på andra)            |
   |            DW500 (PÅ)            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> Föreslagna prestandanivåer är inte en direkt konvertering. Vi rekommenderar till exempel att du går från DW600 till DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Uppgradera i en region som stöds med Azure-portalen

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Migrering från Gen1 till Gen2 via Azure-portalen är permanent. Det finns ingen process för att återvända till Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

1. Om den beräkningsoptimerade gen1-nivå SQL-poolen som ska uppgraderas pausas [återupptar du SQL-poolen](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > SQL-poolen måste köras för att migrera till Gen2.

2. Var beredd på några minuters driftstopp. 

3. Identifiera eventuella kodreferenser till beräkningsoptimerade gen1-prestandanivåer och ändra dem till motsvarande beräkningsoptimerad Gen2-prestandanivå. Nedan följer två exempel på var du bör uppdatera kodreferenser innan du uppgraderar:

   Kommandot Original Gen1 PowerShell:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Ändrad till:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" ändras till - RequestedServiceObjectiveName "DW300**c**"
   >

   Ursprungligt Gen1 T-SQL-kommando:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Ändrad till:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = "DW300" ändras till SERVICE_OBJECTIVE = "DW300**c"**

## <a name="start-the-upgrade"></a>Starta uppgraderingen

1. Gå till din Compute Optimized Gen1 SQL-pool i Azure-portalen. Om den beräkningsoptimerade gen1-nivå SQL-poolen som ska uppgraderas pausas [återupptar du SQL-poolen](pause-and-resume-compute-portal.md). 
2. Välj **Uppgradera till Gen2-kort** ![under fliken Uppgifter: Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)
    
    > [!NOTE]
    > Om kortet Uppgradera **till Gen2** inte visas under fliken Uppgifter är prenumerationstypen begränsad i den aktuella regionen.
    > [Skicka in en supportbiljett](sql-data-warehouse-get-started-create-support-ticket.md) för att få din prenumeration vitlistad.

3. Se till att din arbetsbelastning har körts och quiesced innan du uppgraderar. Du får driftstopp i några minuter innan SQL-poolen är online igen som en SQL-pool på Beräkningsoptimerad Gen2-nivå. **Välj Uppgradering:**

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Övervaka uppgraderingen** genom att kontrollera statusen i Azure-portalen:

   ![Uppgradera3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   Det första steget i uppgraderingsprocessen går igenom skalningsåtgärden ("Uppgradering - Offline") där alla sessioner kommer att dödas och anslutningar kommer att tas bort. 

   Det andra steget i uppgraderingsprocessen är datamigrering ("Uppgradering - Online"). Datamigrering är en online trickle bakgrundsprocess. Den här processen flyttar långsamt kolumndata från den gamla lagringsarkitekturen till den nya lagringsarkitekturen med hjälp av en lokal SSD-cache. Under den här tiden är din SQL-pool online för frågor och inläsning. Dina data kommer att vara tillgängliga för frågor oavsett om de har migrerats eller inte. Datamigrering sker med varierande takt beroende på din datastorlek, din prestandanivå och antalet segment i din columnstore. 

5. **Valfri rekommendation:** När skalningsåtgärden är klar kan du påskynda bakgrundsprocessen för datamigrering. Du kan tvinga fram dataförflyttningar genom att köra [Återskapa Alter Index](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller som du skulle fråga efter på en större SLO- och resursklass. Den här åtgärden är **offline** jämfört med trickle bakgrundsprocessen, vilket kan ta timmar att slutföra beroende på antalet och storleken på dina tabeller. När du är klar kommer datamigrering dock att gå mycket snabbare på grund av den nya förbättrade lagringsarkitekturen med högklassiga roddgrupper.
 
> [!NOTE]
> Alter Index återskapa är en offline-åtgärd och tabellerna kommer inte att vara tillgängliga förrän ombyggnaden är klar.

Följande fråga genererar de kommandon som krävs ändra indexombyggnad för att påskynda datamigrering:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Uppgradera från en geografisk Azure-region med återställning via Azure-portalen

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Skapa en användardefinierad återställningspunkt med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Navigera till den SQL-pool som du vill skapa en återställningspunkt för.

3. Högst upp i avsnittet Översikt väljer du **+Ny återställningspunkt**.

    ![Ny återställningspunkt](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Ange ett namn på återställningspunkten.

    ![Namn på återställningspunkt](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Återställa en aktiv eller pausad databas med Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Navigera till den SQL-pool som du vill återställa från.
3. Högst upp i avsnittet Översikt väljer du **Återställ**.

    ![ Återställa översikt](./media/upgrade-to-latest-generation/restoring_0.png)

4. Välj antingen **Automatiska återställningspunkter** eller **användardefinierade återställningspunkter**. För användardefinierade återställningspunkter **väljer du en användardefinierad återställningspunkt** eller **Skapa en ny användardefinierad återställningspunkt**. För servern väljer du **Skapa ny** och väljer en server i ett geografiskt område som stöds av Gen2. 

    ![Automatiska återställningspunkter](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Återställa från en geografisk Azure-region med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill återställa en databas använder du cmdleten [Restore-AzSqlDatabase.](/powershell/module/az.sql/restore-azsqldatabase)

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Om du vill göra det anger du ett Gen2 ServiceObjectiveName (t.ex.**c**

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
3. Välj den prenumeration som innehåller databasen som ska återställas.
4. Hämta databasen som du vill återställa.
5. Skapa återställningsbegäran för databasen och ange ett Gen2 ServiceObjectiveName.
6. Kontrollera status för den geoåterda databasen.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Information om hur du konfigurerar databasen när återställningen har slutförts finns i [Konfigurera databasen efter återställning](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

Den återställda databasen är TDE-aktiverad om källdatabasen är TDE-aktiverad.


Om du får problem med din SQL-pool kan du skapa en [supportbegäran](sql-data-warehouse-get-started-create-support-ticket.md) och referera till "Gen2-uppgradering" som möjlig orsak.

## <a name="next-steps"></a>Nästa steg

Din uppgraderade SQL-pool är online. Om du vill dra nytta av den förbättrade arkitekturen finns i [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md).
