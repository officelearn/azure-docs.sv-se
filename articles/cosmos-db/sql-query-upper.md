---
title: UPPER i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL-systemfunktionen UPPER i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303978"
---
# <a name="upper-azure-cosmos-db"></a>ÖVRE (Azure Cosmos DB)
 Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler.  

Funktionen UPPER-system använder inte indexet. Om du planerar att göra frekventa fall okänsliga jämförelser, kan upper-systemfunktionen förbruka en betydande mängd RU: s. Om så är fallet, i stället för att använda funktionen ÖVRE system för att normalisera data varje gång för jämförelser, kan du normalisera höljet vid insättning. Sedan en fråga som VÄLJ * FRÅN c där UPPER (c.name) = "BOB" helt enkelt blir VÄLJ * FRÅN c där c.name = "BOB".

## <a name="syntax"></a>Syntax
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas `UPPER` hur du använder i en fråga  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
