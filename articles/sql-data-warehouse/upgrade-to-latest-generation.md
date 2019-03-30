---
title: Uppgradera till den senaste generationen i Azure SQL Data Warehouse | Microsoft Docs
description: Uppgradera Azure SQL Data Warehouse till senaste generationen i Azure maskin- och storage-arkitektur.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: 522f1f2f24e8c8c3f68a42569d4057a7694754d1
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651081"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimera prestanda genom att uppgradera SQL Data Warehouse

Uppgradera Azure SQL Data Warehouse till senaste generationen i Azure maskin- och storage-arkitektur.

## <a name="why-upgrade"></a>Varför uppgradera?

Du kan nu uppgradera sömlöst till SQL Data Warehouse Compute Optimized Gen2 nivån i Azure-portalen för [regioner som stöds](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Om din region inte har stöd för lokal uppgradera, kan du uppgradera till en region som stöds eller vänta tills själva uppgradera ska vara tillgängliga i din region. Uppgradera nu för att dra nytta av den senaste generationen i Azure-maskinvaran och bättre lagringsarkitektur inklusive snabbare prestanda, högre skalbarhet och obegränsad kolumnformad lagring. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Gäller

Den här uppgraderingen gäller för Compute Optimized Gen1 nivån informationslager i [regioner som stöds](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Innan du börjar

1. Kontrollera om din [region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) stöds för GEN1 till GEN2 migrering. Obs automatisk migrering datumen. Planera din manuell migrering före startdatumet automatiserad process för att undvika konflikter med den automatiska processen.
2. Om du är i en region som inte stöds ännu kan fortsätta att söka efter din region som ska läggas till eller [uppgradera med hjälp av restore](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) till en region som stöds.
3. Om din region stöds [uppgradera via Azure portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Välj den föreslagna prestandanivån** för datalagret baserat på din aktuella prestandanivå på Compute Optimized Gen1 nivån med hjälp av mappningen nedan:

   | Optimerad Gen1 Beräkningsnivån | Beräkningsnivån optimerade Gen2 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> Föreslagna prestandanivåer är inte en direkt konvertering. Exempelvis rekommenderar vi gå från DW600 till DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Uppgradera i en region som stöds med hjälp av Azure portal

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Migrering från Gen1 till Gen2 via Azure portal är permanent. Det finns inte en process för att återgå till Gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

1. Om datalagret Compute Optimized Gen1 nivå som ska uppgraderas är pausad, [återuppta datalagret](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Azure SQL Data Warehouse måste köras för att migrera till Gen2.

2. Förberedas för ett par minuter stillestånd. 

3. Identifiera alla kod referenser till Compute Optimized Gen1 prestandanivåer och ändra dem till sina motsvarande Compute Optimized Gen2 prestandanivå. Nedan följer två exempel på var du bör uppdatera kod referenser innan du uppgraderar:

   Ursprungliga Gen1 PowerShell-kommando:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Ändrad till:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName ”DW300” ändras till - RequestedServiceObjectiveName ”DW300**c**”
   >

   Ursprungliga Gen1 T-SQL-kommando:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Ändrad till:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = 'DW300' ändras till SERVICE_OBJECTIVE = ' DW300**c**'

## <a name="start-the-upgrade"></a>Starta uppgraderingen

1. Gå till Compute Optimized Gen1 nivån data warehouse i Azure-portalen. Om datalagret Compute Optimized Gen1 nivå som ska uppgraderas är pausad, [återuppta datalagret](pause-and-resume-compute-portal.md). 
2. Välj **uppgradera till Gen2** kortet under fliken aktiviteter:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Om du inte ser den **uppgradera till Gen2** kortet under fliken aktiviteter, typ av prenumeration är begränsad i den aktuella regionen.
    > [Skicka in ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md) att hämta din prenumeration i listan över godkända.

3. Se till att din arbetsbelastning har slutförts körs och offline innan du uppgraderar. Du kommer drabbas för ett par minuter innan datalagret är online igen som ett informationslager för Compute Optimized Gen2 nivå. **Välj uppgradering**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Övervaka uppgraderingen** genom att kontrollera statusen i Azure portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Det första steget i uppgraderingen går igenom skalningsåtgärden (”uppgradera - Offline”) där alla sessioner kommer att avslutas och anslutningar kommer att tas bort. 

   Det andra steget i uppgraderingsprocessen är datamigrering (”uppgradera - Online”). Migrering av data är en online takt bakgrundsprocess. Den här processen flyttar långsamt petabyteskalade kolumndata från den gamla lagringsarkitekturen till den nya storage-arkitekturen med en lokal SSD-cache. Under denna tid kommer ditt informationslager att vara online för att fråga och läsa in. Dina data ska vara tillgängliga för frågor, oavsett om den har migrerats eller inte. Migrering av data sker på olika priser beroende på datastorleken på din, din prestandanivån och antalet columnstore-segment. 

5. **Valfri rekommendation:** När skalning åtgärden har slutförts, kan du påskynda bakgrund migrerar data. Du kan tvinga dataförflyttning genom att köra [Alter Index rebuild](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller du skulle köras mot en större SLO och resurs-klass. Den här åtgärden är **offline** jämfört med bakgrundsprocess takt, vilket kan ta timmar att slutföra beroende på antalet och storleken på dina tabeller. När du är klar kommer datamigrering dock mycket snabbare på grund av den nya arkitekturen i Förbättrad lagring med högkvalitativa radgrupper.
 
> [!NOTE]
> ALTER Index rebuild är en offline-åtgärd och tabellerna är inte tillgängliga förrän återskapandet har slutförts.

Följande fråga genererar nödvändiga Alter Index Rebuild kommandon att påskynda migrering av data:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Uppgradera från en Azure-geografisk region med hjälp av återställning via Azure portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Skapa en användardefinierad återställningspunkt med hjälp av Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Gå till SQL data warehouse som du vill skapa en återställningspunkt för.

3. Högst upp i översiktsavsnittet, Välj **+ ny återställningspunkt**.

    ![Ny återställningspunkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Ange ett namn för återställningspunkten.

    ![Namnet på återställningspunkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Återställ en aktiv eller pausad-databas med Azure portal

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till SQL data warehouse som du vill återställa från.
3. Högst upp i översiktsavsnittet, Välj **återställa**.

    ![ Återställa översikt](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Välj antingen **automatisk återställningspunkter** eller **användardefinierade återställningspunkter**.

    ![Automatiska återställningspunkter](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. För användardefinierade återställningspunkter **Välj en återställningspunkt** eller **skapa en ny användardefinierad återställningspunkt**. Välj en server i en Gen2 geografiska region som stöds. 

    ![Användardefinierade återställningspunkter](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Återställa från en Azure-geografisk region med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill återställa en databas kan använda den [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) cmdlet.

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Du gör detta genom att ange en Gen2 ServiceObjectiveName (t.ex. DW1000**c**) som en valfri parameter.

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista över alla prenumerationer som är associerat med ditt konto.
3. Välj den prenumeration som innehåller databasen som skulle återställas.
4. Hämta den databas som du vill återställa.
5. Skapa recovery-begäran för databasen att ange en Gen2 ServiceObjectiveName.
6. Kontrollera status för geo-återställa databasen.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> För att konfigurera databasen efter återställningen har slutförts, se [konfigurera databasen efter återställningen](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

Den återställda databasen kommer att TDE-aktiverade om källdatabasen är TDE-aktiverad.


Om du får problem med ditt data warehouse, skapa en [supportförfrågan](sql-data-warehouse-get-started-create-support-ticket.md) och referera till ”Gen2 uppgraderingen” som möjlig orsak.

## <a name="next-steps"></a>Nästa steg

Uppgraderade datalagret är online. Om du vill dra nytta av förbättrad arkitektur, se [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).
