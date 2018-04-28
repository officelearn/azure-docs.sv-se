---
title: 'Snabbstart: Pausa och återuppta compute i Azure SQL Data Warehouse - PowerShell | Microsoft Docs'
description: Använd PowerShell för att pausa beräkning i Azure SQL Data Warehouse att minska kostnaderna. Återuppta databearbetning när du är redo att använda datalagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ef341a1528bf759461abfb7cfc6d878fd8a44cb4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>Snabbstart: Pausa och återuppta compute i Azure SQL Data Warehouse med PowerShell
Använd PowerShell för att pausa beräkning i Azure SQL Data Warehouse att minska kostnaderna. [Återuppta databearbetning](sql-data-warehouse-manage-compute-overview.md) när du är redo att använda datalagret.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här kursen kräver Azure PowerShell Modulversion 5.1.1 eller senare. Kör ` Get-Module -ListAvailable AzureRM` reda på vilken version du har för närvarande. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps.md) (Installera Azure PowerShell-modul).

## <a name="before-you-begin"></a>Innan du börjar

Denna Snabbstart förutsätter att du redan har ett SQL data warehouse som du kan pausa och återuppta. Om du behöver skapa en kan du använda [skapa och Connect - portal](create-data-warehouse-portal.md) att skapa ett data warehouse kallas **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) och följ anvisningarna på skärmen.

```powershell
Connect-AzureRmAccount
```

Om du vill se vilken prenumeration som du använder kör [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Om du behöver använda en annan prenumeration än standardvärdet kör [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Leta upp datalagerinformation

Leta upp databasens namn, servernamn och resursgruppen för datalagret som du planerar att pausa och återuppta.

Följ dessa steg för att hitta platsen för ditt informationslager.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **SQL-databaser** i den vänstra sidan av Azure-portalen.
3. Välj **mySampleDataWarehouse** från den **SQL-databaser** sidan. Datalagret öppnas.

    ![Namn och resursen servergrupp](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Skriv ned namnet på datalager, vilket är namnet på databasen. Även Skriv ned namnet på servern och resursgruppen. Du
5.  dessa i kommandona paus och fortsätt.
6. Om din server är foo.database.windows.net Använd bara den första delen som servernamn i PowerShell-cmdlets. I den föregående bilden är det fullständiga servernamnet newserver 20171113.database.windows.net. Ta bort suffixet och använda **newserver 20171113** som servernamn i PowerShell-cmdleten.

## <a name="pause-compute"></a>Pausa beräkning
Du kan pausa och återuppta beräkning resurser på begäran för att spara kostnader. Till exempel om du inte använder databasen under natten och helger, kan du pausa under dessa tider och återuppta under dagen. Är gratis för beräkningsresurser när databasen har pausats. Dock fortsätta att debiteras för lagring.

Pausa en databas genom att använda den [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase.md) cmdlet. Följande exempel pausar ett data warehouse med namnet **mySampleDataWarehouse** finns på en server med namnet **newserver 20171113**. Servern är i ett Azure-resursgrupp med namnet **myResourceGroup**.


```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

En variant exemplet nästa hämtar databasen till $database-objekt. Den kommer sedan objektet till [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase). Resultatet lagras i objektet resultDatabase. Kommandot visas resultatet.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Återuppta beräkning
Starta en databas med den [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase) cmdlet. Följande exempel startar en databas med namnet mySampleDataWarehouse som finns på en server med namnet newserver 20171113. Servern är i ett Azure-resursgrupp med namnet myResourceGroup.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

En variant exemplet nästa hämtar databasen till $database-objekt. Den kommer sedan objektet till [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase.md) och lagrar resultatet i $resultDatabase. Kommandot visas resultatet.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

## <a name="clean-up-resources"></a>Rensa resurser

Du debiteras för informationslagerenheter och data som lagras i informationslagret. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill skydda data i lagring pausa beräkning.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Följ dessa steg för att rensa resurser enligt dina önskemål.

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på ditt informationslager.

    ![Rensa resurser](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Om du vill pausa beräkningarna klickar du på knappen **Pausa**. När informationslagret har pausats visas knappen **Starta**.  Klicka på **Starta** om du vill återuppta beräkningarna.

2. Om du vill ta bort informationslagret så att du varken debiteras för beräkning eller lagring klickar du på **Ta bort**.

3. Ta bort SQL-server som du har skapat, klicka på **mynewserver 20171113.database.windows.net**, och klicka sedan på **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

4. Om du vill ta bort resursgruppen klickar du på **myResourceGroup** och sedan på **Ta bort resursgrupp**.


## <a name="next-steps"></a>Nästa steg
Du har nu pausas och återupptas beräkning för ditt informationslager. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
