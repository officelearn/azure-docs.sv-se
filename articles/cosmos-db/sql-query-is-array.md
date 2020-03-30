---
title: IS_ARRAY i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion IS_ARRAY i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5867850db6eb3d6552bc129cca3708ef7747072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303893"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är en matris.  
  
## <a name="syntax"></a>Syntax
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*Uttryck*  
   Är vilket uttryck som helst.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrollerar objekt av JSON Boolean, tal, sträng, null, `IS_ARRAY` objekt, matris och odefinierade typer med hjälp av funktionen.  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="remarks"></a>Anmärkningar

Denna systemfunktion kommer att dra nytta av ett [intervallindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Typkontrollfunktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
