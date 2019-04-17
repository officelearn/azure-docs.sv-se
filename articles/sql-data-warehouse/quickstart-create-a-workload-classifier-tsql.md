---
title: 'Snabbstart: Skapa en arbetsbelastning klassificerare - T-SQL | Microsoft Docs'
description: Använd T-SQL för att skapa en klassificerare för arbetsbelastning med hög prioritet
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 198faf6791a4a2caa2cefee2181a13ed8185310e
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617345"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql-preview"></a>Snabbstart: Skapa en klassificerare för arbetsbelastning med T-SQL (förhandsversion)

I den här snabbstarten kommer du snabbt skapa en klassificerare för arbetsbelastning med hög prioritet för VD för din organisation. Den här arbetsbelastningen klassificerare kan VD frågor att få företräde framför andra frågor med lägre prioritet i kön.

> [!Note]
> Klassificering för arbetsbelastningen är tillgänglig för förhandsversionen av SQL Data Warehouse Gen2. Klassificering för hantering av arbetsbelastning och vikten förhandsversion är för versioner med ett frisläppningsdatum av 9 April 2019 eller senare.  Användare bör undvika att använda versioner tidigare än det här datumet för arbetsbelastningen management testning.  För att fastställa om din version är hantering av arbetsbelastning kan du köra väljer @@version när du är ansluten till din SQL Data Warehouse-instans.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten förutsätter att du redan har ett SQL data warehouse och att du har KONTROLLDATABAS-behörighet. Om du behöver skapa ett använder du [Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Skapa inloggning för TheCEO

Skapa en SQL Server-autentiseringsinloggning i den `master` databasen med [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) för 'TheCEO'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Skapa användare

[Skapa användare](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), ”TheCEO” i mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Skapa en klassificerare för arbetsbelastning

Skapa en [arbetsbelastning klassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) för ”TheCEO” med hög prioritet.

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

Debiteras du för informationslagerenheter och data som lagras i ditt informationslager. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa databearbetning, du debiteras endast för lagring av data. Återuppta beräkningarna när du är redo att arbeta med data.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ dessa steg för att rensa resurser.

1. Logga in på den [Azure-portalen](https://portal.azure.com)väljer på ditt informationslager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen, Välj den **pausa** knappen. När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningarna Välj **starta**.

3. Om du vill ta bort informationslagret så att du inte debiteras för beräkning eller lagring, Välj **ta bort**.

4. Om du vill ta bort den SQLServer som du skapade, Välj **mynewserver-20180430.database.windows.net** i föregående bild och välj sedan **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen, Välj **myResourceGroup**, och välj sedan **ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Nu har du skapat en arbetsbelastning klassificerare. Köra några frågor som TheCEO att se hur de fungerar. Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) visa frågor och den prioritet som tilldelas.

Läs mer om SQL Data Warehouse arbetsbelastningshantering [SQL Data Warehouse arbetsbelastning vikten](sql-data-warehouse-workload-importance.md) och [SQL Data Warehouse arbetsbelastning klassificering](sql-data-warehouse-workload-classification.md).
