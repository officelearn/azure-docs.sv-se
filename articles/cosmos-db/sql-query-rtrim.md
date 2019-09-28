---
title: RTRIM i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function RTRIM i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 511c085b465b8b70ae71c298054bcb535773837e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349502"
---
# <a name="rtrim-azure-cosmos-db"></a>RTRIM (Azure Cosmos DB)
 Returnerar ett stränguttryck efter att det tar bort avslutande blanksteg.  
  
## <a name="syntax"></a>Syntax
  
```sql
RTRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `RTRIM` inuti en fråga.  
  
```sql
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
