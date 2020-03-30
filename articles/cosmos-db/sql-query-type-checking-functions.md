---
title: Skriv kontrollfunktioner i Azure Cosmos DB-frågespråk
description: Lär dig mer om typkontroll av SQL-systemfunktioner i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349075"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Typkontrollfunktioner (Azure Cosmos DB)

Med textkontrollfunktionerna kan du kontrollera typen av ett uttryck i en SQL-fråga. Du kan använda typkontrollfunktioner för att bestämma vilka typer av egenskaper som finns i objekt i farten, när de är variabla eller okända. 

## <a name="functions"></a>Funktioner

Här är en tabell med inbyggda typkontrollfunktioner som stöds:

Följande funktioner stöder typkontroll mot indatavärden och returnerar var och en ett booleskt värde.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Nästa steg

- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Samlingar](sql-query-aggregates.md)
