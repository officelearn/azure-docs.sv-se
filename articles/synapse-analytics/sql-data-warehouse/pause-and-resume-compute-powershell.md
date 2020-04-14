---
title: Pausa och återuppta beräkning i Synapse SQL-pool med Azure PowerShell
description: Du kan använda Azure PowerShell för att pausa och återuppta Synapse SQL-poolen (datalager). beräkna resurser.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 44ce56238140de90145f69e966c94be8572c7749
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252310"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-with-azure-powershell"></a>Snabbstart: Pausa och återuppta beräkning i Synapse SQL-pool med Azure PowerShell

Du kan använda Azure PowerShell för att pausa och återuppta beräkningsresurserna för Synapse SQL-pool (datalager).
Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här snabbstarten förutsätter att du redan har en SQL-pool som du kan pausa och återuppta. Om du behöver skapa en kan du använda [Skapa och Anslut - portal för](create-data-warehouse-portal.md) att skapa en SQL-pool som heter **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör du [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standard kör du [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-sql-pool-information"></a>Söka efter information om SQL-pooler

Leta reda på databasnamn, servernamn och resursgrupp för den SQL-pool som du planerar att pausa och återuppta.

Så här hittar du platsinformation för DIN SQL-pool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på vänster sida i Azure-portalen.
1. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW).** SQL-poolen öppnas.

    ![Servernamn och resursgrupp](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Skriv ned SQL-poolnamnet, som är databasnamnet. Anteckna även servernamnet och resursgruppen.
1. Använd bara den första delen av servernamnet i PowerShell-cmdlets. I föregående bild är det fullständiga servernamnet sqlpoolservername.database.windows.net. Vi använder **sqlpoolservername** som servernamn i PowerShell-cmdleten.

## <a name="pause-compute"></a>Pausa beräkning

Om du vill spara kostnader kan du pausa och återuppta beräkningsresurser på begäran. Om du till exempel inte använder databasen under natten och på helgerna kan du pausa den under dessa tider och återuppta den under dagen.

>[!NOTE]
>Det finns ingen avgift för beräkningsresurser medan databasen pausas. Du fortsätter dock att debiteras för lagring.

Om du vill pausa en databas använder du cmdleten [Suspend-AzSqlDatabase.](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) I följande exempel pausas en SQL-pool med namnet **mySampleDataWarehouse** som finns på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resursgrupp med namnet **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

I följande exempel hämtas databasen till $database-objektet. Det rör sedan objektet till [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Resultaten lagras i objektresultatetDatabas. Det slutliga kommandot visar resultaten.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Återuppta beräkning

Starta en databas genom att använda cmdleten [Resume-AzSqlDatabase.](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) I följande exempel startar en databas med namnet **mySampleDataWarehouse** som finns på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resursgrupp med namnet **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

I nästa exempel hämtas databasen till $database objektet. Det rör sedan objektet till [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och lagrar resultaten i $resultDatabase. Det slutliga kommandot visar resultaten.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Kontrollera status för sql-poolåtgärden

Om du vill kontrollera status för din SQL-pool använder du cmdleten [Get-AzSqlDatabaseActivity.](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i SQL-poolen. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagring, pausa beräkning.
- Om du vill ta bort framtida avgifter kan du ta bort SQL-poolen.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure-portalen](https://portal.azure.com)och klicka på din SQL-pool.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När SQL-poolen är pausad visas en **Start-knapp.**  Klicka på **Starta** om du vill återuppta beräkningarna.

3. Om du vill ta bort SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **Ta bort**.

4. Om du vill ta bort den SQL-server som du skapade klickar du **på sqlpoolservername.database.windows.net**och sedan på **Ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om SQL-pool fortsätter du till [artikeln Läs in data i SQL Pool.](load-data-from-azure-blob-storage-using-polybase.md) Mer information om hur du hanterar beräkningsfunktioner finns i artikeln [Hantera beräkningsöversikt.](sql-data-warehouse-manage-compute-overview.md)
