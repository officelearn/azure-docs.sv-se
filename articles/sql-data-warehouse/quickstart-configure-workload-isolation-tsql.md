---
title: 'Snabb start: Konfigurera arbets belastnings isolering-T-SQL'
description: Använd T-SQL för att konfigurera arbets belastnings isolering.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 11/21/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 2a6c5ca9f7d2ceaef08b28e78b38b94a459548f5
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304752"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Snabb start: Konfigurera arbets belastnings isolering med T-SQL

I den här snabb starten skapar du snabbt en arbets belastnings grupp och en klassificerare för att reservera resurser för data inläsning. Arbets belastnings gruppen kommer att allokera 20% av system resurserna till en data belastning.  Klassificeringen av arbets belastningen tilldelar förfrågningar till arbets belastnings gruppen för data inläsningar.  Med 20% isolering för data inläsningar är de garanterat resurser för att nå service avtal.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information finns på [prissidan för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Förutsättningar
 
Den här snabb starten förutsätter att du redan har en SQL Data Warehouse och att du har behörighet att kontrol lera databasen. Om du behöver skapa ett använder du [Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Skapa inloggning för DataLoads

Skapa en inloggning för SQL Server autentisering i `master`-databasen med hjälp av [create login](/sql/t-sql/statements/create-login-transact-sql) for ' ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Skapa användare

[Skapa användare](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "ELTLogin", i mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Skapa en arbets belastnings grupp
Skapa en [arbets belastnings grupp](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) för DataLoads med 20% isolering.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Skapa en klassificering för arbets belastning

Skapa en [arbets belastnings klassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) för att mappa ELTLogin till arbets belastnings gruppen DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Visa befintliga arbets belastnings grupper och klassificerare och kör tids värden

```sql
--Workload groups
SELECT * FROM 
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM 
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM 
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Rensa resurser

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
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

- Nu har du skapat en arbets belastnings grupp. Kör några frågor som ELTLogin för att se hur de fungerar. Se [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) för att visa frågor och arbets belastnings gruppen som tilldelats.
- Mer information om Azure SQL Data Warehouse arbets belastnings hantering finns i [arbets belastnings hantering](sql-data-warehouse-workload-management.md) och [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md).
