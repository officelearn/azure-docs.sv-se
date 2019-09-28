---
title: SIN i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen SIN i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b4450ea9dd50f8093fa9569d7f82fe124022c3a1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349464"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Returnerar trigonometrisk sinus för den angivna vinkeln i radianer i det angivna uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel beräknas `SIN` för den angivna vinkeln.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
