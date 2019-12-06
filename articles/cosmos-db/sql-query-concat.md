---
title: CONCAt i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen CONCAt SQL system i Azure Cosmos DB Returnerar en sträng som är resultatet av sammanfogningen av två eller fler sträng värden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adb8564d4addaaa3f4a383b32941549aed3d53bd
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871558"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
 Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som sammanfogar till de andra värdena. Funktionen `CONCAT` kräver minst två *str_expr* argument.  
  
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
