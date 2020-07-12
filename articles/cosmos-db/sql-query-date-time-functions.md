---
title: Datum-och tids funktioner i Azure Cosmos DB frågespråk
description: Lär dig mer om datum-och tids funktioner i SQL system-funktioner i Azure Cosmos DB att utföra DateTime-och timestamp-åtgärder.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246945"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Datum-och tids funktioner (Azure Cosmos DB)

Med funktionen för datum-och tids funktioner kan du utföra DateTime-och timestamp-åtgärder i Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funktioner för att hämta datum och tid

Med följande skalära funktioner kan du hämta aktuellt UTC-datum och-tid i två former: en sträng som följer ISO 8601-formatet eller en numerisk tidstämpel vars värde är UNIX-epoken i millisekunder:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>Funktioner för att arbeta med DateTime-värden

Med följande funktioner kan du enkelt ändra DateTime-värden:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>Nästa steg

- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Aggregeringar](sql-query-aggregates.md)
