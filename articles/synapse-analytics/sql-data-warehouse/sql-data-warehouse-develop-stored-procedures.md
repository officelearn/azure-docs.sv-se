---
title: Användning av lagrade procedurer
description: Tips för att utveckla lösningar genom att implementera lagrade procedurer i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 87c7eaa57f9da87bd83f89953afc09632d42b1f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213405"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Använda lagrade procedurer i Synapse SQL-pool

Den här artikeln innehåller tips för att utveckla lösningar för SQL-pooler genom att implementera lagrade procedurer.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

SQL-poolen stöder många av de T-SQL-funktioner som används i SQL Server. Det är viktigt att det finns skalbara funktioner som du kan använda för att maximera prestandan för din lösning.

För att hjälpa dig att underhålla skalning och prestanda i SQL-poolen finns det också ytterligare funktioner som har beteende skillnader.

## <a name="introducing-stored-procedures"></a>Introduktion till lagrade procedurer

Lagrade procedurer är ett bra sätt att kapsla in din SQL-kod, som lagras nära SQL-poolens data. Lagrade procedurer hjälper också utvecklare att modularize sina lösningar genom att kapsla in koden i hanterbara enheter, vilket underlättar bättre kod åter användning. Varje lagrad procedur kan också acceptera parametrar för att göra dem ännu mer flexibla.

SQL-poolen ger en förenklad och strömlinjeformad implementering av lagrade procedurer. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte är förkompilerad kod.

I allmänhet är kompileringen för informations lager liten jämfört med den tid det tar att köra frågor mot stora data volymer. Det är viktigt att se till att den lagrade procedur koden är korrekt optimerad för stora frågor.

> [!TIP]
> Målet är att spara timmar, minuter och sekunder, inte millisekunder. Det är därför bra att tänka på lagrade procedurer som behållare för SQL-logik.

När SQL-poolen kör den lagrade proceduren, parsas SQL-uttryck, översätts och optimeras vid körning. Under den här processen konverteras varje instruktion till distribuerade frågor. SQL-koden som körs mot data skiljer sig från den skickade frågan.

## <a name="nesting-stored-procedures"></a>Kapslade lagrade procedurer

När lagrade procedurer anropar andra lagrade procedurer eller kör dynamisk SQL, kallas den inre lagrade proceduren eller kod anropet som kapslas.

SQL-poolen stöder högst åtta kapslings nivåer. Kapslings nivån i SQL Server är däremot 32.

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

SQL-poolen stöder för närvarande inte [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Därför måste du spåra kapslings nivån. Det är osannolikt att du överskrider gränsen på åtta kapslings nivåer. Men om du gör det måste du arbeta om koden för att passa de kapslade nivåerna i den här gränsen.

## <a name="insertexecute"></a>INSERT..EXESÖTA

SQL-poolen tillåter inte att du använder resultat uppsättningen för en lagrad procedur med en INSERT-instruktion. Det finns dock en annan metod som du kan använda. Ett exempel finns i artikeln om [temporära tabeller](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Begränsningar

Det finns vissa aspekter av lagrade Transact-SQL-procedurer som inte implementeras i SQL-poolen, som är följande:

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
