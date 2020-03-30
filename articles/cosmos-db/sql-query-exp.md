---
title: EXP i Azure Cosmos DB-frågespråk
description: Lär dig mer om SQL-systemfunktionen Exponent (EXP) i Azure Cosmos DB för att returnera exponentiellt värde för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873326"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Returnerar exponentiellt värde för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Returtyper
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Anmärkningar
  
  Konstanten **e** (2.718281...), är basen av naturliga logaritmer.  
  
  Exponenten för ett tal är den **konstanta e** som lyfts till talets kraft. TILL exempel EXP(1,0) = e^1,0 = 2,71828182845905 och EXP(10) = e^10 = 22026.4657948067.  
  
  Exponentialen av den naturliga logaritmen av en numrera är numrera sig själv: EXP (LOG (n)) = n. Och den naturliga logaritmen av exponentialen av ett numrerar är numrera sig själv: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel deklareras en variabel och exponentiellt värde för den angivna variabeln (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 I följande exempel returneras det exponentiella värdet av den naturliga logaritmen på 20 och den naturliga logaritmen för exponentiellt av 20. Eftersom dessa funktioner är omvända funktioner för varandra är returvärdet med avrundning för flyttalsmatet i båda fallen 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systemfunktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
