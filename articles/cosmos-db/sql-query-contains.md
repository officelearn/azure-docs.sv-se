---
title: INNEHÅLLER i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen CONTAINS SQL system i Azure Cosmos DB Returnerar ett booleskt värde som anger om det första sträng uttrycket innehåller det andra
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0c25b63fb6a7bf42bd2ec5b9503cac2cce7583f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302601"
---
# <a name="contains-azure-cosmos-db"></a>INNEHÅLLER (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger huruvida det första stränguttrycket innehåller det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONTAINS(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är sträng uttrycket som ska genomsökas.  
  
*str_expr2*  
   Är sträng uttrycket som ska hittas.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrol leras om "ABC" innehåller "AB" och om "ABC" innehåller "d".  
  
```sql
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"c1": true, "c2": false}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
