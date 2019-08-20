---
title: 'Snabbstart: Skapa en arbets belastnings klassificering-T-SQL | Microsoft Docs'
description: Använd T-SQL för att skapa en klassificering för arbets belastning med hög prioritet.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: ea2e0a3bb55d16c0b413b114fca9da7f95f5c053
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574859"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Snabbstart: Skapa en klassificering för arbets belastning med T-SQL

I den här snabb starten skapar du snabbt en arbets belastnings klassificering med stor betydelse för organisationens VD. Den här arbets belastnings klassificeraren gör det möjligt för VD-frågor att prioriteras över andra frågor med lägre prioritet i kön.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten förutsätter att du redan har en SQL Data Warehouse och att du har behörighet att kontrol lera databasen. Om du behöver skapa ett använder du [Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Skapa inloggning för TheCEO

Skapa en inloggning för SQL Server autentisering i `master` databasen med hjälp av [create login](/sql/t-sql/statements/create-login-transact-sql) for ' TheCEO '.

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

## <a name="create-a-workload-classifier"></a>Skapa en klassificering för arbets belastning

Skapa en [arbets belastnings klassificering](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) för "TheCEO" med hög prioritet.

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

Du debiteras för data lager enheter och data som lagras i ditt informations lager. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkning debiteras du bara för data lagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Rensa resurserna genom att följa dessa steg.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj på informations lagret.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **pausa** . När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort data lagret så att du inte debiteras för beräkning eller lagring väljer du **ta bort**.

4. Om du vill ta bort den SQL-Server som du har skapat väljer du **mynewserver-20180430.Database.Windows.net** i föregående bild och väljer sedan **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resurs gruppen väljer du **myResourceGroup**och väljer sedan **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

- Du har nu skapat en arbets belastnings klassificering. Kör några frågor som TheCEO för att se hur de fungerar. Se [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) för att visa frågor och prioriteten som tilldelats.
- Mer information om Azure SQL Data Warehouse arbets belastnings hantering finns i sekretess för [arbets belastning](sql-data-warehouse-workload-importance.md) och [arbets belastning](sql-data-warehouse-workload-classification.md).
- Se instruktions artiklar för att [Konfigurera arbets belastnings prioritet](sql-data-warehouse-how-to-configure-workload-importance.md) och hur du [hanterar och övervakar arbets belastnings hantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
