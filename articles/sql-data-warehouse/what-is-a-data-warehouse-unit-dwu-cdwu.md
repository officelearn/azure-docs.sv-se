---
title: 'Informationslagerenheter (dwu: er, cDWUs) i Azure SQL Data Warehouse | Microsoft Docs'
description: 'Rekommendationer för att välja bästa antalet informationslagerenheter (dwu: er, cDWUs) för att optimera pris och prestanda och hur du ändrar antalet enheter.'
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 94791e4dc3d3c841dde4685d34d4e3fdaf7d9af7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185968"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Informationslagerenheter (dwu: er) och beräkning Informationslagerenheter (cDWUs)
Rekommendationer för att välja bästa antalet informationslagerenheter (dwu: er, cDWUs) för att optimera pris och prestanda och hur du ändrar antalet enheter. 

## <a name="what-are-data-warehouse-units"></a>Vad är Informationslagerenheter?
Med SQL Data Warehouse CPU, minne och i/o är paketerade i enheter för beräkning skala kallas Informationslagerenheter (dwu: er). En DWU är en abstrakt, normaliserad mått av beräkningsresurser och prestanda. Genom att ändra din servicenivå ändra du antalet dwu: er som är allokerade till systemet, vilket i sin tur justerar prestanda och kostnad för systemet. 

Du kan öka antalet informationslagerenheter för att betala för högre prestanda. Minska informationslagerenheter för att betala för lägre prestanda. Kostnader för lagring och beräkning faktureras separat, så ändrar informationslagerenheter inte påverkas lagringskostnader.

Prestanda för informationslagerenheter baserat på dessa arbetsbelastning måtten i informationslager:

- Hur snabbt kan en standard datalagring datafrågor skanna ett stort antal rader och utför sedan en komplex aggregering? Denna åtgärd är i/o- och CPU-intensiv.
- Hur snabbt kan datalagret mata in data från Azure Storage-Blobbar eller Azure Data Lake? Denna åtgärd är nätverks- och CPU-intensiv. 
- Hur snabbt kan den [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-kommandot Kopiera en tabell? Den här åtgärden innebär att läsa data från lagring, distribuera den över enhetens noder och skriva till lagring igen. Denna åtgärd är CPU, IO och belasta nätverket.

Öka dwu: er:
- Linjärt ändras prestanda för sökningar, aggregeringar och CTAS uttryck
- Ökar antalet läsare och skrivare för PolyBase belastningen åtgärder
- Ökar det maximala antalet samtidiga frågor och samtidighet platser.

## <a name="service-level-objective"></a>Servicenivåmål
Den artikel för servicenivåmål (SNM) är skalbarhet-inställning som anger nivån kostnad och prestanda för ditt informationslager. Servicenivåer för Gen2 mäts i beräkning informationslagerenheter (cDWU), till exempel DW2000c. Gen1 servicenivåer mäts i dwu: er, till exempel DW2000. 

I T-SQL anger inställningen SERVICE_OBJECTIVE servicenivån och nivå för prestanda för ditt informationslager.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Prestandanivåer och Informationslagerenheter

Varje prestandanivån använder en något annorlunda måttenhet för sina informationslagerenheter. Denna skillnad visas på fakturan som enheten skala översätter direkt till fakturering.

- Gen1 informationslager mäts i Informationslagerenheter (dwu: er).
- Gen2 data warehousesr mäts i beräkning Informationslagerenheter (cDWUs). 

Både dwu: er och cDWUs stöd för skalning beräkning uppåt eller nedåt och pausa compute när du inte behöver använda datalagret. Dessa åtgärder är alla på begäran. Gen2 använder ett lokalt diskbaserad cacheminne på datornoderna för att förbättra prestanda. När du skalar eller pausa systemet cachen betraktas som inaktuella och så en period av cache värma krävs innan optimala prestanda uppnås.  

Om du ökar informationslagerenheter ökar linjärt du datorresurser. Gen2 ger bästa frågeprestanda och högsta skala men har en högre pris. Det är utformat för företag som har ett konstant krav på prestanda. Dessa system se de flesta användning av cachen. 

### <a name="capacity-limits"></a>Kapacitetsbegränsningar
Varje SQLServer (till exempel myserver.database.windows.net) har en [Database Transaction Units (DTU)](../sql-database/sql-database-what-is-a-dtu.md) kvot som gör att ett visst antal informationslagerenheter. Mer information finns i [arbetsbelastning management kapacitetsbegränsningar](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Hur många informationslagerenheter behöver jag?
Idealiskt antalet informationslagerenheter beror mycket på din arbetsbelastning och mängden data som du har läst in i systemet.

Steg för att hitta den bästa DWU för din arbetsbelastning:

1. Börja genom att välja en mindre DWU. 
2. Övervaka programmets prestanda när du testar databelastningar i systemet, sett antalet dwu: er som valts jämfört med prestanda du se.
3. Identifiera eventuella ytterligare krav för periodiska perioder med hög aktivitet. Om arbetsbelastningen visar betydande toppar och tvättställ i aktiviteten och det finns goda skäl att skala ofta.

SQL Data Warehouse är ett skalbart system som kan etablera stora mängder beräknings- och betydande mängder data. Om du vill visa dess true funktioner för skalning, speciellt vid större dwu: er, rekommenderar vi skalning datauppsättningen när du skalar för att säkerställa att du har tillräckligt med data att mata in alla processorer. För att skala testa bör du använda minst 1 TB.

> [!NOTE]
>
> Frågeprestanda ökar med mer parallellisering bara om arbetet som kan delas mellan beräkningsnoder. Om du upptäcker att skalning inte är ändra din prestanda kan behöva du finjustera din tabelldesign och/eller dina frågor. Frågan justera vägledning, se [hantera användarfrågor](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Behörigheter

Ändra informationslagerenheter kräver behörigheterna som beskrivs i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

## <a name="view-current-dwu-settings"></a>Visa aktuella DWU-inställningar

Visa aktuella DWU-inställningar:

1. Öppna SQL Server Object Explorer i Visual Studio.
2. Ansluta till master-databasen som är kopplade till den logiska SQL Database-servern.
3. Välj vyn sys.database_service_objectives dynamisk hantering. Här är ett exempel: 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Ändra informationslagerenheter

### <a name="azure-portal"></a>Azure Portal
Ändra dwu: er eller cDWUs:

1. Öppna den [Azure-portalen](https://portal.azure.com), öppna databasen och på **skala**.

2. Under **skala**, flytta skjutreglaget åt vänster eller höger för att ändra DWU-inställningen.

3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Klicka på **Ja** för att bekräfta eller **Nej** för att avbryta.

### <a name="powershell"></a>PowerShell
Ändra dwu: er eller cDWUs genom att använda den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell-cmdlet. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW som finns på servern minserver-databasen.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Mer information finns i [PowerShell-cmdlets för SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
Med T-SQL kan du visa de aktuella inställningarna för DWU eller cDWU, ändra inställningarna och kontrollera förloppet. 

Ändra dwu: er eller cDWUs:

1. Ansluta till master-databasen som är associerade med din logiska SQL Database-server.
2. Använd den [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL-instruktion. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW-databasen. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API:er

Du kan ändra de dwu: er i [skapa eller uppdatera databasen](/rest/api/sql/databases/createorupdate) REST API. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW som finns på servern minserver-databasen. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Fler REST API-exempel finns [REST API: er för SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Kontrollera status för DWU ändras

DWU ändringar kan ta flera minuter att slutföra. Om du skalning automatiskt, Överväg att implementera logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd. 

Kontrollera databasens status via olika slutpunkter kan du implementera korrekt automation. Portalen ger aviseringar när en åtgärd och det aktuella tillståndet för databaser, men tillåter inte att programmatiskt kontroll av tillstånd. 

Du kan inte kontrollera databasens status för skalbar åtgärder med Azure-portalen.

Kontrollera status för DWU ändras:

1. Ansluta till master-databasen som är associerade med din logiska SQL Database-server.
2. Skicka följande fråga för att kontrollera databasens status.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Skicka följande fråga för att kontrollera status för åtgärden

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Den här DMV returnerar information om hantering av olika åtgärder på ditt SQL Data Warehouse, till exempel igen och tillstånd för åtgärden, vilket antingen är IN_PROGRESS eller SLUTFÖRTS.

## <a name="the-scaling-workflow"></a>Skalning arbetsflödet

När du initierar en skalningsåtgärden stängs systemet först alla öppna sessioner, återställer alla öppna transaktioner för att säkerställa ett konsekvent tillstånd. För skala inträffar skalning bara när den här transaktionella återställning har slutförts.  

- En skala upp åtgärd system bestämmelserna ytterligare compute och sedan reattaches lagring-lagret. 
- För en skala ned-åtgärd på onödiga noder kopplar du från lagringen och återansluta till övriga noder.

## <a name="next-steps"></a>Nästa steg
Läs mer om att hantera prestanda i [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md) och [minne och samtidighet gränser](memory-and-concurrency-limits.md).



