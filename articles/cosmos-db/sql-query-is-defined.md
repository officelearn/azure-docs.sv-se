---
title: IS_DEFINED i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion IS_DEFINED i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a5fcaf52d8e9e6b942a95f0b0c43f3f654c5d5d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303859"
---
# <a name="is_defined-azure-cosmos-db"></a>IS_DEFINED (Azure Cosmos DB)
 Returnerar ett booleskt värde som anger huruvida egenskapen har tilldelats ett värde.  
  
## <a name="syntax"></a>Syntax
  
```sql
IS_DEFINED(<expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*Uttryck*  
   Är vilket uttryck som helst.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel kontrolleras om det finns en egenskap i det angivna JSON-dokumentet. Den första returnerar sant eftersom "a" är närvarande, men den andra returnerar falskt eftersom "b" är frånvarande.  
  
```sql
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"isDefined1":true,"isDefined2":false}]  
```  

## <a name="remarks"></a>Anmärkningar

Denna systemfunktion kommer att dra nytta av ett [intervallindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Typkontrollfunktioner Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
