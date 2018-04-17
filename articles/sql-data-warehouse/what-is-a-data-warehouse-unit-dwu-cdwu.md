---
title: 'Vad är Informationslagerenheter (dwu: er, cDWUs) i Azure SQL Data Warehouse? | Microsoft Docs'
description: 'Prestanda skala ut funktioner i Azure SQL Data Warehouse. Skala ut genom att justera dwu: er, cDWUs, eller pausa och återuppta beräkningsresurser för att spara kostnader.'
services: sql-data-warehouse
author: sqlmojo
manager: craigg-msft
ms.topic: conceptual
ms.component: manage
ms.date: 04/09/2018
ms.author: joeyong
ms.reviewer: jrj
ms.openlocfilehash: 56d59be2074a3047ce19fde3e808354266040864
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Informationslagerenheter (dwu: er) och beräkning Informationslagerenheter (cDWUs)
Beskriver Informationslagerenheter (dwu: er) och beräkna Informationslagerenheter (cDWUS) för Azure SQL Data Warehouse. Innehåller rekommendationer för att välja bästa antalet informationslagerenheter och hur du ändrar antalet. 

## <a name="what-are-data-warehouse-units"></a>Vad är Informationslagerenheter?
Med SQL Data Warehouse CPU, minne och i/o är paketerade i enheter för beräkning skala kallas Informationslagerenheter (dwu: er). En DWU är en abstrakt, normaliserad mått av beräkningsresurser och prestanda. Genom att ändra din servicenivå ändra du antalet dwu: er som är allokerade till systemet, vilket i sin tur justerar prestanda och kostnad för systemet. 

Du kan öka antalet informationslagerenheter för att betala för högre prestanda. Minska informationslagerenheter för att betala för lägre prestanda. Kostnader för lagring och beräkning faktureras separat, så ändrar informationslagerenheter inte påverkas lagringskostnader.

Prestanda för informationslagerenheter baserat på dessa arbetsbelastning måtten i informationslager:

- Hur snabbt kan en standard datalagring datafrågor skanna ett stort antal rader och utför sedan en komplex aggregering? Denna åtgärd är i/o- och CPU-intensiv.
- Hur snabbt kan datalagret mata in data från Azure Storage-Blobbar eller Azure Data Lake? Denna åtgärd är nätverks- och CPU-intensiv. 
- Hur snabbt kan den [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-kommandot Kopiera en tabell? Den här åtgärden innebär att läsa data från lagring, distribuera den över enhetens noder och skriva till lagring igen. Denna åtgärd är CPU, IO och belasta nätverket.

Öka dwu: er:
- Linjärt ändras prestanda för sökningar, aggregeringar och CTAS uttryck
- Ökar antalet läsare och skrivare för PolyBase belastningen åtgärder
- Ökar det maximala antalet samtidiga frågor och samtidighet platser.

## <a name="service-level-objective"></a>Servicenivåmål
Den artikel för servicenivåmål (SNM) är skalbarhet-inställning som anger nivån kostnad och prestanda för ditt informationslager. Servicenivåer för optimerad för beräkning prestanda nivå skala mäts i beräkning informationslagerenheter (cDWU), till exempel DW2000c. Optimerad för elasticitet servicenivåer mäts i dwu: er, till exempel DW2000. 

I T-SQL anger inställningen SERVICE_OBJECTIVE servicenivån och nivå för prestanda för ditt informationslager.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Prestandanivåer och Informationslagerenheter

Varje prestandanivån använder en något annorlunda måttenhet för sina informationslagerenheter. Denna skillnad visas på fakturan som enheten skala översätter direkt till fakturering.

- Den optimerade för elasticitet prestandanivån mäts i Informationslagerenheter (dwu: er).
- Den optimerade compute för beräkning prestandanivån mäts i-Informationslagerenheter (cDWUs). 

Både dwu: er och cDWUs stöd för skalning beräkning uppåt eller nedåt och pausa compute när du inte behöver använda datalagret. Dessa åtgärder är alla på begäran. Den optimerade för beräkning prestandanivån använder också ett lokalt diskbaserad cacheminne på datornoderna för att förbättra prestanda. När du skalar eller pausa systemet cachen betraktas som inaktuella och så en period av cache värma krävs innan optimala prestanda uppnås.  

Om du ökar informationslagerenheter ökar linjärt du datorresurser. Den optimerade för beräkning prestandanivån ger bästa frågeprestanda och högsta skala men har en högre pris. Det är utformat för företag som har ett konstant krav på prestanda. Dessa system se de flesta användning av cachen. 

### <a name="capacity-limits"></a>Kapacitetsbegränsningar
Varje SQLServer (till exempel myserver.database.windows.net) har en [Database Transaction Units (DTU)](../sql-database/sql-database-what-is-a-dtu.md) kvot som gör att ett visst antal informationslagerenheter. Mer information finns i [arbetsbelastning management kapacitetsbegränsningar](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Hur många informationslagerenheter behöver jag?
Idealiskt antalet informationslagerenheter beror mycket på din arbetsbelastning och mängden data som du har läst in i systemet.

Steg för att hitta den bästa DWU för din arbetsbelastning:

1. Börja genom att välja en mindre DWU med hjälp av den optimerade för elasticitet prestandanivån under utvecklingen.  Eftersom problem i det här skedet funktionella validering är optimerad för elasticitet prestandanivån rimliga alternativet. En bra utgångspunkt är DW200. 
2. Övervaka programmets prestanda när du testar databelastningar i systemet, sett antalet dwu: er som valts jämfört med prestanda du se.
3. Identifiera eventuella ytterligare krav för periodiska perioder med hög aktivitet. Om arbetsbelastningen visar betydande toppar och tvättställ i aktiviteten och det finns goda skäl för att skala ofta och sedan ge företräde åt den optimerade för elasticitet prestandanivå.
4. Om du behöver mer än 1000 DWU sedan ge företräde åt optimerad för beräkning prestandanivån eftersom det ger bästa prestanda.

SQL Data Warehouse är ett skalbart system som kan etablera stora mängder beräknings- och betydande mängder data. Om du vill visa dess true funktioner för skalning, speciellt vid större dwu: er, rekommenderar vi skalning datauppsättningen när du skalar för att säkerställa att du har tillräckligt med data att mata in alla processorer. För att skala testa bör du använda minst 1 TB.

> [!NOTE]
>
> Frågeprestanda ökar med mer parallellisering bara om arbetet som kan delas mellan beräkningsnoder. Om du upptäcker att skalning inte är ändra din prestanda kan behöva du finjustera din tabelldesign och/eller dina frågor. För frågan justera vägledning, se följande [prestanda](sql-data-warehouse-overview-manage-user-queries.md) artiklar. 

## <a name="permissions"></a>Behörigheter

Ändra informationslagerenheter kräver behörigheterna som beskrivs i [ALTER DATABASE][ALTER DATABASE]. 

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

3. Klicka på **Spara**. Ett meddelande visas. Klicka på **Ja** att bekräfta eller **inga** att avbryta.

### <a name="powershell"></a>PowerShell
Ändra dwu: er eller cDWUs genom att använda [Set-AzureRmSqlDatabase] [Set-AzureRmSqlDatabase] PowerShell-cmdleten. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW som finns på servern minserver-databasen.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
Med T-SQL kan du visa de aktuella inställningarna för DWU eller cDWU, ändra inställningarna och kontrollera förloppet. 

Ändra dwu: er eller cDWUs:

1. Ansluta till master-databasen som är associerade med din logiska SQL Database-server.
2. Använd den [ALTER DATABASE] [ ALTER DATABASE] TSQL-instruktion. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW-databasen. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API:er

Om du vill ändra de dwu: er använder [skapa eller uppdatera databasen] [skapa eller uppdatera databasen] REST-API. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW som finns på servern minserver-databasen. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


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
Se följande artiklar för att hjälpa dig förstå några ytterligare KPI-begrepp:

* [Hantering av arbetsbelastning och samtidighet][Workload and concurrency management]
* [Översikt över tabellen design][Table design overview]
* [tabell-distribution][Table distribution]
* [Tabell indexering][Table indexing]
* [Tabellpartitionering][Table partitioning]
* [Tabellstatistik][Table statistics]
* [Bästa praxis][Best practices]

<!--Image reference-->

<!--Article references-->

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./resource-classes-for-workload-management.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]:../role-based-access-control/built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
