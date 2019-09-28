---
title: System funktioner i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktioner i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 98a087f02035545b828cfcf11c58d03d02a5e76a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349173"
---
# <a name="system-functions-azure-cosmos-db"></a>System funktioner (Azure Cosmos DB)

 Cosmos DB innehåller många inbyggda SQL-funktioner. Kategorier av inbyggda funktionerna finns nedan.  
  
|Funktionsgrupp|Beskrivning|Åtgärder|  
|--------------|-----------------|-----------------| 
|[Matrisfunktioner](sql-query-array-functions.md)|Matrisfunktioner kan du utföra en åtgärd på en matris indatavärdet och returnera numeriska, booleskt värde eller Matrisvärde. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Datum-och tids funktioner](sql-query-date-time-functions.md)|Med funktionerna för datum och tid kan du hämta aktuellt UTC-datum och tid i två formulär; en numerisk tidstämpel vars värde är UNIX-epoken i millisekunder eller som en sträng som följer ISO 8601-formatet. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md) |
|[Matematiska funktioner](sql-query-mathematical-functions.md)|Matematiska funktioner utför en beräkning, vanligtvis baserat på indatavärden som tillhandahålls som argument och returnerar ett numeriskt värde. | [ABS](sql-query-abs.md), [ARCCOS](sql-query-acos.md), [ARCSIN](sql-query-asin.md), [ARCTAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [TAK](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [GRADER](sql-query-degrees.md), [EXP](sql-query-exp.md), [GOLV](sql-query-floor.md), [LOGG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [STRÖM](sql-query-power.md), [RADIANER](sql-query-radians.md), [RAND](sql-query-rand.md), [AVRUNDA](sql-query-round.md), [SIGNERA](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [KVADRAT](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Spatial funktioner](sql-query-spatial-functions.md)|Funktionerna spatial utföra en åtgärd på en spatialobjektet indatavärdet och returnerar ett numeriskt eller booleskt värde. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Strängfunktioner](sql-query-string-functions.md)|Strängfunktioner utföra en åtgärd på ett strängvärde för indata och returnerar en sträng, numeriskt eller booleskt värde. | [concat](sql-query-concat.md), [innehåller](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [Left](sql-query-left.md), [length](sql-query-length.md), [lägre](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [replace](sql-query-replace.md), [Replikera](sql-query-replicate.md), [Omvänd](sql-query-reverse.md), [Right](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [ STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [substring](sql-query-substring.md), [toString](sql-query-tostring.md), [trim](sql-query-trim.md), [versal](sql-query-upper.md) |
|[Funktioner för typkontroll](sql-query-type-checking-functions.md)|Med funktionerna för typkontroll kan du kontrollera typen av ett uttryck i SQL-frågor. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Inbyggda kontra användardefinierade funktioner (UDF: er)

Om du för närvarande använder en användardefinierad funktion (UDF) för vilken en inbyggd funktion nu är tillgänglig, kan motsvarande inbyggda funktion gå snabbare att köra och effektivare.

## <a name="built-in-versus-ansi-sql-functions"></a>Inbyggda jämfört med ANSI SQL Functions

Den största skillnaden mellan Cosmos DB Functions och ANSI SQL Functions är att Cosmos DB funktioner är utformade för att fungera bra med schema lösa och blandade schema data. Om t. ex. en egenskap saknas eller har ett icke-numeriskt värde som `unknown`, hoppas objektet över i stället för att returnera ett fel.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Matrisfunktioner](sql-query-array-functions.md)
- [Datum-och tids funktioner](sql-query-date-time-functions.md)
- [Matematiska funktioner](sql-query-mathematical-functions.md)
- [Spatial funktioner](sql-query-spatial-functions.md)
- [Strängfunktioner](sql-query-string-functions.md)
- [Funktioner för typkontroll](sql-query-type-checking-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Agg regeringar](sql-query-aggregates.md)
