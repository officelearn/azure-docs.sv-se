---
title: Ersätt i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen Ersätt i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2cb7d82efd010fd7c3395a4f6a9217370d9e5779
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349594"
---
# <a name="replace-azure-cosmos-db"></a>Ersätt (Azure Cosmos DB)
 Ersätter alla förekomster av ett angivet strängvärde med ett annat strängvärde.  
  
## <a name="syntax"></a>Syntax
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är sträng uttrycket som ska genomsökas.  
  
*str_expr2*  
   Är sträng uttrycket som ska hittas.  
  
*str_expr3*  
   Är sträng uttrycket för att ersätta förekomster av *str_expr2* i *str_expr1*.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `REPLACE` i en fråga.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
