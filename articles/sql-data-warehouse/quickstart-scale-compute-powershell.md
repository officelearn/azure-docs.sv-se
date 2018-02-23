---
title: "Snabbstart: Skala ut beräkning i Azure SQL Data Warehouse - PowerShell | Microsoft Docs"
description: "PowerShell-uppgifter som ska skalas ut beräkningsresurser genom att justera informationslagerenheter."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: a3a435d6bdb0d35c96349540d5e9f9b5be61bd9b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Snabbstart: Skala compute i Azure SQL Data Warehouse i PowerShell

Skala bearbetning i Azure SQL Data Warehouse i PowerShell. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) tillbaka beräkning för att spara kostnader för bättre prestanda eller skalning. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Den här kursen kräver Azure PowerShell Modulversion 5.1.1 eller senare. Kör `Get-Module -ListAvailable AzureRM` reda på vilken version du har för närvarande. Om du behöver installera eller uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps.md) (Installera Azure PowerShell-modul). 

## <a name="before-you-begin"></a>Innan du börjar

Denna Snabbstart förutsätter att du redan har ett SQL data warehouse som du kan skala. Om du behöver skapa en Använd [skapa och Connect - portal](create-data-warehouse-portal.md) att skapa ett data warehouse kallas **mySampleDataWarehouse**. 

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) och följ anvisningarna på skärmen.

```powershell
Add-AzureRmAccount
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

4. Skriv ned namnet på datalager som ska användas som databasnamn på. Även Skriv ned namnet på servern och resursgruppen. Du använder dem i pausen och återuppta kommandon.
5. Om din server är foo.database.windows.net Använd bara den första delen som servernamn i PowerShell-cmdlets. I den föregående bilden är det fullständiga servernamnet newserver 20171113.database.windows.net. Vi använder **newserver 20171113** som servernamn i PowerShell-cmdleten.

## <a name="scale-compute"></a>Skala beräkning

Du kan öka eller minska beräkningsresurser genom att justera informationslagerenheter i SQL Data Warehouse. Den [skapa och Connect - portal](create-data-warehouse-portal.md) skapade **mySampleDataWarehouse** och initieras med 400 dwu: er. Följande steg justera dwu: er för **mySampleDataWarehouse**.

Du kan ändra informationslagerenheter den [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell-cmdlet. I följande exempel anger informationslagerenheter till DW300 för databasen **mySampleDataWarehouse** som finns i resursgruppen **myResourceGroup** på servern  **mynewserver 20171113**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-database-state"></a>Kontrollera databasens status

Det aktuella tillståndet för datalagret, Använd den [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) PowerShell-cmdlet. Detta hämtar tillståndet för den **mySampleDataWarehouse** databasen i ResourceGroup **myResourceGroup** och server **mynewserver 20171113.database.windows.net**.

```powershell
Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
```

Vilket leder till ungefär så här:

```powershell   
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Du kan sedan kontrollera om du vill se den **Status** av databasen. I det här fallet kan du se att den här databasen är online.  När du kör det här kommandot får statusvärdet Online, pausa, inställd på återupptar, skalning eller pausas.

## <a name="next-steps"></a>Nästa steg
Nu har du lärt dig hur du kan skala beräkning för ditt informationslager. Om du vill veta mer om Azure SQL Data Warehouse kan fortsätta med självstudiekursen om att läsa in data.

> [!div class="nextstepaction"]
>[Läsa in data i ett SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
