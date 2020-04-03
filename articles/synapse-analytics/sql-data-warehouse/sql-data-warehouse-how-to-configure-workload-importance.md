---
title: Konfigurera arbetsbelastningsprioritet
description: Lär dig hur du anger prioritet på begäransnivå i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 4aa0e7da1e77334a8b9048dab4a461133f220bd1
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582449"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurera arbetsbelastningsbetydelse i Azure Synapse Analytics

Om du anger prioritet i Synapse SQL för Azure Synapse kan du påverka schemaläggningen av frågor. Frågor med högre prioritet kommer att schemaläggas för att köras före frågor med lägre prioritet. Om du vill tilldela prioritet till frågor måste du skapa en arbetsbelastningsklassificerare.

## <a name="create-a-workload-classifier-with-importance"></a>Skapa en arbetsbelastningsklassificerare med prioritet

Ofta i ett informationslager scenario har du användare som behöver sina frågor för att köra snabbt.  Användaren kan vara chefer för företaget som behöver köra rapporter eller användaren kan vara en analytiker som kör en adhoc fråga. Du skapar en arbetsbelastningsklassificerare för att tilldela prioritet till en fråga.  Exemplen nedan använder den nya [klassenifierarsyntaxen](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) för att skapa två klassificerare.  Medlemsnamn kan vara en enskild användare eller en grupp. Individuella användarklassificeringar har företräde framför rollklassificeringar. Så här hittar du befintliga datalageranvändare:

```sql
Select name from sys.sysusers
```

Så här skapar du en arbetsbelastningsklassificerare för en användare med högre prioritet:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Så här skapar du en arbetsbelastningsklassificerare för en användare som kör adhoc-frågor med lägre prioritet:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Efterföljande moment
- Mer information om arbetsbelastningshantering finns i [Arbetsbelastningsklassificering](sql-data-warehouse-workload-classification.md)
- Mer information om Betydelse finns i [Arbetsbelastningsbetydning](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till Hantera och övervaka arbetsbelastningsbetydning](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
