---
title: Använda lagrade procedurer
description: Tips för att utveckla lösningar genom att implementera lagrade procedurer i Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a8350f8027a78ae5692e12661f2e0d2013ab4c46
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618956"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Använda lagrade procedurer i Synapse SQL-pool
Den här artikeln innehåller tips för hur du utvecklar SQL-poollösningar genom att implementera lagrade procedurer.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

SQL-poolen stöder många av de T-SQL-funktioner som används i SQL Server. Ännu viktigare är att det finns utskalningsspecifika funktioner som du kan använda för att maximera prestanda för din lösning.

För att hjälpa dig att behålla skalan och prestandan för SQL-poolen finns det också ytterligare funktioner som har beteendemässiga skillnader.


## <a name="introducing-stored-procedures"></a>Införande av lagrade procedurer
Lagrade procedurer är ett bra sätt att kapsla in din SQL-kod, som lagras nära dina SQL-pooldata. Lagrade procedurer hjälper också utvecklare att modularisera sina lösningar genom att kapsla in koden i hanterbara enheter, vilket underlättar större återanvändning av kod. Varje lagrad procedur kan också acceptera parametrar för att göra dem ännu mer flexibla.

SQL-poolen ger en förenklad och strömlinjeformad implementering av lagrade procedurer. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte är förkompilerad kod. 

För informationslager är kompileringstiden i allmänhet liten jämfört med den tid det tar att köra frågor mot stora datavolymer. Det är viktigare att se till att den lagrade procedurkoden är korrekt optimerad för stora frågor. 

> [!TIP]
> Målet är att spara timmar, minuter och sekunder, inte millisekunder. Så det är bra att tänka på lagrade procedurer som behållare för SQL-logik.     

När SQL-poolen kör din lagrade procedur tolkas SQL-uttrycken, översätts och optimeras vid körning. Under den här processen konverteras varje sats till distribuerade frågor. SQL-koden som körs mot data skiljer sig från den fråga som skickas.

## <a name="nesting-stored-procedures"></a>Kapsla lagrade procedurer
När lagrade procedurer anropar andra lagrade procedurer, eller kör dynamisk SQL, sägs den inre lagrade proceduren eller kodanropet vara kapslad.

SQL-poolen stöder högst åtta kapslingsnivåer. Kapslingsnivån i SQL Server är däremot 32.

Det lagrade proceduranropet på den översta nivån motsvarar kapsla nivå 1.

```sql
EXEC prc_nesting
```
Om den lagrade proceduren också gör ett annat EXEC-anrop ökar bonivån till två.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Om den andra proceduren sedan kör någon dynamisk SQL ökar kapslingsnivån till tre.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

SQL-poolen stöder för närvarande inte [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Därför måste du spåra boet nivå. Det är osannolikt att du överskrider gränsen för åtta bonivå. Men om du gör det måste du omarbeta koden så att den passar kapslingsnivåerna inom den här gränsen.

## <a name="insertexecute"></a>Infoga.. Utföra
SQL-poolen tillåter inte att du använder resultatuppsättningen för en lagrad procedur med en INSERT-sats. Det finns dock ett alternativt tillvägagångssätt som du kan använda. En exempelvis se artikeln på [temporära tabeller](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Begränsningar
Det finns vissa aspekter av Transact-SQL-lagrade procedurer som inte implementeras i SQL-poolen, som är följande:

* tillfälliga lagrade förfaranden
* numrerade lagrade procedurer
* utökade lagrade procedurer
* CLR-lagrade procedurer
* krypteringsalternativ
* replikeringsalternativ
* tabellvärderade parametrar
* Skrivskyddade parametrar
* standardparametrar
* exekveringskontexter
* returutdrag

## <a name="next-steps"></a>Nästa steg
Fler utvecklingstips finns i [utvecklingsöversikt](sql-data-warehouse-overview-develop.md).

