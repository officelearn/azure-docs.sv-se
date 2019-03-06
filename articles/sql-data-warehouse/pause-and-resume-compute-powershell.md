---
title: 'Snabbstart: Pausa och återuppta beräkning i Azure SQL Data Warehouse – PowerShell | Microsoft Docs'
description: Använd PowerShell för att pausa beräkning i Azure SQL Data Warehouse för att sänka kostnaderna. Återuppta beräkningarna när du är redo att använda datalagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6dd378f69dbf0c7cffe04d4b6a18fd3e6cda6b8b
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409983"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>Snabbstart: Pausa och återuppta beräkning i Azure SQL Data Warehouse med PowerShell
Använd PowerShell för att pausa beräkning i Azure SQL Data Warehouse för att sänka kostnaderna. [Återuppta beräkningarna](sql-data-warehouse-manage-compute-overview.md) när du är redo att använda datalagret.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här snabbstarten förutsätter att du redan har ett SQL data warehouse som du kan pausa och återuppta. Om du vill skapa ett kan du använda [skapa och ansluta – portal](create-data-warehouse-portal.md) att skapa ett informationslager med namnet **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med den [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) och följer den på skärmen riktningar.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration som du använder kör [Get-AzSubscription](/powershell/module/az.profile/get-azsubscription).

```powershell
Get-AzSubscription
```

Om du vill använda en annan prenumeration än standard, kör [Set-AzContext](/powershell/module/az.profile/set-azcontext).

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

4. Skriv ned namnet på informationslagret, vilket är namnet på databasen. Anteckna även servernamnet och resursgruppen. Du
5.  dessa i kommandona pausa och återuppta.
6. Om din server är foo.database.windows.net använder du bara den första delen som servernamn i dina PowerShell-cmdlets. I den föregående bilden är det fullständiga servernamnet newserver-20171113.database.windows.net. Ta bort suffixet och använda **newserver-20171113** som servernamn i PowerShell-cmdleten.

## <a name="pause-compute"></a>Pausa databearbetning
Du kan pausa och återuppta beräkning resurser på begäran för att spara kostnader. Till exempel om du inte använder databasen under natten och helger, kan du pausa under dessa tider och återuppta den under dagen. Det finns ingen kostnad för beräkningsresurser när databasen har pausats. Dock fortsätta att betala för lagring.

För att pausa en databas, använda den [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase) cmdlet. Följande exempel pausar ett informationslager med namnet **mySampleDataWarehouse** finns på en server med namnet **newserver-20171113**. Servern är i ett Azure-resursgrupp med namnet **myResourceGroup**.


```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

En variant den här nästa exempel hämtar databasen i $database-objekt. Det kommer sedan objektet till [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase). Resultaten lagras i objektet resultDatabase. Det slutliga kommandot visas resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Återuppta beräkning
Starta en databas med den [återuppta AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) cmdlet. I följande exempel startar en databas med namnet mySampleDataWarehouse som finns på en server med namnet newserver-20171113. Servern är i ett Azure-resursgrupp som heter myResourceGroup.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

En variant den här nästa exempel hämtar databasen i $database-objekt. Det kommer sedan objektet till [återuppta AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase) och lagrar resultatet i $resultDatabase. Det slutliga kommandot visas resultatet.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i informationslagret. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagring, pausa beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på ditt informationslager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När informationslagret har pausats visas knappen **Starta**.  Klicka på **Starta** om du vill återuppta beräkningarna.

2. Om du vill ta bort informationslagret så att du varken debiteras för beräkning eller lagring klickar du på **Ta bort**.

3. Ta bort den SQLServer som du skapade genom att klicka på **mynewserver 20171113.database.windows.net**, och klicka sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

4. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg
Du har nu pausas och återupptas beräkning för informationslagret. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
