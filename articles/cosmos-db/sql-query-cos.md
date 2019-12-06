---
title: COS i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen cosinus (COS) i SQL system i Azure Cosmos DB Returnerar det trigonometriska cosinus för den angivna vinkeln, i radianer, i det angivna uttrycket
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 05546ef531059e05a1426a288a48446d63d8e5df
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873411"
---
# <a name="cos-azure-cosmos-db"></a>COS (Azure Cosmos DB)
 Returnerar trigonometrisk cosinus för den angivna vinkeln i radianer i det angivna uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
COS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel beräknas `COS` av den angivna vinkeln.  
  
```sql
SELECT COS(14.78) AS cos  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"cos": -0.59946542619465426}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
