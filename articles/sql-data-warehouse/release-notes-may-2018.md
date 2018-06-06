---
title: Azure SQL Data Warehouse viktig information kan 2018 | Microsoft Docs
description: Viktig information om Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 843621a8f6e08b2b51d4b7abd05d0ae6c3393fe1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726041"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Vad är nytt i Azure SQL Data Warehouse (maj 2018)?
Azure SQL Data Warehouse får ständigt förbättringar. Den här artikeln beskrivs nya funktioner och ändringar som har införts i maj 2018. 

## <a name="gen-2-instances"></a>Generation 2 instanser
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL Data Warehouse Compute optimerade Gen2 nivå anger nya prestanda standarder för datalagring i molnet. Kunder nu att få upp till fem gånger bättre frågeprestanda, fyra gånger mer samtidighet och fem gånger högre beräkningskraft jämfört med den nuvarande generationen. Den kan nu hantera 128 samtidiga frågor från ett enda kluster, den högsta för alla data datalagring Molntjänsten.

Finns det [Turbocharge moln analytics med Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) blogg meddelande från Rohan Kumar, företagets VD Azure Data.

## <a name="features"></a>Funktioner

### <a name="auto-statistics"></a>Automatisk statistik
Statistik är viktiga för att optimera frågeplan i moderl kostnad-baserade optimerare som motorn i SQL Data Warehouse. När alla frågor som är kända i förväg, är att fastställa statistik objekt måste du skapa en hastigheterna uppgift. Men när systemet inför ad hoc- och slumpmässiga frågor som är vanliga för arbetsbelastningar för informationslager, systemadministratörer kan behöva kämpa för att förutsäga statistik måste du skapa ett leder till potentiellt något sämre frågeplaner för körning och fråga längre svarstider. Ett sätt att minimera det här problemet är att skapa statistik objekt på alla tabellkolumner i förväg. Men levereras den här processen med en påföljder som statistik objekten måste hållas under tabellen processen för inläsning, orsakar längre inläsning gånger.

SQL Data Warehouse stöder nu automatisk generering av statistik objekt ger större flexibilitet, produktivitet och enkelt att använda för administratörer och utvecklare, samtidigt som du säkerställer att systemet fortsätter att erbjuda kvalitet körning planer och bäst svarstider.

Om du vill aktivera eller inaktivera automatisk statistik skapas i SQL Data Warehouse, kör du följande sats:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Som bästa praxis och vägledning, rekommenderar vi att ställa `AUTO_CREATE_STATISTICS` att `ON`.

> [!NOTE]
> Automatisk statistik skapas *aktiverad som standard* för alla nya datalager.
>  

Finns det [ALTER DATABASE SET-alternativ](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) artikeln för ytterligare information.

### <a name="rejected-row-support"></a>Avvisade raden stöd
Kunder som använder ofta [PolyBase (externa tabeller) för att läsa in data](design-elt-data-loading.md) parallell uppbyggnad inläsning av data till SQL Data Warehouse på grund av hög prestanda. PolyBase är standardmodell för inläsning vid inläsning av data via [Azure Data Factory](http://azure.com/adf) samt. 

SQL Data Warehouse lägger till möjligheten att definiera en nekade raden plats via den `REJECTED_ROW_LOCATION` parameter med den [Skapa extern tabell](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) instruktionen. Efter körning av en [Skapa tabell AS Välj (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) från extern tabell några rader som inte gick att läsa in kommer att lagras i en fil nära källan för ytterligare undersökning. 

Finns det [laddas säkert sätt med SQL Data Warehouse PolyBase avvisade raden plats](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) bloggar för mer information om beteendet avvisad rad.

I följande exempel visas den nya syntaxen för att ange Avvisade rader.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

### <a name="alter-view"></a>ÄNDRA VYN
[Ändra VYN](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) tillåter en användare att ändra en tidigare skapad vy utan att behöva ta bort/skapa vyn och tillämpa behörigheter. 

I följande exempel ändrar en tidigare skapad vy.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

### <a name="concatws"></a>CONCAT_WS
[CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) funktionen returnerar en sträng som uppstår till följd av en sammanslagning av två eller flera värden på ett sätt för slutpunkt till slutpunkt. Den separerar de sammanfogade värdena med avgränsare som anges i det första argumentet. Den `CONCAT_WS` funktionen är användbar för att generera utdata för Comma-Separated värde (CSV).

I följande exempel visas att en uppsättning med int-värden med kommatecken.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Instruktionen returnerar följande resultat:
```
result
---------
1,2,3
```
I följande exempel visas att en uppsättning med blandade data typen värden med kommatecken.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Instruktionen returnerar följande resultat:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```
### <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
Den [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) system lagrade procedur returnerar information om datatyper som stöds av den aktuella miljön. Den används ofta av verktyg som ansluter via ODBC-anslutningar för undersökning för typ av data.

I följande exempel hämtar information för alla datatyper som stöds av SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="behavior-changes"></a>Funktionsändringar
### <a name="select-into-with-order-by"></a>SELECT INTO med ORDER BY
SQL Data Warehouse kommer nu att blockera `SELECT INTO` frågor som innehåller en `ORDER BY` satsen. Tidigare, fungerar den här åtgärden genom att först beställa data i minnet och sedan lägga till i måltabellen ordna data så att de matchar formen tabell.

#### <a name="previous-behavior"></a>Tidigare beteende
Följande instruktion fungerar med ytterligare bearbetning omkostnader.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

#### <a name="current-behavior"></a>Aktuella beteende
Följande instruktion genereras ett fel som anger den `ORDER BY` -satsen stöds inte i en `SELECT INTO` instruktion.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Instruktionen fel returnerades:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

### <a name="set-parseonly-on-query-status"></a>Ange PARSEONLY på fråga status
Med den `SET PARSEONLY ON` syntax gör att användaren kan SQL Data Warehouse-motor granska syntaxen för varje T-SQL-uttryck och returnerar eventuella felmeddelanden utan att kompilera eller instruktionen körs. Tidigare i den [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) system Visa status för de här uttrycken visas fortfarande i den `Running` tillstånd. Den `sys.dm_pdw_exec_requests` Visa nu returnerar status som `Complete`.