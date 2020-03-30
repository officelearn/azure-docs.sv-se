---
title: INDEX_OF i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktion INDEX_OF i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71350983"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 Returnerar startpositionen för den första förekomsten av det andra stränguttrycket i det första angivna stränguttrycket eller -1 om strängen inte hittas.  
  
## <a name="syntax"></a>Syntax
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är stränguttrycket som ska sökas igenom.  
  
*str_expr2*  
   Är stränguttrycket att söka efter.  

*numeric_expr* Valfrit numeriskt uttryck som anger den position sökningen ska starta. Den första positionen i *str_expr1* är 0. 
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras indexet för olika delsträngar inuti "abc".  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Strängfunktioner Azure Cosmos DB](sql-query-string-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
