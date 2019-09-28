---
title: Typ kontroll funktioner i Azure Cosmos DB frågespråk
description: Lär dig mer om typ kontroll av SQL system-funktioner i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349075"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Typ kontroll funktioner (Azure Cosmos DB)

Med typ kontroll funktionerna kan du kontrol lera typen av uttryck i en SQL-fråga. Du kan använda typ kontroll funktioner för att avgöra vilka typer av egenskaper som finns i objekt i farten, när de är variabla eller okända. 

## <a name="functions"></a>Funktioner

Här är en tabell över inbyggda typ kontroll funktioner som stöds:

Typkontroll mot indatavärden stöd för följande funktioner och var och en returnerar ett booleskt värde.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Nästa steg

- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Agg regeringar](sql-query-aggregates.md)
