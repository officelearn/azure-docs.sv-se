---
title: Azure SQL Data Warehouse viktig maj 2018 | Microsoft Docs
description: Viktig information för Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 45a391f45d11d968818bafc97a705411a133b273
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57430857"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Vad är nytt i Azure SQL Data Warehouse? Maj 2018 
Azure SQL Data Warehouse tar emot förbättringar kontinuerligt. Den här artikeln beskriver nya funktioner och ändringar som har införts i maj 2018. 

## <a name="gen-2-instances"></a>Gen 2 instanser
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Azure SQL Data Warehouse Compute Optimized Gen2 nivån anger nya standarder för prestanda för datalager i molnet. Kunder nu få upp till fem gånger bättre frågeprestanda, fyra gånger mer samtidighet och fem gånger högre beräkningskraft jämfört med den aktuella generationen. Den kan nu hantera 128 samtidiga frågor från ett enda kluster, det högsta värdet av alla olika tjänst i molnet.

Se den [Turbocharge molnbaserade analyser med Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) bloggmeddelandet från Rohan Kumar, Corporate Vice President, Azure Data.

## <a name="auto-statistics"></a>Automatisk statistik
Statistik är viktiga för att optimera en frågeplan i moderna kostnadsbaserad optimerare, till exempel motorn i SQL Data Warehouse. När alla frågor är kända i förväg, är avgör vad statistik objekt måste du skapa en uppgift som kan uppnås. När systemet är inför ad hoc- och slumpmässiga frågor som är vanliga för arbetsbelastningar för informationslager, systemadministratörer kan behöva kämpa för att förutsäga vilka statistik måste dock skapas ledande att potentiellt icke-optimal frågeplaner för körning och längre frågesvarstiderna. Ett sätt att minimera det här problemet är att skapa statistik objekt på alla tabellkolumner i förväg. Men levereras den här processen med en särskilda avgifter eftersom statistik objekt måste finnas kvar under tabellen processen för inläsning och ge längre inläsning av gånger.

SQL Data Warehouse stöder nu automatisk generering av statistik objekt, vilket ger större flexibilitet, produktivitet och användarvänlighet för administratörer och utvecklare, samtidigt som systemet fortsätter att erbjuda körningsplaner för kvalitet och bästa svarstider.

Om du vill aktivera eller inaktivera automatisk statistik skapas i SQL Data Warehouse, kör du följande uttryck:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Som bästa praxis och vägledning, rekommenderar vi att ställa `AUTO_CREATE_STATISTICS` alternativet att `ON`.

> [!NOTE]
> Skapa en automatisk statistik är *aktiverad som standard* för alla nya informationslager.
>  

Se den [ALTER DATABASE SET-alternativ](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) för ytterligare information.

## <a name="rejected-row-support"></a>Avvisade raden Support
Kunder använder ofta [PolyBase (externa tabeller) för att läsa in data](design-elt-data-loading.md) parallell natur inläsning av data till SQL Data Warehouse på grund av hög prestanda. PolyBase är standard läser in modellen vid inläsning av data via [Azure Data Factory](http://azure.com/adf) samt. 

SQL Data Warehouse lägger till möjligheten att definiera en avvisade raden plats via den `REJECTED_ROW_LOCATION` parametern med det [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) instruktionen. Efter körning av en [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) från den externa tabellen alla rader som inte gick att läsa in kommer att lagras i en fil nära källan för ytterligare utredning. 

Se den [läsa in tryggt med SQL Data Warehouse PolyBase avvisade raden platsen](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) blogg för mer information om beteendet avvisad rad.

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

## <a name="alter-view"></a>ÄNDRA VYN
[Ändra vy](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) kan användaren ändra vyn tidigare skapade utan att behöva ta bort/skapa vyn och ange behörigheter igen. 

I följande exempel ändrar en tidigare skapad vy.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
Den [CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) funktionen returnerar en sträng som följd av en sammanfogning av två eller flera värden på ett sätt för slutpunkt till slutpunkt. Användarfunktioner av sammansatta värden med avgränsaren som anges i det första argumentet. Den `CONCAT_WS` funktionen är användbar för att generera utdata Comma-Separated värde (CSV).

I följande exempel visas sammanfoga en uppsättning med int-värden med kommatecken.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Instruktionen returnerar följande resultat:
```
result
---------
1,2,3
```
I följande exempel visas sammanfoga en uppsättning blandat Datatypvärden med ett kommatecken.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Instruktionen returnerar följande resultat:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
Den [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) system lagrade proceduren returnerar information om datatyper som stöds av den aktuella miljön. Det är ofta används av verktyg som ansluter via ODBC-anslutningar för undersökning för typ av data.

I följande exempel hämtar information för alla datatyper som stöds av SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Välj INTO med ORDER BY-Funktionsändring
SQL Data Warehouse nu blockerar `SELECT INTO` frågor som innehåller en `ORDER BY` satsen. Tidigare fungerar den här åtgärden genom att första beställa data i minnet och sedan lägga till i måltabellen sortering av data för att matcha formen tabell.

### <a name="previous-behavior"></a>Beteende för tidigare
Följande instruktion fungerar med omkostnader ytterligare bearbetning.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Aktuella beteendet
Följande instruktion genereras ett fel som indikerar den `ORDER BY` -satsen stöds inte i en `SELECT INTO` instruktionen.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Instruktionen fel returnerades:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>Ange PARSEONLY på fråga status (Funktionsändring)
Med hjälp av den `SET PARSEONLY ON` syntax kan användaren ha SQL Data Warehouse-motorn granska syntaxen för varje T-SQL-instruktionen och returnerar eventuella felmeddelanden utan att kompilera eller instruktionen körs. Tidigare i den [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) systemvy, förblir statusen för dessa instruktioner i den `Running` tillstånd. Den `sys.dm_pdw_exec_requests` Visa nu returnerar status som `Complete`.

## <a name="next-steps"></a>Nästa steg
Nu när du vet lite om SQL Data Warehouse, lär du dig hur du snabbt [skapa ett SQL Data Warehouse][create a SQL Data Warehouse]. Om du inte har erfarenhet av Azure kan [Azure-ordlistan][Azure glossary] vara till hjälp om du stöter på ny terminologi. Eller så kan du se över några av de övriga SQL Data Warehouse-resurserna.  

* [Kundernas framgångsberättelser]
* [Bloggar]
* [Funktionsbegäranden]
* [Videoklipp]
* [Customer Advisory Team-bloggar]
* [Stack Overflow-forum]
* [Twitter]


[Bloggar]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-bloggar]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundernas framgångsberättelser]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsbegäranden]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoklipp]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
