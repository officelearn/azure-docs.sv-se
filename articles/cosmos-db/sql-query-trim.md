---
title: TRIM i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion TRIM i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 073efbd91e637a17693617b16c7787d61e150ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304250"
---
# <a name="trim-azure-cosmos-db"></a>TRIM (Azure Cosmos DB)
 Returnerar ett stränguttryck när det har tagit bort inledande och avslutande blanks.  
  
## <a name="syntax"></a>Syntax
  
```sql
TRIM(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas `TRIM` hur du använder i en fråga.  
  
```sql
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
