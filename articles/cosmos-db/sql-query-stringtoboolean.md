---
title: StringToBoolean i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function StringToBoolean i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bb31aa3ba90c54873586cfba35e668cd31e32572
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099274"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar uttryck som har översatts till ett booleskt värde. Om uttrycket inte kan översättas returneras undefined.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som ska tolkas som ett booleskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck eller odefinierat.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur `StringToBoolean` beter sig mellan olika typer. 
 
 Följande är exempel på giltiga indatatyper.

Blank steg tillåts bara före eller efter "true"/"false".

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

Följande är exempel på ogiltiga indatatyper.

 Booleska värden är Skift läges känsliga och måste skrivas med små bokstäver, d.v.s. "sant" och "falskt".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Här är resultatuppsättningen.  
  
```json
[{}]
``` 

Det överförda uttrycket kommer att parsas som ett booleskt uttryck. de här inmatningarna utvärderas inte till typen Boolean och returneras därför inte.

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

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
