---
title: Använda lagrade procedurer
description: Tips för att implementera lagrade procedurer i Azure SQL Data Warehouse för att utveckla lösningar.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e6e1144043cbbbc8124785351e1e56a776b84527
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692805"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Använda lagrade procedurer i SQL Data Warehouse
Tips för att implementera lagrade procedurer i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

SQL Data Warehouse stöder många av de T-SQL-funktioner som används i SQL Server. Det är viktigt att det finns skalbara funktioner som du kan använda för att maximera prestandan för din lösning.

Men för att bevara skalan och prestandan hos SQL Data Warehouse finns det också vissa funktioner och funktioner som har beteende skillnader och andra som inte stöds.


## <a name="introducing-stored-procedures"></a>Introduktion till lagrade procedurer
Lagrade procedurer är ett bra sätt att kapsla in din SQL-kod. lagra den nära dina data i informations lagret. Lagrade procedurer hjälper utvecklare att modularize sina lösningar genom att kapsla in koden i hanterbara enheter. underlättar bättre åter användning av kod. Varje lagrad procedur kan också acceptera parametrar för att göra dem ännu mer flexibla.

SQL Data Warehouse tillhandahåller en förenklad och strömlinjeformad implementering av lagrade procedurer. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte är förkompilerad kod. I informations lager är kompileringen av tiden liten jämfört med den tid det tar att köra frågor mot stora data volymer. Det är viktigt att se till att den lagrade procedur koden är korrekt optimerad för stora frågor. Målet är att spara timmar, minuter och sekunder, inte millisekunder. Det är därför mer användbart att tänka på lagrade procedurer som behållare för SQL-logik.     

När SQL Data Warehouse kör den lagrade proceduren, parsas SQL-uttryck, översätts och optimeras vid körning. Under den här processen konverteras varje instruktion till distribuerade frågor. SQL-koden som körs mot data skiljer sig från den skickade frågan.

## <a name="nesting-stored-procedures"></a>Kapslade lagrade procedurer
När lagrade procedurer anropar andra lagrade procedurer eller kör dynamisk SQL, kallas den inre lagrade proceduren eller kod anropet som kapslas.

SQL Data Warehouse stöder högst åtta kapslings nivåer. Detta skiljer sig något från SQL Server. Kapslings nivån i SQL Server är 32.

Det översta lagrade procedur anropet motsvarar kapslad nivå 1.

```sql
EXEC prc_nesting
```
Om den lagrade proceduren också gör ett annat EXEC-anrop, ökar kapslings nivån till två.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Om den andra proceduren sedan kör en del dynamisk SQL, ökar kapslings nivån till tre.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Obs! SQL Data Warehouse stöder för närvarande inte [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Du måste spåra kapslings nivån. Det är inte troligt att du överskrider gränsen på åtta kapslings nivåer, men om du gör det måste du återanvända koden för att passa de kapslade nivåerna inom den här gränsen.

## <a name="insertexecute"></a>Infoga.. KÖRA
SQL Data Warehouse tillåter inte att du använder resultat uppsättningen för en lagrad procedur med en INSERT-instruktion. Det finns dock en alternativ metod som du kan använda. Ett exempel finns i artikeln om [temporära tabeller](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Begränsningar
Det finns vissa aspekter av lagrade Transact-SQL-procedurer som inte har implementerats i SQL Data Warehouse.

De är:

* temporärt lagrade procedurer
* numrerade lagrade procedurer
* utökade lagrade procedurer
* Lagrade CLR-procedurer
* krypterings alternativ
* replikeringsalternativ
* tabell värdes parametrar
* skrivskyddade parametrar
* standard parametrar
* körnings kontexter
* Return-instruktion

## <a name="next-steps"></a>Nästa steg
Mer utvecklings tips finns i [utvecklings översikt](sql-data-warehouse-overview-develop.md).

