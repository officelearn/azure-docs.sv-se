---
title: EXP i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen exponent (EXP) SQL system i Azure Cosmos DB att returnera exponent svärdet för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873326"
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
  
  Exponenten för den naturliga logaritmen för ett tal är antalet själva: EXP (loggning (n)) = n. Och den naturliga logaritmen för e upphöjt till ett tal är antalet själva: LOG (EXP (n)) = n.  
  
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
