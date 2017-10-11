---
title: Lagrade procedurer i SQL Data Warehouse | Microsoft Docs
description: "Tips för att använda lagrade procedurer i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b238789-6efe-4820-bf77-5a5da2afa0e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: e42d80f0ca35f3fbb67389c66d072bc40d8a8d2c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="stored-procedures-in-sql-data-warehouse"></a>Lagrade procedurer i SQL Data Warehouse
SQL Data Warehouse stöder många Transact-SQL-funktioner som finns i SQL Server. Det finns viktigare skala ut specifika funktioner som vi ska använda för att maximera prestandan för din lösning.

Om du vill behålla är skalning och prestanda för SQL Data Warehouse det dock också vissa egenskaper och funktioner som har beteendebaserade skillnader och andra som inte stöds.

Den här artikeln förklarar hur du implementerar lagrade procedurer i SQL Data Warehouse.

## <a name="introducing-stored-procedures"></a>Introduktion till lagrade procedurer
En lagrad procedur är ett bra sätt för att kapsla in din SQL-kod. lagra det nära dina data i datalagret. Genom att kapsla in koden i hanterbara enheter hjälper lagrade procedurer utvecklare modularize sina lösningar. underlätta större primärförpackningar i koden. Alla lagrade procedurer kan också accepterar parametrar för att göra dem ännu mer flexibel.

SQL Data Warehouse ger en förenklad och effektiv lagrade proceduren implementering. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte före kompilerad kod. Vi är vanligtvis mindre berörda med Kompileringstid i datalager. Det är mer viktigt att den lagrade procedur koden är korrekt optimerad när mot stora datamängder. Målet är att spara timmar, minuter och sekunder inte millisekunder. Därför är det mer bra att fundera över lagrade procedurer som behållare för SQL-logiken.     

När SQL Data Warehouse kör den lagrade proceduren parsas SQL-instruktioner, översatta och optimerad vid körning. Under den här processen konvertera varje instruktion i distribuerade frågor. SQL-kod som faktiskt körs mot data skiljer sig i frågan har skickats.

## <a name="nesting-stored-procedures"></a>Kapsling lagrade procedurer
När lagrade procedurer anropa andra lagrade procedurer eller köra dynamisk sql sedan inre lagrade proceduren eller kod anrop anses vara kapslade.

SQL Data Warehouse stöder maximalt 8 kapslingsnivåer. Detta är något annorlunda till SQL Server. Den kapslade i SQL Server är 32.

Det översta nivå lagrade proceduranropet är lika om du vill kapsla nivå 1

```sql
EXEC prc_nesting
```
Om den lagrade proceduren är också en annan EXEC anropa sedan ökar detta kapsla nivå 2

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Om den andra proceduren utför vissa dynamisk sql sedan ökar detta kapsla nivå 3

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Obs SQL Data Warehouse stöder för närvarande inte@NESTLEVEL. Du måste hålla reda på nest-nivå dig. Det är inte troligt träffar du begränsningen på 8 kapsla men i så fall behöver du igen att fungera och ”förenkla” den så att den passar i den här gränsen.

## <a name="insertexecute"></a>INSERT... KÖRA
SQL Data Warehouse tillåter inte att du kan använda resultatet av en lagrad procedur med en INSERT-instruktion. Det finns en annan metod som du kan använda.

Se följande artikel på [temporära tabeller] ett exempel på hur du gör detta.

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
För fler utvecklingstips, se [utvecklingsöversikt][development overview].

<!--Image references-->

<!--Article references-->
[temporära tabeller]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
