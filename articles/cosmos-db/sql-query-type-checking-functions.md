---
title: Typ kontroll funktioner i Azure Cosmos DB frågespråk
description: Lär dig mer om typ kontroll av SQL system-funktioner i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bafc599ddb502d5714b08ff58ed942e9c17557a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93093766"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Typ kontroll funktioner (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Med typ kontroll funktionerna kan du kontrol lera typen av uttryck i en SQL-fråga. Du kan använda typ kontroll funktioner för att avgöra vilka typer av egenskaper som finns i objekt i farten, när de är variabla eller okända. 

## <a name="functions"></a>Funktioner

Här är en tabell över inbyggda typ kontroll funktioner som stöds:

Följande funktioner stöder typ kontroll mot indatavärden och varje returnera ett booleskt värde.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Nästa steg

- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Aggregeringar](sql-query-aggregates.md)
