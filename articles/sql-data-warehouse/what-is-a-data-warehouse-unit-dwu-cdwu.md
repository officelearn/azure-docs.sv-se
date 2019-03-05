---
title: 'Informationslagerenheter (dwu: er, cDWUs) i Azure SQL Data Warehouse | Microsoft Docs'
description: 'Rekommendationer om hur du väljer perfekt antalet informationslagerenheter (dwu: er, cDWUs) för att optimera pris och prestanda och hur du ändrar antalet enheter.'
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 32a3ec7146e4dd43e0c5f522187856132745b539
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307097"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Informationslagerenheter (dwu: er) och beräkning Informationslagerenheter (cDWUs)
Rekommendationer om hur du väljer perfekt antalet informationslagerenheter (dwu: er, cDWUs) för att optimera pris och prestanda och hur du ändrar antalet enheter. 

## <a name="what-are-data-warehouse-units"></a>Vad är Informationslagerenheter?
Med SQL Data Warehouse processor, minne och IO paketeras i enheter för beräkning kallas Informationslagerenheter (dwu: er). En Informationslagerenhet representerar en abstrakt, normaliserade måttet beräkningsresurser och prestanda. Du ändrar antalet dwu: er som allokeras till systemet, vilket i sin tur justerar prestanda och kostnaden för ditt system genom att ändra din servicenivå. 

Om du vill betala för högre prestanda, kan du öka antalet informationslagerenheter. Minska informationslagerenheter för att betala för lägre prestanda. Kostnader för lagring och beräkning faktureras separat, så ändra informationslagerenheter inte påverkar kostnader för lagring.

Prestanda för informationslagerenheter baseras på dessa arbetsbelastningar måtten i informationslager:

- Hur snabbt kan en standard olika datafrågor skanna ett stort antal rader och sedan utföra en komplex aggregering? Den här åtgärden är i/o- och CPU-intensiva.
- Hur snabbt kan datalagret mata in data från Azure Storage-Blobbar eller Azure Data Lake? Den här åtgärden är nätverks- och processorintensiv. 
- Hur snabbt kan den [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-kommandot Kopiera en tabell? Den här åtgärden innebär att läsa data från lagring, distribuerar dessa till enhetens noder och skriva till lagring igen. Den här åtgärden är CPU, IO och belasta nätverket.

Öka dwu: er:
- Linjärt ändras prestanda för genomsökningar och aggregeringar CTAS-uttryck
- Ökar antalet och skrivfunktioner för PolyBase belastningen åtgärder
- Ökar det maximala antalet samtidiga frågor och samtidighetsfack.

## <a name="service-level-objective"></a>Servicenivåmål
Den tjänst för servicenivåmål (SNM) är den skalbarhet-inställningen som styr både vilken kostnad och prestanda för ditt informationslager. Servicenivåer för Gen2 mäts i beräkning informationslagerenheter (cDWU), till exempel DW2000c. Gen1 servicenivåer mäts i dwu: er, till exempel DW2000.
  > [!NOTE]
  > Azure SQL Data Warehouse Gen2 nyligen lagt till ytterligare skalningsfunktioner för att stödja beräkningsnivåer så lågt som 100 cDWU. Befintliga informationslager för närvarande på Gen1 som kräver nedre compute nivåerna nu kan du uppgradera till Gen2 i de regioner som är tillgängliga för utan extra kostnad.  Om din region inte stöds ännu, kan du fortfarande uppgradera till en region som stöds. Mer information finns i [uppgradera till Gen2](upgrade-to-latest-generation.md).

I T-SQL anger inställningen SERVICE_OBJECTIVE servicenivå och prestandanivå för ditt informationslager.

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

Varje prestandanivå använder en något annorlunda måttenhet för sina informationslagerenheter. Den här skillnaden syns på fakturan som enheten skala direkt översätts till fakturering.

- Gen1 informationslager mäts i Informationslagerenheter (dwu: er).
- Gen2 data informationslager mäts i compute-Informationslagerenheter (cDWUs). 

Både dwu: er och cDWUs stöder skalningsberäkning upp eller ned, och pausa när du inte behöver använda datalagret. Dessa åtgärder är alla på begäran. Gen2 använder en lokal diskbaserad cacheminne på beräkningsnoderna för att förbättra prestanda. När du skalar eller pausa systemet cachen betraktas som inaktuella och därför en viss cache värmer krävs innan optimala prestanda uppnås.  

När du ökar informationslagerenheter ökar linjärt datorresurser. Gen2 ger bästa frågeprestanda och högsta skala. Dessa system gör de flesta användningen av cachen.

### <a name="capacity-limits"></a>Kapacitetsbegränsningar
Varje SQLServer (t.ex, myserver.database.windows.net) har en [Database Transaction Unit (DTU)](../sql-database/sql-database-what-is-a-dtu.md) kvot som gör att ett visst antal informationslagerenheter. Mer information finns i den [arbetsbelastning management kapacitetsbegränsningar](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Hur många informationslagerenheter behöver jag?
Perfekt antalet informationslagerenheter beror mycket på din arbetsbelastning och mängden data som du har läst in i systemet.

Steg för att hitta den bästa DWU för din arbetsbelastning:

1. Börja genom att välja en mindre DWU. 
2. Övervaka programprestanda när du testar Datainläsningen i systemet, bakgrund av antalet dwu: er som valts jämfört med den prestanda du se.
3. Identifiera eventuella ytterligare krav för periodiska perioder med hög aktivitet. Om arbetsbelastningen visar betydande toppar och fördjupningar försedda i aktiviteten och det finns en bra anledning att skala ofta.

SQL Data Warehouse är en skalbar system som kan utnyttja stora mängder beräknings- och fråga ansenliga mängder data. Om du vill visa dess SANT funktioner för skalning, särskilt i större dwu: er, rekommenderar vi skala datauppsättningen när du skalar om du vill kontrollera att du har tillräckligt med data för att mata in processorerna. För att skala testa bör du använda minst 1 TB.

> [!NOTE]
>
> Prestanda för frågor endast ökar med mer parallellisering om arbetet kan delas upp mellan compute-noder. Om du upptäcker att skala inte ändrar dina prestanda, kan du behöva justera din tabelldesign och/eller dina frågor. Riktlinjer om frågejustering, se [hantera användarfrågor](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Behörigheter

Ändra informationslagerenheter kräver behörigheterna som beskrivs i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

Inbyggda roller för Azure-resurser, till exempel SQL DB-deltagare och SQL Server-deltagare kan ändra DWU-inställningar. 

## <a name="view-current-dwu-settings"></a>Visa aktuella DWU-inställningar

Visa aktuella DWU-inställningen:

1. Öppna SQL Server Object Explorer i Visual Studio.
2. Ansluta till master-databasen som är associerade med den logiska SQL Database-servern.
3. Välj den dynamiska hanteringsvyn sys.database_service_objectives. Här är ett exempel: 

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

1. Öppna den [Azure-portalen](https://portal.azure.com), öppna din databas och på **skala**.

2. Under **skala**, flytta skjutreglaget till vänster eller höger för att ändra DWU-inställningen.

3. Klicka på **Spara**. Ett bekräftelsemeddelande visas. Klicka på **Ja** för att bekräfta eller **Nej** för att avbryta.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan ändra dwu: er eller cDWUs med den [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell-cmdlet. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW som finns på servern minserver-databasen.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Mer information finns i [PowerShell-cmdletar för SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
Med T-SQL kan du visa aktuella DWU eller cDWU inställningarna, ändra inställningarna och kontrollera förloppet. 

Ändra dwu: er eller cDWUs:

1. Ansluta till master-databasen som är associerade med din logiska SQL Database-server.
2. Använd den [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL-instruktion. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW-databasen. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API:er

Du kan ändra dwu-enheterna med den [skapa eller uppdatera databasen](/rest/api/sql/databases/createorupdate) REST API. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW som finns på servern minserver-databasen. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Fler REST API-exempel finns i [REST API: er för SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Kontrollera status för DWU-ändringar

DWU ändringar kan ta flera minuter att slutföra. Överväg att implementera logik för att se till att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd om du skalar automatiskt. 

Kontrollerar databasstatus för via olika slutpunkter kan du implementera korrekt automation. Portalen meddelar vid slutförandet av en åtgärd och det aktuella tillståndet för databaser, men tillåter inte för programmässig kontroll av tillstånd. 

Du kan inte kontrollera databasens status för skalbar åtgärder med Azure-portalen.

Så här kontrollerar status för DWU-ändringar:

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

Den här DMV returnerar information om hantering av olika åtgärder på SQL Data Warehouse, till exempel åtgärden och åtgärdens, vilket antingen är status är IN_PROGRESS eller COMPLETED.

## <a name="the-scaling-workflow"></a>Skalning arbetsflödet

När du initierar en skalningsåtgärd stoppar systemet först alla öppna sessioner, återställa alla öppna transaktioner för att säkerställa ett konsekvent tillstånd. För skalning inträffar skalning bara när den här transaktionella återställning har slutförts.  

- För en upp-system etablerar de ytterligare compute och sedan återansluts sedan till storage-skiktet. 
- För en skala ned åtgärd, den onödiga noder frånkoppla från lagringen eller återansluta till de återstående noderna.

## <a name="next-steps"></a>Nästa steg
Läs mer om att hantera prestanda i [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md) och [minne och samtidighet gränser](memory-and-concurrency-limits.md).



