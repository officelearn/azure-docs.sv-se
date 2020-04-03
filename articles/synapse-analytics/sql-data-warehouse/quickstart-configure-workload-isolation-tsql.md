---
title: 'Snabbstart: Konfigurera arbetsbelastningsisolering - T-SQL'
description: Använd T-SQL för att konfigurera arbetsbelastningsisolering.
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
ms.openlocfilehash: c4920b2a5b4ff0b1a94fa8fa0e83f72761802b97
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583803"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Snabbstart: Konfigurera arbetsbelastningsisolering med T-SQL

I den här snabbstarten skapar du snabbt en arbetsbelastningsgrupp och klassificerare för att reservera resurser för datainläsning. Arbetsbelastningsgruppen allokerar 20 % av systemresurserna till en databelastning.  Arbetsbelastningsklassificeraren tilldelar begäranden till arbetsbelastningsgruppen för databelastningar.  Med 20 % isolering för databelastningar är de garanterade resurser för att träffa SLA:er.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!NOTE]
> Om du skapar en Synapse SQL-pool i Azure Synapse Analytics kan det leda till en ny fakturerbar tjänst.  Mer information finns i [Azure Synapse Analytics-priser](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Krav
 
Den här snabbstarten förutsätter att du redan har en Synapse SQL-pool i Azure Synapse och att du har behörigheter för CTRL-databas. Om du behöver skapa ett använder du [Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Skapa inloggning för DataLoads

Skapa en SQL Server-autentiseringsinloggning i `master` databasen med CREATE [LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för "ELTLogin".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Skapa användare

[Skapa användare](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "ELTLogin", i mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Skapa en arbetsbelastningsgrupp
Skapa en [arbetsbelastningsgrupp](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för DataLoads med 20 % isolering.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Skapa en arbetsbelastningsklassificerare

Skapa en [arbetsbelastningsklassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för att mappa ELTLogin till arbetsbelastningsgruppen DataLoads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Visa befintliga arbetsbelastningsgrupper och klassificerare och körningsvärden

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

Du debiteras för informationslagerenheter och data som lagras i ditt informationslager. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagring kan du pausa beräkningen när du inte använder SQL-poolen. Genom att pausa beräkningen debiteras du bara för datalagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ dessa steg för att rensa resurser.

1. Logga in på [Azure-portalen](https://portal.azure.com)och välj på ditt informationslager.

    ![Rensa resurser](./media/quickstart-configure-workload-isolation-tsql/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **Pausa.** När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort informationslagret så att du inte debiteras för beräkning eller lagring väljer du **Ta bort**.

4. Om du vill ta bort den SQL-server som du skapade markerar du **mynewserver-20180430.database.windows.net** i föregående bild och väljer sedan **Ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen markerar du **myResourceGroup**och väljer sedan **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

- Du har nu skapat en arbetsbelastningsgrupp. Kör några frågor som ELTLogin för att se hur de presterar. Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om du vill visa frågor och den tilldelade arbetsbelastningsgruppen.
- Mer information om Hantering av Synapse SQL-arbetsbelastning finns i [Arbetsbelastningshantering](sql-data-warehouse-workload-management.md) och [arbetsbelastningsisolering](sql-data-warehouse-workload-isolation.md).
