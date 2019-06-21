---
title: Konfigurera vikten för arbetsbelastningen i Azure SQL Data Warehouse | Microsoft Docs
description: Lär dig mer om att begäran på prioritet.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 10a1fe2bff43b6799f47fc0245802ce578ef8f8f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165535"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Konfigurera vikten för arbetsbelastningen i Azure SQL Data Warehouse

Ange prioritet i SQL Data Warehouse kan du påverka planeringen av frågor. Frågor med högre prioritet schemaläggs att köras innan frågor med lägre prioritet. Om du vill ange prioritet frågor, måste du skapa en klassificerare för arbetsbelastning.

## <a name="create-a-workload-classifier-with-importance"></a>Skapa en klassificerare för arbetsbelastning med prioritet

I ett scenario med data warehouse har ofta användare som behöver deras frågor körs snabbt.  Användaren kan chefer på företaget som behöver köra rapporter eller användaren kan vara en analytiker som en ad hoc-fråga som körs. Du skapar en arbetsbelastning klassificerare för att ange prioritet till en fråga.  Exemplen nedan använder den nya [skapa arbetsbelastning klassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) syntaxen för att skapa två klassificerare.  Medlemsnamn kan vara en enskild användare eller en grupp. Enskilda användare klassificeringar företräde framför rollen klassificeringar. Om du vill hitta befintliga data warehouse-användare, kör du:

```sql
Select name from sys.sysusers
```

Om du vill skapa en klassificerare för arbetsbelastning för en användare med högre prioritet kör:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Skapa en klassificerare för arbetsbelastning för en användare som kör ad hoc-frågor med lägre prioritet som kör:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om hantering av arbetsbelastning, [arbetsbelastning klassificering](sql-data-warehouse-workload-classification.md)
- Läs mer på vikten [arbetsbelastning prioritet](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till hantera och övervaka arbetsbelastningen prioritet](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
