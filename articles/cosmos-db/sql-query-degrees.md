---
title: GRADER i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen grader i SQL-systemet i Azure Cosmos DB för att returnera motsvarande vinkel i grader för en vinkel angiven i radianer
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5f432f5ef57c8eccc0cf629f00d2231364785b9a
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871337"
---
# <a name="degrees-azure-cosmos-db"></a>GRADER (Azure Cosmos DB)
 Returnerar motsvarande vinkel i grader för en vinkel som anges i radianer.  
  
## <a name="syntax"></a>Syntax
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras grad i en vinkeln för PI/2 radianer.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"degrees": 90}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
