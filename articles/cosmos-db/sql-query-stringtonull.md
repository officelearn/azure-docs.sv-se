---
title: StringToNull i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen StringToNull i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296447"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Returnerar uttryck översatt till null. Om uttrycket inte kan översättas returnerar det odefinierade.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck som ska tolkas som ett null-uttryck.
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett null-uttryck eller odefinierat.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel `StringToNull` visas hur det fungerar mellan olika typer. 

Följande är exempel med giltig indata.

 Blanktecken tillåts endast före eller efter "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Följande är exempel med ogiltig indata.

Null är skiftlägeskänsligt och måste skrivas med alla gemener, dvs "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Här är resultatuppsättningen.  
  
```json
[{}]
```  

Uttrycket som skickas tolkas som ett null-uttryck. Dessa indata utvärderar inte för att skriva null och returnerar därmed odefinierade.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
