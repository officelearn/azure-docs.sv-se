---
title: Använda lagrade procedurer
description: Tips för att implementera lagrade procedurer i Synapse SQL-pool (Data Warehouse) för att utveckla lösningar.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f88b994104ed8a2d80fb3b16f125f8a087cbe3c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958386"
---
# <a name="use-stored-procedures-in-sql-pool"></a>Använda lagrade procedurer i SQL-poolen

Tips för att implementera lagrade procedurer i Synapse SQL-pool (Data Warehouse) för att utveckla lösningar.

## <a name="what-to-expect"></a>Vad som ska förväntas

SQL-poolen stöder många av de T-SQL-funktioner som används i SQL Server. Det är viktigt att det finns skalbara funktioner som du kan använda för att maximera prestandan för din lösning.

Men för att upprätthålla skalning och prestanda i SQL-poolen finns det också vissa funktioner och funktioner som har beteende skillnader och andra som inte stöds.

## <a name="introducing-stored-procedures"></a>Introduktion till lagrade procedurer

Lagrade procedurer är ett bra sätt att kapsla in din SQL-kod. lagra den nära dina data i informations lagret. Lagrade procedurer hjälper utvecklare att modularize sina lösningar genom att kapsla in koden i hanterbara enheter. underlättar bättre åter användning av kod. Varje lagrad procedur kan också acceptera parametrar för att göra dem ännu mer flexibla.

SQL-poolen ger en förenklad och strömlinjeformad implementering av lagrade procedurer. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte är förkompilerad kod. I informations lager är kompileringen av tiden liten jämfört med den tid det tar att köra frågor mot stora data volymer. Det är viktigt att se till att den lagrade procedur koden är korrekt optimerad för stora frågor. Målet är att spara timmar, minuter och sekunder, inte millisekunder. Det är därför mer användbart att tänka på lagrade procedurer som behållare för SQL-logik.

När SQL-poolen kör den lagrade proceduren, parsas SQL-uttryck, översätts och optimeras vid körning. Under den här processen konverteras varje instruktion till distribuerade frågor. SQL-koden som körs mot data skiljer sig från den skickade frågan.

## <a name="nesting-stored-procedures"></a>Kapslade lagrade procedurer

När lagrade procedurer anropar andra lagrade procedurer eller kör dynamisk SQL, kallas den inre lagrade proceduren eller kod anropet som kapslas.

SQL-poolen stöder högst åtta kapslings nivåer. Detta skiljer sig något från SQL Server. Kapslings nivån i SQL Server är 32.

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

> [!NOTE]
> SQL-poolen stöder för närvarande [inte @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Du måste spåra kapslings nivån. Det är inte troligt att du överskrider gränsen på åtta kapslings nivåer, men om du gör det måste du återanvända koden för att passa de kapslade nivåerna inom den här gränsen.

## <a name="insertexecute"></a>INSERT..EXESÖTA

SQL-poolen tillåter inte att du använder resultat uppsättningen för en lagrad procedur med en INSERT-instruktion. Det finns dock en alternativ metod som du kan använda. Ett exempel finns i artikeln om [temporära tabeller](develop-tables-temporary.md).

## <a name="limitations"></a>Begränsningar

Det finns vissa aspekter av lagrade procedurer för Transact-SQL som inte har implementerats i SQL-poolen.

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

Mer utvecklings tips finns i [utvecklings översikt](develop-overview.md).
