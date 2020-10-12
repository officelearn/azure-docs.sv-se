---
title: StringToObject i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function StringToObject i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c3e61d1efe20910d84ef4ff583d74982b3ea9f3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "78296389"
---
# <a name="stringtoobject-azure-cosmos-db"></a>StringToObject (Azure Cosmos DB)
 Returnerar uttryck översatt till ett objekt. Om uttrycket inte kan översättas returneras undefined.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToObject(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som ska tolkas som ett JSON-objekt uttryck. Observera att kapslade sträng värden måste skrivas med dubbla citat tecken för att vara giltiga. Mer information om JSON-formatet finns i [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett objekt uttryck eller ett odefinierat objekt.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur `StringToObject` beter sig mellan olika typer. 
  
 Följande är exempel på giltiga indatatyper.

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

 Följande är exempel på ogiltiga indatatyper.
Även om de är giltiga i en fråga, kommer de inte att parsas till giltiga objekt. Strängar inom en objekt sträng måste antingen vara undantagna "{ \\ " a \\ ": \\ " Str \\ "} eller så måste det omgivande citatet vara en enstaka {" a ":" Str "}.

Enkla citat tecken som omger egenskaps namnen är inte giltiga JSON.

```sql
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

Här är resultatuppsättningen.

```json
[{}]
```  

Egenskaps namn utan omgivande citat tecken är inte giltiga JSON.

```sql
SELECT 
    StringToObject("{a:[1,2,3]}")
```

Här är resultatuppsättningen.

```json
[{}]
``` 

Följande är exempel på ogiltiga indatatyper.

 Det överförda uttrycket kommer att parsas som ett JSON-objekt. dessa indata utvärderas inte till Type-objekt och returneras därför inte.

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

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
