---
title: Använda lagrade procedurer
description: Tips för att implementera lagrade procedurer i Azure SQL Data Warehouse för att utveckla lösningar.
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
ms.openlocfilehash: 83c3187c580bda33df8780a0e36f0fb9f2a4f484
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351581"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Använda lagrade procedurer i SQL Data Warehouse
Tips för att implementera lagrade procedurer i Azure SQL Data Warehouse för att utveckla lösningar.

## <a name="what-to-expect"></a>Vad du kan förvänta dig

SQL Data Warehouse stöder många av de T-SQL-funktioner som används i SQL Server. Ännu viktigare är att det finns utskalningsspecifika funktioner som du kan använda för att maximera prestanda för din lösning.

Men för att upprätthålla skalan och prestandan för SQL Data Warehouse finns det också vissa funktioner som har beteendeskillnader och andra som inte stöds.


## <a name="introducing-stored-procedures"></a>Införande av lagrade procedurer
Lagrade procedurer är ett bra sätt att kapsla in din SQL-kod. lagra den nära dina data i informationslagret. Lagrade procedurer hjälper utvecklare att modularisera sina lösningar genom att kapsla in koden i hanterbara enheter. underlätta större återanvändning av kod. Varje lagrad procedur kan också acceptera parametrar för att göra dem ännu mer flexibla.

SQL Data Warehouse tillhandahåller en förenklad och strömlinjeformad implementering av lagrade procedurer. Den största skillnaden jämfört med SQL Server är att den lagrade proceduren inte är förkompilerad kod. I informationslager är kompileringstiden liten i jämförelse med den tid det tar att köra frågor mot stora datavolymer. Det är viktigare att se till att den lagrade procedurkoden är korrekt optimerad för stora frågor. Målet är att spara timmar, minuter och sekunder, inte millisekunder. Det är därför mer användbart att tänka på lagrade procedurer som behållare för SQL-logik.     

När SQL Data Warehouse kör din lagrade procedur tolkas SQL-uttrycken, översätts och optimeras vid körning. Under den här processen konverteras varje sats till distribuerade frågor. SQL-koden som körs mot data skiljer sig från den fråga som skickas.

## <a name="nesting-stored-procedures"></a>Kapsla lagrade procedurer
När lagrade procedurer anropar andra lagrade procedurer, eller kör dynamisk SQL, sägs den inre lagrade proceduren eller kodanropet vara kapslad.

SQL Data Warehouse stöder högst åtta kapslingsnivåer. Detta skiljer sig något från SQL Server. Kapslingsnivån i SQL Server är 32.

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

SQL Data Warehouse stöder för närvarande inte [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Du måste spåra boet nivå. Det är osannolikt att du överskrider gränsen för åtta bonivå, men om du gör det måste du omarbeta koden så att den passar kapslingsnivåerna inom den här gränsen.

## <a name="insertexecute"></a>Infoga.. Utföra
SQL Data Warehouse tillåter inte att du använder resultatuppsättningen för en lagrad procedur med en INSERT-sats. Det finns dock en alternativ metod som du kan använda. En exempelvis se artikeln på [temporära tabeller](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Begränsningar
Det finns vissa aspekter av Transact-SQL-lagrade procedurer som inte implementeras i SQL Data Warehouse.

De är:

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

