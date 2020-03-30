---
title: ERSÄTT i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen ERSÄTT i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302210"
---
# <a name="replace-azure-cosmos-db"></a>ERSÄTT (Azure Cosmos DB)
 Ersätter alla förekomster av ett angivet strängvärde med ett annat strängvärde.  
  
## <a name="syntax"></a>Syntax
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är stränguttrycket som ska sökas igenom.  
  
*str_expr2*  
   Finns stränguttrycket.  
  
*str_expr3*  
   Är stränguttrycket som ska ersätta förekomster av *str_expr2* i *str_expr1*.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett stränguttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas `REPLACE` hur du använder i en fråga.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Anmärkningar

Den här systemfunktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
