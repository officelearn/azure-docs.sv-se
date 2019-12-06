---
title: INNEHÅLLER i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen CONTAINS SQL system i Azure Cosmos DB Returnerar ett booleskt värde som anger om det första sträng uttrycket innehåller det andra
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 59c0e2628bfde24200bd386adc858ba778790231
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871541"
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

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
