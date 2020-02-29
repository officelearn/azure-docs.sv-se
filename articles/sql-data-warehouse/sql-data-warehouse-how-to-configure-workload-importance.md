---
title: Konfigurera arbetsbelastningsprioritet
description: Lär dig hur du ställer in viktig information i Azure Synapse Analytics.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 8b2a4333717938edf9f3039e29e8df88cece7cc1
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196806"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurera arbets belastnings prioritet i Azure Synapse Analytics

Genom att ange prioritet i SQL Analytics för Azure Synapse kan du påverka schemaläggningen av frågor. Frågor med högre prioritet schemaläggs att köras innan frågor med lägre prioritet. Om du vill tilldela prioritet för frågor måste du skapa en klassificering för arbets belastning.

## <a name="create-a-workload-classifier-with-importance"></a>Skapa en klassificering av arbets belastningar med prioritet

I ett informations lager scenario har du ofta användare som behöver sina frågor för att kunna köras snabbt.  Användaren kan vara chefer för företaget som behöver köra rapporter eller så kan användaren vara en analytiker som kör en adhoc-fråga. Du skapar en arbets belastnings klassificerare för att tilldela prioritet för en fråga.  I exemplen nedan används den nya klassificerings-syntaxen för [create-arbetsbelastning](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) för att skapa två klassificerare.  Membername kan vara en enskild användare eller en grupp. Enskilda användar klassificeringar prioriteras framför roll klassificeringar. Om du vill hitta befintliga data lager användare kör du:

```sql
Select name from sys.sysusers
```

För att skapa en arbets belastnings klassificering för en användare med en högre prioritets körning:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Så här skapar du en arbets belastnings klassificering för en användare som kör adhoc-frågor med lägre prioritets körning:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Nästa steg
- Mer information om arbets belastnings hantering finns i avsnittet om [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md)
- Mer information om prioritet finns i [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till hantera och övervaka arbets belastnings prioritet](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
