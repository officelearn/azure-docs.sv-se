---
title: 'Snabbstart: Skapa en arbetsbelastningsklassificerare - T-SQL'
description: Använd T-SQL för att skapa en arbetsbelastningsklassificerare med stor prioritet.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 6354390c44c0991b0d6bf36cba8efd4272f9c73d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583772"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Snabbstart: Skapa en arbetsbelastningsklassificerare med T-SQL

I den här snabbstarten skapar du snabbt en arbetsbelastningsklassificerare med stor betydelse för VD:n för din organisation. Den här arbetsbelastningsklassificeraren gör det möjligt för VD-frågor att ha företräde framför andra frågor med lägre betydelse i kön.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Om du skapar en Synapse SQL-poolinstans i Azure Synapse Analytics kan det leda till en ny fakturerbar tjänst.  Mer information finns i [Azure Synapse Analytics-priser](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Krav

Den här snabbstarten förutsätter att du redan har ett SQL Data Warehouse och att du har behörigheter för KONTROLLDATABAS. Om du behöver skapa ett använder du [Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Skapa inloggning för TheCEO

Skapa en SQL Server-autentiseringsinloggning i `master` databasen med CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql) för 'TheCEO'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Skapa användare

[Skapa användare](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO", i mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Skapa en arbetsbelastningsklassificerare

Skapa en [arbetsbelastningsklassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) för "TheCEO" med stor betydelse.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Visa befintliga klassificerare

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Rensa resurser

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Du debiteras för informationslagerenheter och data som lagras i ditt informationslager. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkningen debiteras du bara för datalagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ dessa steg för att rensa resurser.

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj på ditt informationslager.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **Pausa.** När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort informationslagret så att du inte debiteras för beräkning eller lagring väljer du **Ta bort**.

4. Om du vill ta bort den SQL-server som du skapade markerar du **mynewserver-20180430.database.windows.net** i föregående bild och väljer sedan **Ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen markerar du **myResourceGroup**och väljer sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

- Du har nu skapat en arbetsbelastningsklassificerare. Kör några frågor som TheCEO för att se hur de presterar. Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) om du vill visa frågor och den tilldelade betydelsen.
- Mer information om Synapse SQL-arbetsbelastningshantering finns i [Arbetsbelastningsbetydning](sql-data-warehouse-workload-importance.md) och [arbetsbelastningsklassificering](sql-data-warehouse-workload-classification.md).
- Se hur du gör-artiklar för att [konfigurera arbetsbelastningsbetydning](sql-data-warehouse-how-to-configure-workload-importance.md) och hur du [hanterar och övervakar arbetsbelastningshantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
