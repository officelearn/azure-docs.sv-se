---
title: Konfigurera arbetsbelastningsprioritet
description: Lär dig hur du ställer in prioritet för ärende nivån.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692686"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Konfigurera prioritet för arbets belastning i Azure SQL Data Warehouse

Genom att ange prioritet i SQL Data Warehouse kan du påverka schemaläggningen av frågor. Frågor med högre prioritet schemaläggs att köras innan frågor med lägre prioritet. Om du vill tilldela prioritet för frågor måste du skapa en klassificering för arbets belastning.

## <a name="create-a-workload-classifier-with-importance"></a>Skapa en klassificering av arbets belastningar med prioritet

I ett informations lager scenario har du ofta användare som behöver sina frågor för att kunna köras snabbt.  Användaren kan vara chefer för företaget som behöver köra rapporter eller så kan användaren vara en analytiker som kör en adhoc-fråga. Du skapar en arbets belastnings klassificerare för att tilldela prioritet för en fråga.  I exemplen nedan används den nya klassificerings-syntaxen för [create-arbetsbelastning](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) för att skapa två klassificerare.  Membername kan vara en enskild användare eller en grupp. Enskilda användar klassificeringar prioriteras framför roll klassificeringar. Om du vill hitta befintliga data lager användare kör du:

```sql
Select name from sys.sysusers
```

För att skapa en arbets belastnings klassificering för en användare med en högre prioritets körning:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Så här skapar du en arbets belastnings klassificering för en användare som kör adhoc-frågor med lägre prioritets körning:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Nästa steg
- Mer information om arbets belastnings hantering finns i avsnittet om [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md)
- Mer information om prioritet finns i [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Gå till hantera och övervaka arbets belastnings prioritet](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
