---
title: StringToBoolean i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen StringToBoolean i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296549"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Returnerar uttryck översatt till en boolesk. Om uttrycket inte kan översättas returnerar det odefinierade.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck som ska tolkas som ett booleskt uttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt uttryck eller odefinierat.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel `StringToBoolean` visas hur det fungerar mellan olika typer. 
 
 Följande är exempel med giltig indata.

Blanktecken är tillåtet endast före eller efter "sant"/"falskt".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Följande är exempel med ogiltig indata.

 Booles är skiftlägeskänsliga och måste skrivas med alla gemener, dvs "sant" och "falskt".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Här är resultatuppsättningen.  
  
```json
[{}]
``` 

Uttrycket som skickas tolkas som ett booleskt uttryck. Dessa indata utvärderar inte att skriva Boolean och därmed returnera odefinierad.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
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
