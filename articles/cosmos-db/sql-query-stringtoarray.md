---
title: StringToArray i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen StringToArray i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302924"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Returnerar uttryck översatt till en matris. Om uttrycket inte kan översättas returnerar det odefinierade.  
  
## <a name="syntax"></a>Syntax
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck som ska tolkas som ett JSON-arrayuttryck. 
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett matrisuttryck eller odefinierat. 
  
## <a name="remarks"></a>Anmärkningar
  Kapslade strängvärden måste skrivas med dubbla citattecken för att vara giltiga JSON. Mer information om JSON-formatet finns [i json.org](https://json.org/)
  
## <a name="examples"></a>Exempel
  
  I följande exempel `StringToArray` visas hur det fungerar mellan olika typer. 
  
 Följande är exempel med giltig indata.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Här är resultatuppsättningen.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Följande är ett exempel på ogiltig indata. 
   
 Enstaka citattecken i matrisen är inte giltiga JSON.
Även om de är giltiga i en fråga, kommer de inte att tolka till giltiga matriser. Strängar i matrissträngen måste antingen fly\\\\"[ " "]" eller det omgivande citatet måste vara en enda "["]'.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Här är resultatuppsättningen.

```json
[{}]
```

Följande är exempel på ogiltig indata.
   
 Uttrycket som skickas tolkas som en JSON-matris. Följande utvärderar inte att skriva matris och därmed returnera odefinierad.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Här är resultatuppsättningen.

```json
[{}]
```

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
