---
title: LÄGRE i Azure Cosmos DB-frågespråk
description: Lär dig mer om funktionen LÄGRE SQL-system i Azure Cosmos DB för att returnera ett stränguttryck efter konvertering av versaler till gemener
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302278"
---
# <a name="lower-azure-cosmos-db"></a>LÄGRE (Azure Cosmos DB)
 Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener.  

Funktionen LOWER-system använder inte indexet. Om du planerar att göra frekventa fall okänsliga jämförelser, kan lower-systemfunktionen förbruka en betydande mängd RU: s. Om så är fallet, i stället för att använda funktionen LÄGRE system för att normalisera data varje gång för jämförelser, kan du normalisera höljet vid insättning. Sedan en fråga som VÄLJ * FRÅN c där lägre (c.name) = "bob" helt enkelt blir VÄLJ * FRÅN c där c.name = "bob".

## <a name="syntax"></a>Syntax
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett stränguttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas `LOWER` hur du använder i en fråga.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
