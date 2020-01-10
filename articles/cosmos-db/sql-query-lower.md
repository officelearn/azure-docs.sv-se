---
title: LÄGRE Azure Cosmos DB frågespråk
description: Lär dig mer om den lägre SQL system-funktionen i Azure Cosmos DB att returnera ett sträng uttryck efter konvertering av versaler till gemener
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 35efbb8d4d97ab52abb20487d15a80985946c499
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732611"
---
# <a name="lower-azure-cosmos-db"></a>LÄGRE (Azure Cosmos DB)
 Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener.  

Den lägre systemfunktionen använder inte indexet. Om du planerar att göra frekventa Skift läges känsliga jämförelser kan den lägre systemfunktionen förbruka en betydande mängd RU. I så fall, i stället för att använda funktionen för lägre system för att normalisera data varje gång för jämförelser, kan du normalisera höljet vid infogning. Sedan väljer du en fråga som SELECT * från c där lägre (c. Name) = "Robert" bara väljer * från c där c.name = ' Bob '.

## <a name="syntax"></a>Syntax
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `LOWER` i en fråga.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
