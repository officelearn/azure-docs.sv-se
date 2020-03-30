---
title: StringToNumber i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen StringToNumber i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296430"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Returnerar uttryck översatt till ett tal. Om uttrycket inte kan översättas returnerar det odefinierade.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck som ska tolkas som ett JSON-taluttryck. Tal i JSON måste vara ett heltal eller en flyttals. Mer information om JSON-formatet finns [i json.org](https://json.org/)  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett taluttryck eller odefinierat.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel `StringToNumber` visas hur det fungerar mellan olika typer. 

Blanktecken tillåts endast före eller efter numret.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Här är resultatuppsättningen.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

I JSON måste ett giltigt tal vara antingen ett heltal eller ett flyttalsnummer.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Här är resultatuppsättningen.  
  
```json
{{}}
```  

Uttrycket som skickas tolkas som ett taluttryck. Dessa indata utvärderar inte för att skriva Tal och returnerar därmed odefinierade. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Här är resultatuppsättningen.  
  
```json
{{}}
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
