---
title: 'Snabbstart: Pausa och återuppta beräkning i Azure SQL Data Warehouse – PowerShell | Microsoft Docs'
description: Använd PowerShell för att pausa beräkning i Azure SQL Data Warehouse för att spara kostnader. Återuppta beräkning när du är redo att använda data lagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 03/20/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 220d2aefd714cd3546fa4d5c2ac8852d2786b8ac
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575417"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>Snabbstart: Pausa och återuppta beräkning i Azure SQL Data Warehouse med PowerShell

Använd PowerShell för att pausa beräkning i Azure SQL Data Warehouse för att spara kostnader. [Återuppta beräkning](sql-data-warehouse-manage-compute-overview.md) när du är redo att använda data lagret.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här snabb starten förutsätter att du redan har ett SQL Data Warehouse som du kan pausa och återuppta. Om du behöver skapa ett informations lager med namnet **mySampleDataWarehouse**kan du använda [skapa och ansluta-portalen](create-data-warehouse-portal.md) .

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

Leta upp databasens namn, servernamnet och resursgruppen för det informationslager som du tänker pausa och återuppta.

Följ de här anvisningarna för att hitta platsen för ditt informationslager.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **SQL-databaser** på den vänstra sidan i Azure-portalen.
3. Välj **mySampleDataWarehouse** på sidan **SQL-databaser**. Informationslagret öppnas.

    ![Servernamn och resursgrupp](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Skriv ner data lager namnet, som är databas namnet. Anteckna även servernamnet och resursgruppen.
6. Om din server är foo.database.windows.net använder du bara den första delen som servernamn i dina PowerShell-cmdlets. I den föregående bilden är det fullständiga servernamnet newserver-20171113.database.windows.net. Ta bort suffixet och Använd **NewServer-20171113** som server namn i PowerShell-cmdleten.

## <a name="pause-compute"></a>Pausa beräkning

För att spara kostnader kan du pausa och återuppta beräknings resurser på begäran. Om du till exempel inte använder databasen under natten och på helger, kan du pausa den under dessa tider och återuppta den under dagen. Det kostar inget att beräkna resurser när databasen har pausats. Men du kan fortsätta att debiteras för lagring.

Använd cmdleten [suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) om du vill pausa en databas. I följande exempel pausas ett informations lager med namnet **mySampleDataWarehouse** som finns på en server med namnet **NewServer-20171113**. Servern finns i en Azure-resurs grupp med namnet **myResourceGroup**.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

En variation, det här nästa exempel hämtar databasen till $database-objektet. Sedan rör det objektet för att [pausa-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). Resultaten lagras i objektet resultDatabase. Det slutliga kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Återuppta beräkning

Använd cmdleten [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) för att starta en databas. I följande exempel startas en databas med namnet mySampleDataWarehouse som finns på en server med namnet NewServer-20171113. Servern finns i en Azure-resurs grupp med namnet myResourceGroup.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

En variation, det här nästa exempel hämtar databasen till $database-objektet. Sedan rör det objektet för att [återuppta-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) och lagrar resultaten i $resultDatabase. Det slutliga kommandot visar resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-data-warehouse-operation"></a>Kontrol lera statusen för data lager åtgärden

Använd cmdleten [Get-AzSqlDatabaseActivity](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseActivity#description) för att kontrol lera statusen för ditt informations lager.

```
Get-AzSqlDatabaseActivity -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database02"
```

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i informationslagret. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringen ska du pausa beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på [Azure Portal](https://portal.azure.com)och klicka på ditt informations lager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När informationslagret har pausats visas knappen **Starta**.  Klicka på **Starta** om du vill återuppta beräkningarna.

3. Om du vill ta bort informationslagret så att du varken debiteras för beräkning eller lagring klickar du på **Ta bort**.

4. Om du vill ta bort den SQL-Server som du har skapat klickar du på **mynewserver-20171113.Database.Windows.net**och sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg

Du har nu pausat och återupptagit beräkningen för ditt informations lager. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
> [Läs in data i en SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
