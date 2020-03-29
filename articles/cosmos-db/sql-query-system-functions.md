---
title: Systemfunktioner i Azure Cosmos DB-frågespråk
description: Lär dig mer om inbyggda och användardefinierade SQL-systemfunktioner i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870538"
---
# <a name="system-functions-azure-cosmos-db"></a>Systemfunktioner (Azure Cosmos DB)

 Cosmos DB tillhandahåller många inbyggda SQL-funktioner. Kategorierna av inbyggda funktioner listas nedan.  
  
|Funktionsgrupp|Beskrivning|Åtgärder|  
|--------------|-----------------|-----------------| 
|[Matrisfunktioner](sql-query-array-functions.md)|Matrisfunktionerna utför en åtgärd på ett matrisindatavärde och returnerar numeriskt, booleskt eller matrisvärde. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Datum- och tidsfunktioner](sql-query-date-time-functions.md)|Med datum- och tidsfunktionerna kan du hämta aktuellt UTC-datum och tid i två formulär. en numerisk tidsstämpel vars värde är Unix-epoken i millisekunder eller som en sträng som överensstämmer med ISO 8601-formatet. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Matematiska funktioner](sql-query-mathematical-functions.md)|De matematiska funktionerna utför var och en en beräkning, vanligtvis baserat på indatavärden som anges som argument, och returnerar ett numeriskt värde. | [ABS,](sql-query-abs.md) [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [Tak,](sql-query-ceiling.md) [COS,](sql-query-cos.md) [COT,](sql-query-cot.md) [Grader,](sql-query-degrees.md) [EXP,](sql-query-exp.md) [GOLV,](sql-query-floor.md) [Log,](sql-query-log.md) [Log10,](sql-query-log10.md) [PI](sql-query-pi.md), [Makt](sql-query-power.md), [Radians](sql-query-radians.md), [RAND](sql-query-rand.md), [Runda,](sql-query-round.md) [TECKEN,](sql-query-sign.md) [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [KVADRATISK](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNK](sql-query-trunc.md) |
|[Spatiella funktioner](sql-query-spatial-functions.md)|De rumsliga funktionerna utför en åtgärd på ett rumsligt objektindatavärde och returnerar ett numeriskt eller booleskt värde. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Strängfunktioner](sql-query-string-functions.md)|Strängfunktionerna utför en åtgärd på ett strängindatavärde och returnerar ett sträng-, numeriskt eller booleskt värde. | [CONCAT](sql-query-concat.md), [INNEHÅLLER](sql-query-contains.md), [slutarmed](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [vänster,](sql-query-left.md) [längd](sql-query-length.md), [lägre,](sql-query-lower.md) [LTRIM](sql-query-ltrim.md), [ersätt](sql-query-replace.md), [replikera](sql-query-replicate.md), [BAKÅT](sql-query-reverse.md), [HÖGER](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [BÖRJARMED](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Funktioner för typkontroll](sql-query-type-checking-functions.md)|Med funktionerna för typkontroll kan du kontrollera typen av ett uttryck i SQL-frågor. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Inbyggda kontra användardefinierade funktioner (UDFs)

Om du för närvarande använder en användardefinierad funktion (UDF) som en inbyggd funktion nu är tillgänglig för, blir motsvarande inbyggda funktion snabbare att köra och effektivare.

## <a name="built-in-versus-ansi-sql-functions"></a>Inbyggda jämfört med ANSI SQL-funktioner

Den största skillnaden mellan Cosmos DB-funktioner och ANSI SQL-funktioner är att Cosmos DB-funktioner är utformade för att fungera bra med schemalösa och blandade schemadata. Om en egenskap till exempel saknas eller har `unknown`ett icke-numeriskt värde som hoppas objektet över i stället för att returnera ett fel.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Matrisfunktioner](sql-query-array-functions.md)
- [Datum- och tidsfunktioner](sql-query-date-time-functions.md)
- [Matematiska funktioner](sql-query-mathematical-functions.md)
- [Spatiella funktioner](sql-query-spatial-functions.md)
- [Strängfunktioner](sql-query-string-functions.md)
- [Funktioner för typkontroll](sql-query-type-checking-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Samlingar](sql-query-aggregates.md)
