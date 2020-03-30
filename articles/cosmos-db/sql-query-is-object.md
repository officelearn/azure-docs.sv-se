---
title: IS_OBJECT i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion IS_OBJECT i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303808"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger om typen av det angivna uttrycket är ett JSON-objekt.  
  
## <a name="syntax"></a>Syntax
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*Uttryck*  
   Är vilket uttryck som helst.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrollerar objekt av JSON Boolean, tal, sträng, null, `IS_OBJECT` objekt, matris och odefinierade typer med hjälp av funktionen.  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="remarks"></a>Anmärkningar

Denna systemfunktion kommer att dra nytta av ett [intervallindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Typkontrollfunktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
