---
title: CONCAt i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen CONCAt i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351258"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som sammanfogar till de andra värdena. Funktionen `CONCAT` kräver minst två *str_expr* -argument.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras sammanfogad sträng med de angivna värdena.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
