---
title: Med lagrade procedurer i Azure SQL Data Warehouse | Microsoft Docs
description: Tips för att implementera lagrade procedurer i Azure SQL Data Warehouse för utveckling av lösningar.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/02/2019
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 8a53a63b7425935e117d7af951717999bc9340b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439728"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Med lagrade procedurer i SQL Data Warehouse
Tips för att implementera lagrade procedurer i Azure SQL Data Warehouse för utveckling av lösningar.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

SQL Data Warehouse stöder många av T-SQL-funktioner som används i SQL Server. Det finns ännu viktigare är skalbar specifika funktioner som du kan använda för att maximera prestanda i din lösning.

Men om du vill behålla finns den skala och prestanda i SQL Data Warehouse det också vissa egenskaper och funktioner som har beteendeanalys skillnader och andra som inte stöds.


## <a name="introducing-stored-procedures"></a>Introduktion till lagrade procedurer
Lagrade procedurer är ett bra sätt för att kapsla in din SQL-kod. lagra det nära dina data i datalagret. Lagrade procedurer som hjälper utvecklare att modularize sina lösningar genom att kapsla in koden i hanterbara enheter; underlätta större återanvändning av kod. Alla lagrade procedurer kan också acceptera parametrar för att göra dem ännu mer flexibel.

SQL Data Warehouse ger en förenklad och smidig lagrad procedur-implementering. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte är förkompilerad kod. I informationslager är kompileringstid liten jämfört med den tid det tar för att köra frågor mot stora datamängder. Det är viktigare att se till att den lagrade procedur koden korrekt är optimerad för stora frågor. Målet är att spara timmar, minuter och sekunder, inte millisekunder. Det är därför mer bra att tänka på lagrade procedurer som behållare för SQL-logik.     

När SQL Data Warehouse kör den lagrade proceduren, är SQL-uttryck parsas, översättas och optimerad vid körning. Under den här processen konverteras varje uttryck i distribuerade frågor. SQL-kod som körs mot data skiljer sig från den fråga som skickats.

## <a name="nesting-stored-procedures"></a>Kapsling lagrade procedurer
När lagrade procedurer anropa andra lagrade procedurer, eller kör dynamic SQL, sedan inre lagrade proceduren eller kod anrop anses vara kapslade.

SQL Data Warehouse stöder högst åtta kapslingsnivåer. Det här är något annorlunda till SQL Server. Den kapslade nivån i SQL Server är 32.

Det översta lagrade proceduranropet är om du vill kapsla nivå 1.

```sql
EXEC prc_nesting
```
Om den lagrade proceduren gör även en annan EXEC anropa, ökar den kapslade nivån till två.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Om den andra proceduren körs sedan vissa dynamic SQL, ökar den kapslade nivån till tre.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Observera att SQL Data Warehouse inte stöder för närvarande [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Du behöver spåra kapslade-nivå. Det är inte troligt att överskrida den åtta kapslade nivå, men om du gör det, måste du omarbeta koden så att de passar kapslingsnivåer i den här gränsen.

## <a name="insertexecute"></a>INFOGA... KÖRA
SQL Data Warehouse tillåter inte att du kan använda resultatuppsättningen för en lagrad procedur med en INSERT-instruktion. Det finns dock en annan metod som du kan använda. Ett exempel finns i artikeln på [temporära tabeller](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Begränsningar
Det finns vissa aspekter av Transact-SQL-lagrade procedurer som inte är implementerade i SQL Data Warehouse.

De är:

* tillfälliga lagrade procedurer
* numrerade lagrade procedurer
* Utökade lagrade procedurer
* CLR som lagrade procedurer
* Krypteringsalternativet
* replikeringsalternativ
* tabellvärdeparametrar
* skrivskyddad parametrar
* standardparametrar
* körningen kontexter
* returnera instruktionen

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips, se [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

