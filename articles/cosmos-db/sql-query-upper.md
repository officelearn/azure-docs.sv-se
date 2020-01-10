---
title: ÖVRE i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen i övre Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9809a138a5e28eb069d545d39cfda815c915bd78
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728896"
---
# <a name="upper-azure-cosmos-db"></a>ÖVRE (Azure Cosmos DB)
 Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler.  

Den övre system funktionen använder inte indexet. Om du planerar att göra frekventa Skift läges känsliga jämförelser kan den övre systemfunktionen använda en betydande mängd RU. I så fall, i stället för att använda funktionen VERSALer för att normalisera data varje gång för jämförelser, kan du normalisera höljet vid infogning. Sedan väljer du en fråga som SELECT * FROM c där (c. Name) = ' BOB ' bara väljer * från c WHERE c.name = ' BOB '.

## <a name="syntax"></a>Syntax
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `UPPER` i en fråga  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
