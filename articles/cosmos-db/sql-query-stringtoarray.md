---
title: StringToArray i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function StringToArray i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349272"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Returnerar uttryck som har översatts till en matris. Om uttrycket inte kan översättas returneras undefined.  
  
## <a name="syntax"></a>Syntax
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som ska tolkas som ett JSON-mat ris uttryck. 
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett mat ris uttryck eller odefinierat. 
  
## <a name="remarks"></a>Kommentarer
  Kapslade sträng värden måste skrivas med dubbla citat tecken för att vara giltiga JSON. Mer information om JSON-formatet finns i [JSON.org](https://json.org/)
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur `StringToArray` beter sig mellan olika typer. 
  
 Följande är exempel på giltiga indatatyper.

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

Följande är ett exempel på ogiltiga indatatyper. 
   
 Enkla citat tecken i matrisen är inte giltiga JSON.
Även om de är giltiga i en fråga går de inte att parsa till giltiga matriser. Strängar i mat ris strängen måste antingen föregås av "[\\" \\ "]" eller så måste det omgivande citatet vara en enstaka ["]".

```sql
SELECT
    StringToArray("['5','6','7']")
```

Här är resultatuppsättningen.

```json
[{}]
```

Följande är exempel på ogiltiga indatatyper.
   
 Det överförda uttrycket kommer att parsas som en JSON-matris. följande utvärderar inte till typ mat ris och returnerar således odefinierad.
   
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

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
