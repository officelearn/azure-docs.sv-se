---
title: LÄGRE Azure Cosmos DB frågespråk
description: Lär dig mer om den lägre SQL system-funktionen i Azure Cosmos DB att returnera ett sträng uttryck efter konvertering av versaler till gemener
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 163b893b74b5f6d3950ae92e209f96abde8cd90e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075287"
---
# <a name="lower-azure-cosmos-db"></a>LÄGRE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
  
  Returnerar ett sträng uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `LOWER` i en fråga.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
