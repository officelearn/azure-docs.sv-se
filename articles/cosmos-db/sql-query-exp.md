---
title: EXP i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen EXP i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 21c7ae63f46f2acd961245c59805220174c106f1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351038"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Returnerar exponentiell värdet för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
  Ständiga **e** (2.718281...) är basen för logaritmen.  
  
  E upphöjt till ett tal är konstanten **e** upphöjt till för talet. Till exempel EXP(1.0) = e ^ 1.0 = 2.71828182845905 och EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Exponenten för den naturliga logaritmen för ett tal är själva siffran: EXP (LOG (n)) = n. Och den naturliga logaritmen av exponenten för ett tal är själva siffran: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Exempel
  
  Exemplet nedan deklarerar en variabel och returnerar exponentiell värdet för den angivna variabeln (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 I följande exempel returneras exponentiell värdet för natural logarithm 20 och den naturliga logaritmen för e upphöjt till 20. Eftersom dessa funktioner är inverterade funktioner från varandra, är det returnera värdet med avrundning för flytande punkt matematiska i båda fallen 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
