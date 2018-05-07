---
title: Med lagrade procedurer i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att använda lagrade procedurer i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 0ad8a599065a44469a3151813972b3d2561782c6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Med hjälp av lagrade procedurer i SQL Data Warehouse
Tips för att använda lagrade procedurer i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

SQL Data Warehouse stöder många av T-SQL-funktioner som används i SQL Server. Det finns ännu bättre, skalbar specifika funktioner som du kan använda för att maximera prestandan för din lösning.

Om du vill behålla är skalning och prestanda för SQL Data Warehouse det dock också vissa egenskaper och funktioner som har beteendebaserade skillnader och andra som inte stöds.


## <a name="introducing-stored-procedures"></a>Introduktion till lagrade procedurer
En lagrad procedur är ett bra sätt för att kapsla in din SQL-kod. lagra det nära dina data i datalagret. Lagrade procedurer hjälper utvecklare modularize sina lösningar genom att kapsla in koden i hanterbara enheter; underlätta större återanvändning av kod. Alla lagrade procedurer kan också accepterar parametrar för att göra dem ännu mer flexibel.

SQL Data Warehouse ger en förenklad och effektiv lagrade proceduren implementering. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte före kompilerad kod. I datalager är kompileringstid liten jämfört med den tid det tar för att köra frågor mot stora datamängder. Det är mer viktigt att kontrollera att den lagrade procedur koden korrekt är optimerad för stora frågor. Målet är att spara timmar, minuter och sekunder, inte millisekunder. Därför är det mer bra att fundera över lagrade procedurer som behållare för SQL-logiken.     

När SQL Data Warehouse kör den lagrade proceduren, är SQL-uttryck parsas översättas och optimerad vid körning. Under den här processen konvertera varje uttryck i distribuerade frågor. SQL-kod som körs mot data skiljer sig frågan har skickats.

## <a name="nesting-stored-procedures"></a>Kapsling lagrade procedurer
När lagrade procedurer anropa andra lagrade procedurer, eller kör dynamisk SQL, sedan inre lagrade proceduren eller kod anrop anses vara kapslade.

SQL Data Warehouse stöder högst åtta kapslingsnivåer. Detta är något annorlunda till SQL Server. Den kapslade i SQL Server är 32.

Det översta lagrade proceduranropet är lika om du vill kapsla nivå 1.

```sql
EXEC prc_nesting
```
Om den lagrade proceduren gör också en annan EXEC anropa, ökar kapsla nivån till två.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Om den andra proceduren körs sedan vissa dynamisk SQL, ökar kapsla nivån till tre.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Observera att SQL Data Warehouse inte stöder för närvarande [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Du behöver spåra nest-nivå. Det är inte troligt att överskrida gränsen på åtta nest-nivå, men om du gör det, måste du omarbeta din kod så att den passar kapslingsnivåer i den här gränsen.

## <a name="insertexecute"></a>INSERT... KÖRA
SQL Data Warehouse tillåter inte att du kan använda resultatet av en lagrad procedur med en INSERT-instruktion. Det finns en annan metod som du kan använda. Ett exempel finns i artikeln på [temporära tabeller](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Begränsningar
Det finns vissa aspekter av Transact-SQL-lagrade procedurer som inte har implementerats i SQL Data Warehouse.

De är:

* tillfälliga lagrade procedurer
* numrerade lagrade procedurer
* utökade lagrade procedurer
* CLR lagrade procedurer
* Krypteringsalternativ
* replikeringsalternativet
* tabellvärdeparametrar
* skrivskyddad parametrar
* standardparametrar
* körningen kontexter
* returnera instruktionen

## <a name="next-steps"></a>Nästa steg
För fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

