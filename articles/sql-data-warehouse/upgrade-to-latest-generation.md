---
title: Uppgradera till den senaste generationen Azure SQL Data Warehouse | Microsoft Docs
description: Uppgradera Azure SQL Data Warehouse till den senaste generationen av Azures maskinvaru-och lagrings arkitektur.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: 2864e3d29a0beccd2ef52732a85ea1495e1efab8
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575297"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Optimera prestanda genom att uppgradera SQL Data Warehouse

Uppgradera Azure SQL Data Warehouse till den senaste generationen av Azures maskinvaru-och lagrings arkitektur.

## <a name="why-upgrade"></a>Varför uppgradera?

Nu kan du sömlöst uppgradera till den SQL Data Warehouse Compute-optimerade Gen2-nivån i Azure Portal för [regioner som stöds](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Om din region inte stöder själv uppgradering kan du uppgradera till en region som stöds eller vänta tills själv uppgradering är tillgänglig i din region. Uppgradera nu för att dra nytta av den senaste generationen Azure-maskinvara och förbättrad lagrings arkitektur, inklusive snabbare prestanda, högre skalbarhet och obegränsad kolumn lagring. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Gäller

Den här uppgraderingen gäller för data lager som är optimerade på gen1 nivå i [regioner som stöds](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Innan du börjar

1. Kontrol lera om din [region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) stöds för GEN1 till Gen2-migrering. Anteckna datum för automatisk migrering. För att undvika konflikter med den automatiserade processen bör du planera din manuella migrering innan start datumet för automatisk process.
2. Om du befinner dig i en region som ännu inte stöds fortsätter du att söka efter din region som ska läggas till eller [uppgraderas med hjälp av](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) Restore till en region som stöds.
3. Om din region stöds [uppgraderar du genom Azure Portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Välj den föreslagna prestanda nivån** för informations lagret baserat på din aktuella prestanda nivå på Compute-optimerade gen1-nivåer med hjälp av mappningen nedan:

   | Compute-optimerad gen1-nivå | Compute-optimerad Gen2-nivå |
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
> Föreslagna prestanda nivåer är inte en direkt konvertering. Vi rekommenderar till exempel att du går från DW600 till DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Uppgradera i en region som stöds med hjälp av Azure Portal

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Migrering från gen1 till Gen2 via Azure Portal är permanent. Det finns ingen process för att återgå till gen1.  

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

1. Om det data lager som ska uppgraderas har pausats på det beräknade gen1, [återupptar du data lagret](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Azure SQL Data Warehouse måste köras för att migrera till Gen2.

2. Var beredd på några minuter av stillestånds tid. 

3. Identifiera eventuella kod referenser för att kunna beräkna optimerade prestanda nivåer för gen1 och ändra dem till motsvarande Compute-optimerade Gen2 prestanda nivå. Nedan visas två exempel på var du bör uppdatera kod referenser innan du uppgraderar:

   Ursprungligt gen1 PowerShell-kommando:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Ändrat till:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" ändras till-RequestedServiceObjectiveName "DW300**c**"
   >

   Ursprungligt gen1 T-SQL-kommando:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Ändrat till:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = ' DW300 ' har ändrats till SERVICE_OBJECTIVE = ' DW300**c**'

## <a name="start-the-upgrade"></a>Starta uppgraderingen

1. Gå till ditt Compute-optimerade gen1 nivå informations lager i Azure Portal. Om det data lager som ska uppgraderas har pausats på det beräknade gen1, [återupptar du data lagret](pause-and-resume-compute-portal.md). 
2. Välj **Uppgradera till Gen2** -kortet under fliken aktiviteter:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Om du inte ser kortet **Uppgradera till Gen2** på fliken aktiviteter är prenumerations typen begränsad i den aktuella regionen.
    > [Skicka in ett support ärende](sql-data-warehouse-get-started-create-support-ticket.md) för att hämta din prenumerations vit listas.

3. Se till att arbets belastningen har slutförts och är offline innan du uppgraderar. Du kommer att uppleva drift stopp i några minuter innan ditt informations lager är online igen som ett data lager för data behandling som är optimerat på Gen2 nivå. **Välj uppgradering**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Övervaka uppgraderingen** genom att kontrol lera statusen i Azure Portal:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Det första steget i uppgraderings processen går igenom skalnings åtgärden ("uppgradering – offline") där alla sessioner kommer att avlivas och anslutningarna kommer att tas bort. 

   Det andra steget i uppgraderings processen är datamigrering ("uppgradering – online"). Datamigrering är en bakgrunds process online trickle. Den här processen flyttar långsamt kolumn data från den gamla lagrings arkitekturen till den nya lagrings arkitekturen med hjälp av en lokal SSD-cache. Under den här tiden är ditt informations lager online för frågor och inläsning. Dina data är tillgängliga för frågor oavsett om de har migrerats eller inte. Datamigreringen sker i olika hastigheter beroende på din data storlek, prestanda nivå och antalet dina columnstore-segment. 

5. **Valfri rekommendation:** När skalnings åtgärden är klar kan du påskynda bakgrunds processen för datamigrering. Du kan tvinga data förflyttning genom att köra [Alter index Rebuild](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller som du frågar efter en större service nivå mål och resurs klass. Den här åtgärden är frånkopplad till bakgrunds processen trickle, vilket kan ta timmar att slutföra beroende på antalet och storleken på dina tabeller. Men när det är klart kommer migreringen att bli mycket snabbare på grund av den nya förbättrad lagrings arkitekturen med högkvalitativa av hög kvalitet.
 
> [!NOTE]
> Alter index Rebuild är en offline-åtgärd och tabellerna kommer inte att vara tillgängliga förrän återställningen har slutförts.

Följande fråga genererar nödvändiga kommandon för att bygga om Alter index för att påskynda datamigreringen:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Uppgradera från ett geografiskt Azure-område med hjälp av Restore via Azure Portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Skapa en användardefinierad återställnings punkt med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Navigera till SQL Data Warehouse som du vill skapa en återställnings punkt för.

3. Överst i översikts avsnittet väljer du **+ ny återställnings punkt**.

    ![Ny återställningspunkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Ange ett namn för återställnings punkten.

    ![Namn på återställnings punkt](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Återställa en aktiv eller pausad databas med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Navigera till SQL Data Warehouse som du vill återställa från.
3. Överst i översikts avsnittet väljer du **Återställ**.

    ![ Återställa översikt](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Välj antingen **automatiska återställnings punkter** eller **användardefinierade återställnings punkter**.

    ![Automatiska återställningspunkter](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. För användardefinierade återställnings punkter **väljer du en återställnings punkt** eller **skapar en ny användardefinierad återställnings punkt**. Välj en server i en geografisk region som stöds av Gen2. 

    ![Användardefinierade återställnings punkter](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Återställa från en geografisk Azure-region med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill återställa en databas använder du cmdleten Restore [-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) .

> [!NOTE]
> Du kan utföra en geo-återställning till Gen2! Det gör du genom att ange en Gen2-ServiceObjectiveName (t. ex. DW1000**c**) som en valfri parameter.

1. Öppna Windows PowerShell.
2. Anslut till ditt Azure-konto och lista alla prenumerationer som är kopplade till ditt konto.
3. Välj den prenumeration som innehåller den databas som ska återställas.
4. Hämta databasen som du vill återställa.
5. Skapa en återställnings förfrågan för databasen och ange en Gen2-ServiceObjectiveName.
6. Kontrol lera statusen för den geo-återställda databasen.

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
> Information om hur du konfigurerar databasen när återställningen har slutförts finns i [Konfigurera databasen efter](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)återställningen.

Den återställda databasen kommer att TDEs om käll databasen är TDE-aktive rad.


Om du får problem med ditt informations lager kan du skapa en [support förfrågan](sql-data-warehouse-get-started-create-support-ticket.md) och en referens "Gen2 uppgradering" som möjlig orsak.

## <a name="next-steps"></a>Nästa steg

Det uppgraderade data lagret är online. För att dra nytta av den förbättrade arkitekturen, se [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md).
