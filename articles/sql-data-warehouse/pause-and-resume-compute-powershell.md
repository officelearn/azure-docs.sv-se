---
title: 'Snabb start: pausa & återuppta beräkning – PowerShell '
description: Använd PowerShell för att pausa beräkning i Azure Synapse Analytics SQL-poolen för att spara kostnader. Återuppta beräkning när du är redo att använda data lagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ce183edef9e5895d7b3f702f5466c505956a869a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200574"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-with-azure-powershell"></a>Snabb start: pausa och återuppta beräkning i Azure Synapse Analytics SQL-poolen med Azure PowerShell

Använd Azure PowerShell för att pausa beräkning för SQL-poolen för att spara kostnader. [Återuppta beräkning](sql-data-warehouse-manage-compute-overview.md) när du är redo att använda data lagret.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här snabb starten förutsätter att du redan har en SQL-pool som du kan pausa och återuppta. Om du behöver skapa ett kan du använda [skapa och ansluta-portalen](create-data-warehouse-portal.md) för att skapa en SQL-pool med namnet **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör du [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standardinställningen kör du [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Leta upp information om informationslager

Leta upp databas namnet, Server namnet och resurs gruppen för SQL-poolen som du planerar att pausa och återuppta.

Följ de här stegen för att hitta plats information för SQL-poolen.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
1. Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** . Informationslagret öppnas.

    ![Servernamn och resursgrupp](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Skriv ner data lager namnet, som är databas namnet. Anteckna även servernamnet och resursgruppen.
1. Använd endast den första delen av Server namnet i PowerShell-cmdletar. I föregående bild är det fullständiga Server namnet sqlpoolservername.database.windows.net. Vi använder **sqlpoolservername** som server namn i PowerShell-cmdleten.

## <a name="pause-compute"></a>Pausa beräkning

För att spara kostnader kan du pausa och återuppta beräknings resurser på begäran. Om du till exempel inte använder databasen under natten och på helger, kan du pausa den under dessa tider och återuppta den under dagen. Det kostar inget att beräkna resurser när databasen har pausats. Men du kan fortsätta att debiteras för lagring.

Använd cmdleten [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) om du vill pausa en databas. I följande exempel pausas ett informations lager med namnet **mySampleDataWarehouse** som finns på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resurs grupp med namnet **myResourceGroup**.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "nsqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

En variation, det här nästa exempel hämtar databasen till $database-objektet. Sedan rör det objektet för att [pausa-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). Resultaten lagras i objektet resultDatabase. Det slutliga kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Återuppta beräkning

Använd cmdleten [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) för att starta en databas. I följande exempel startas en databas med namnet **mySampleDataWarehouse** som finns på en server med namnet **sqlpoolservername**. Servern finns i en Azure-resurs grupp med namnet **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

En variation, det här nästa exempel hämtar databasen till $database-objektet. Sedan rör det objektet för att [återuppta-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) och lagrar resultaten i $resultDatabase. Det slutliga kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Kontrol lera statusen för data lager åtgärden

Använd cmdleten [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) för att kontrol lera statusen för ditt informations lager.

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i informationslagret. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringen ska du pausa beräkningen.
- Om du vill ta bort framtida avgifter kan du ta bort SQL-poolen.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och klicka på SQL-poolen.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När SQL-poolen har pausats visas en **Start** -knapp.  Klicka på **Starta** om du vill återuppta beräkningarna.

3. Om du vill ta bort SQL-poolen så att du inte debiteras för beräkning eller lagring klickar du på **ta bort**.

4. Om du vill ta bort den SQL-Server som du har skapat klickar du på **sqlpoolservername.Database.Windows.net**och sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg

Du har nu pausat och återupptagit beräkningen för SQL-poolen. Om du vill veta mer om SQL-poolen fortsätter du till självstudien för att läsa in data.

> [!div class="nextstepaction"]
> [Läs in data i SQL-pool](load-data-from-azure-blob-storage-using-polybase.md)
