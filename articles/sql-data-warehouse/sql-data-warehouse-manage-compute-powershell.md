---
title: Hantera datorkraft i Azure SQL Data Warehouse (PowerShell) | Microsoft Docs
description: "PowerShell-uppgifter för hantering av beräkningskraft. Skala beräkningsresurser genom att justera dwu: er. Eller, pausa och återuppta beräkningsresurser för att spara kostnader."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6a185d96447c2e1b0b463439dd062081e783da5f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Hantera datorkraft i Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [Översikt](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

## <a name="before-you-begin"></a>Innan du börjar
### <a name="install-the-latest-version-of-azure-powershell"></a>Installera den senaste versionen av Azure PowerShell
> [!NOTE]
> Om du vill använda Azure PowerShell med SQL Data Warehouse, behöver du Azure PowerShell version 1.0.3 eller senare.  Kontrollera din nuvarande version kör kommandot **Get-Module - ListAvailable-Name Azure**. Du kan installera den senaste versionen från [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Mer information finns i [hur du installerar och konfigurerar du Azure PowerShell][How to install and configure Azure PowerShell].
>
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Kom igång med Azure PowerShell-cmdlets
Att komma igång:

1. Öppna Azure PowerShell.
2. Köra dessa kommandon för att logga in till Azure Resource Manager och välja din prenumeration i PowerShell-Kommandotolken.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Skala datorkraft
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Du kan ändra de dwu: er i [Set-AzureRmSqlDatabase] [ Set-AzureRmSqlDatabase] PowerShell-cmdlet. I följande exempel anger servicenivåmålet till DW1000 för MySQLDW som finns på servern minserver-databasen.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausa beräkning
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pausa en databas genom att använda den [Suspend-AzureRmSqlDatabase] [ Suspend-AzureRmSqlDatabase] cmdlet. Följande exempel pausar en databas med namnet Database02 som finns på en server med namnet Server01. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

> [!NOTE]
> Observera att om din server är foo.database.windows.net, använda ”foo” - servernamn i PowerShell-cmdlets.
>
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
En variant exemplet nästa hämtar databasen till $database-objekt. Den kommer sedan objektet till [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Resultatet lagras i objektet resultDatabase. Kommandot visas resultatet.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Återuppta beräkning
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Starta en databas med den [Resume-AzureRmSqlDatabase] [ Resume-AzureRmSqlDatabase] cmdlet. Följande exempel startar en databas med namnet Database02 som finns på en server med namnet Server01. Servern är i ett Azure-resursgrupp med namnet ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

En variant exemplet nästa hämtar databasen till $database-objekt. Den kommer sedan objektet till [Resume-AzureRmSqlDatabase] [ Resume-AzureRmSqlDatabase] och lagrar resultatet i $resultDatabase. Kommandot visas resultatet.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state"></a>Kontrollera databasens status

I ovanstående exempel visas en kan använda [Get-AzureRmSqlDatabase] [ Get-AzureRmSqlDatabase] cmdlet om du vill ha information om en databas, vilket kontrollerar status, men ska användas som ett argument. 

```powershell
Get-AzureRmSqlDatabase [-ResourceGroupName] <String> [-ServerName] <String> [[-DatabaseName] <String>]
 [-InformationAction <ActionPreference>] [-InformationVariable <String>] [-Confirm] [-WhatIf]
 [<CommonParameters>]
```

Som kommer att resultera i något att 

```powershell   
ResourceGroupName             : nytrg
ServerName                    : nytsvr
DatabaseName                  : nytdb
Location                      : West US
DatabaseId                    : 86461aae-8e3d-4ded-9389-ac9d4bc69bbb
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1General_CP1CI_AS
CatalogCollation              :
MaxSizeBytes                  : 32212254720
Status                        : Online
CreationDate                  : 10/26/2016 4:33:14 PM
CurrentServiceObjectiveId     : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
CurrentServiceObjectiveName   : System2
RequestedServiceObjectiveId   : 620323bf-2879-4807-b30d-c2e6d7b3b3aa
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           : 1/1/0001 12:00:00 AM
```

Där du kan sedan kontrollera för att se den *Status* av databasen. I det här fallet kan du se att den här databasen är online. 

När du kör det här kommandot får statusvärdet antingen Online, pausa, inställd på återupptar, skalning och pausad.

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Nästa steg
Andra hanteringsåtgärder finns [översikt över][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Get-AzureRmSqlDatabase]: /powershell/servicemanagement/azure.sqldatabase/v1.6.1/get-azuresqldatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Azure portal]: http://portal.azure.com/
