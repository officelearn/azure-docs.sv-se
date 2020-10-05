---
title: 'Snabb start: skapa en klassificering för arbets belastning – Portal'
description: Använd Azure Portal för att skapa en klassificering för arbets belastning med hög prioritet.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 691cdcb525f8e9e3d1fb914372b9f62366f4bfba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "85213031"
---
# <a name="quickstart-create-a-synapse-sql-pool-workload-classifier-using-the-azure-portal"></a>Snabb start: skapa en Synapse SQL-pool arbets belastnings klassificering med hjälp av Azure Portal

I den här snabb starten ska du skapa en [klassificering för arbets belastning](sql-data-warehouse-workload-classification.md) för att tilldela frågor till en arbets belastnings grupp.  Klassificeraren tilldelar begär Anden från `ELTLogin` SQL-användaren till `DataLoads` arbets belastnings gruppen.   Följ självstudierna [snabb start: Konfigurera arbets belastnings isolering](quickstart-configure-workload-isolation-portal.md) för att skapa `DataLoads` arbets belastnings gruppen.  Den här självstudien skapar en klassificerare för arbets belastning med alternativet WLM_LABEL som hjälper till att ytterligare klassificera begär Anden.  Klassificeraren tilldelar också `HIGH` [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md) för dessa begär Anden.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.


## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Att skapa en instans av SQL-poolen i Azure Synapse Analytics kan resultera i en ny fakturerbar tjänst.  Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten förutsätter att du redan har en instans av SQL-poolen i Synapse SQL och att du har behörighet att kontrol lera databasen. Om du behöver skapa ett använder du [Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.
<br><br>
Det finns en arbets belastnings grupp `DataLoads` .  Se självstudierna [snabb start: Konfigurera arbets belastnings isolering](quickstart-configure-workload-isolation-portal.md) för att skapa arbets belastnings gruppen.
<br><br>
>[!IMPORTANT] 
>SQL-poolen måste vara online för att konfigurera arbets belastnings hantering. 


## <a name="create-a-login-for-eltlogin"></a>Skapa en inloggning för ELTLogin

Skapa en inloggning för SQL Server autentisering i `master` databasen med hjälp av [Skapa inloggning](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för `ELTLogin` .

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user-and-grant-permissions"></a>Skapa användare och bevilja behörigheter

När inloggningen har skapats måste du skapa en användare i-databasen.  Använd [create User](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) för att skapa SQL-användaren `ELTRole` i **mySampleDataWarehouse**.  Eftersom vi kommer att testa klassificeringen under den här självstudien beviljar `ELTLogin` du behörighet till **mySampleDataWarehouse**. 

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
GRANT CONTROL ON DATABASE::mySampleDataWarehouse TO ELTLogin 
END
;
```

## <a name="configure-workload-classification"></a>Konfigurera arbets belastnings klassificering
Klassificeringen gör att du kan dirigera förfrågningar baserat på en uppsättning regler till en arbets belastnings grupp.  I självstudierna [snabb start: Konfigurera arbets belastnings isolering](quickstart-configure-workload-isolation-portal.md) skapade vi `DataLoads` arbets belastnings gruppen.  Nu ska du skapa en klassificering för arbets belastning för att dirigera frågor till `DataLoads` arbets belastnings gruppen.


1.  Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
2.  Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** . SQL-poolen öppnas.
3.  Klicka på **arbets belastnings hantering**.

    ![Klicka på Meny](./media/quickstart-create-a-workload-classifier-portal/menu.png)

4.  Klicka på **inställningar & klassificerare** på höger sida av `DataLoads` arbets belastnings gruppen.

    ![Klicka på Skapa](./media/quickstart-create-a-workload-classifier-portal/settings-classifiers.png)

5. Klicka på **klassificerare**.
6. Klicka på **Lägg till klassificerare**.

    ![Klicka på Lägg till](./media/quickstart-create-a-workload-classifier-portal/add-wc.png)

7.  Ange `ELTLoginDataLoads` som **namn**.
8.  Ange `ELTLogin` för **medlem**.
9.  Välj `High` för **begär ande prioritet**.  *Valfri*, normal prioritet är standard.
10. Ange `fact_loads` som **etikett**.
11. Klicka på **Lägg till**.
12. Klicka på **Spara**.

    ![Klicka på Konfigurera](./media/quickstart-create-a-workload-classifier-portal/config-wc.png)

## <a name="verify-and-test-classification"></a>Verifiera och testa klassificering
Kontrol lera att klassificeraren finns i vyn [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql?view=azure-sqldw-latest) katalog `ELTLoginDataLoads` .

```sql
SELECT * FROM sys.workload_management_workload_classifiers WHERE name = 'ELTLoginDataLoads'
```

Kontrol lera i vyn [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest) Catalog om du vill verifiera klassificerings information.

```sql
SELECT c.[name], c.group_name, c.importance, cd.classifier_type, cd.classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ELTLoginDataLoads'
```

Kör följande instruktioner för att testa klassificeringen.  Se till att du är ansluten som ``ELTLogin`` och ``Label`` används i frågan.
```sql
CREATE TABLE factstaging (ColA int)
INSERT INTO factstaging VALUES(0)
INSERT INTO factstaging VALUES(1)
INSERT INTO factstaging VALUES(2)
GO

CREATE TABLE testclassifierfact WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT * FROM factstaging
OPTION (LABEL='fact_loads')
```

Verifiera `CREATE TABLE` instruktionen som klassificeras mot `DataLoads` arbets belastnings gruppen med hjälp av `ELTLoginDataLoads` arbets belastnings klassificeringen.
```sql 
SELECT TOP 1 request_id, classifier_name, group_name, resource_allocation_percentage, submit_time, [status], [label], command 
FROM sys.dm_pdw_exec_requests 
WHERE [label] = 'fact_loads'
ORDER BY submit_time DESC
```



## <a name="clean-up-resources"></a>Rensa resurser

Ta bort en `ELTLoginDataLoads` arbets belastnings klassificering som skapats i den här självstudien:

1. Klicka på **en klassificerare** på höger sida av `DataLoads` arbets belastnings gruppen.

    ![Klicka på ta bort](./media/quickstart-create-a-workload-classifier-portal/delete-wc.png)

2. Klicka på **klassificerare**.
3. Klicka på **`...`** till höger om `ELTLoginDataLoads` klassificeringen av arbets belastning.
4. Klicka på **ta bort**.
5. Klicka på **Spara**.

    ![Klicka på Spara](./media/quickstart-create-a-workload-classifier-portal/delete-save-wc.png)

Du debiteras för data lager enheter och data som lagras i ditt informations lager. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkning debiteras du bara för data lagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Rensa resurserna genom att följa dessa steg.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj på informations lagret.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **pausa** . När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort data lagret så att du inte debiteras för beräkning eller lagring väljer du **ta bort**.

4. Om du vill ta bort den SQL-Server som du har skapat väljer du **sqlpoolservername.Database.Windows.net** i föregående bild och väljer sedan **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resurs gruppen väljer du **myResourceGroup**och väljer sedan **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

Övervaka din arbets belastning med Azure Portal övervaknings mått.  Mer information finns i [Hantera och övervaka arbets belastnings hantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) .
