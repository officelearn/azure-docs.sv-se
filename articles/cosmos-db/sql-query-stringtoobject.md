---
title: StringToObject i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen StringToObject i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296389"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Returnerar uttryck som översätts till ett objekt. Om uttrycket inte kan översättas returnerar det odefinierade.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck som ska tolkas som ett JSON-objektuttryck. Observera att kapslade strängvärden måste skrivas med dubbla citattecken för att vara giltiga. Mer information om JSON-formatet finns [i json.org](https://json.org/)  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett objektuttryck eller odefinierat.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel `StringToObject` visas hur det fungerar mellan olika typer. 
  
 Följande är exempel med giltig indata.

```sql
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

Här är resultatuppsättningen.

```json
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Följande är exempel med ogiltig indata.
Även om de är giltiga i en fråga, kommer de inte att tolka till giltiga objekt. Strängar i objektsträngen måste antingen fly\\"{\\"a ":\\"str\\"}" eller det omgivande citatet måste vara enstaka {"a": "str"}.

Enstaka citattecken som omger egenskapsnamn är ogiltiga JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Här är resultatuppsättningen.

```json
[{}]
```  

Egenskapsnamn utan omgivande citattecken är inte giltiga JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Här är resultatuppsättningen.

```json
[{}]
``` 

Följande är exempel med ogiltig indata.

 Uttrycket som skickas tolkas som ett JSON-objekt. Dessa indata utvärderar inte att skriva objekt och därmed returnera odefinierade.

```sql
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
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
