---
title: "Skapa ett SQL Data Warehouse med hjälp av PowerShell | Microsoft Docs"
description: "Skapa ett SQL Data Warehouse med hjälp av PowerShell"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 58ba34f8f99b7cd2b6a9a199bc70d79431405100
ms.lasthandoff: 03/10/2017


---
# <a name="create-sql-data-warehouse-using-powershell"></a>Skapa ett SQL Data Warehouse med hjälp av PowerShell
> [!div class="op_single_selector"]
> * [Azure-portalen](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

I den här artikeln visas hur du skapar ett SQL Data Warehouse med PowerShell.

## <a name="prerequisites"></a>Krav
Du behöver följande för att komma igång:

* **Azure-konto**: Gå till [Kostnadsfri utvärderingsversion av Azure][Azure Free Trial] eller [MSDN Azure-krediter][MSDN Azure Credits] för att skapa ett konto.
* **Azure SQL Server**: Mer information finns i [Skapa en Azure SQL-databas i Azure Portal][Create an Azure SQL database in the Azure Portal] eller [Skapa en Azure SQL-databas med PowerShell][Create an Azure SQL database with PowerShell].
* **Resursgrupp**: Använd antingen samma resursgrupp som din Azure SQL-server eller se [hur du kan skapa en resursgrupp](../azure-resource-manager/resource-group-portal.md).
* **PowerShell version 1.0.3 eller senare**: Du kan kontrollera din version genom att köra **Get-Module -ListAvailable -Name Azure**.  Den senaste versionen kan installeras från [Installationsprogram för Microsoft-webbplattform][Microsoft Web Platform Installer].  Mer information om hur du installerar den senaste versionen finns i [Installera och konfigurera Azure PowerShell][How to install and configure Azure PowerShell].

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Mer information om priser finns i [Priser för SQL Data Warehouse][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>Skapa ett SQL Data Warehouse
1. Öppna Windows PowerShell.
2. Kör den här cmdleten för att logga in på Azure Resource Manager.

    ```Powershell
    Login-AzureRmAccount
    ```
3. Välj den prenumeration som du vill använda för din session.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Skapa databas. Det här exemplet skapas en databas med namnet ”mynewsqldw” med tjänstmål-nivån ”DW400” till servern ”sqldwserver1” som finns i resursgruppen med namnet ”mywesteuroperesgp1”.

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

Erfordrade parametrar är:

* **RequestedServiceObjectiveName**: Antalet [DWU][DWU] som du begär.  Värden som stöds är: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 och DW6000.
* **DatabaseName**: Namnet på det SQL Data Warehouse som du skapar.
* **ServerName**: namnet på den server som du använder för att skapa (måste vara V12).
* **ResourceGroupName**: Resursgruppen som du använder dig av.  Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
* **Version**: Måste vara ”DataWarehouse” för att skapa ett SQL Data Warehouse.

Valfria parametrar är:

* **CollationName**: Standardsortering om inte annat anges är SQL_Latin1_General_CP1_CI_AS.  Sorteringen kan inte ändras för en databas.
* **MaxSizeBytes**: Maximal standardstorlek för en databas är 10 GB.

Mer information om parameteralternativen finns i [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] och [Skapa databas (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Nästa steg
När ditt SQL Data Warehouse är färdigetablerat kan du prova att [läsa in exempeldata][loading sample data] eller lära dig hur du [utvecklar][develop], [läser in][load] eller [migrerar][migrate].

Mer information om hur du hanterar SQL Data Warehouse via programmering finns i vår artikel om hur du använder [PowerShell-cmdlets och REST-API:er][PowerShell cmdlets and REST APIs].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

