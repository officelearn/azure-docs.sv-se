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
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5a101aa78dbac4f1a0edb7f414b44c14db392652
ms.openlocfilehash: 7107c64a25b3f60d7789cec57181eec9ce844f6b


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

* **Azure-konto**: Gå till [Kostnadsfri utvärderingsversion av Azure][Kostnadsfri utvärderingsversion av Azure] eller [MSDN Azure-krediter][MSDN Azure-krediter] för att skapa ett konto.
* **Azure SQL-server**: Du hittar mer information i [Skapa en logisk Azure SQL Database-server med Azure-portalen][Skapa en logisk Azure SQL Database-server med Azure-portalen] eller [Skapa en logisk Azure SQL Database-server med PowerShell][Skapa en logisk Azure SQL Database-server med PowerShell].
* **Resursgrupp**: Använd antingen samma resursgrupp som din Azure SQL-server eller se [hur du kan skapa en resursgrupp][hur du kan skapa en resursgrupp].
* **PowerShell version 1.0.3 eller senare**: Du kan kontrollera din version genom att köra **Get-Module -ListAvailable -Name Azure**.  Den senaste versionen kan installeras från [Microsoft installationsprogram för webbplattform][Microsoft installationsprogram för webbplattform].  Mer information om hur du installerar den senaste versionen finns [Hur du installerar och konfigurerar Azure PowerShell][Hur du installerar och konfigurerar Azure PowerShell].

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Se [Priser för SQL Data Warehouse][Priser för SQL Data Warehouse] för mer information om priser.
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

Mer information om parameteralternativ för finns [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] och [Skapa databas (Azure SQL Data Warehouse)][Skapa databas (Azure SQL Data Warehouse)].

## <a name="next-steps"></a>Nästa steg
När ditt SQL Data Warehouse är färdigetablerat, kan du testa att [läsa in exempeldata][läsa in exempeldata] eller kolla hur man [utvecklar][utvecklar], [läser in][läser in] eller [migrerar][migrerar].

Om du vill veta mer om hur du hanterar SQL Data Warehouse programmässigt, kan du kolla vår artikel om hur man använder [PowerShell-cmdletar och REST-API:er][PowerShell-cmdletar och REST-API:er].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrerar]: ./sql-data-warehouse-overview-migrate.md
[utvecklar]: ./sql-data-warehouse-overview-develop.md
[läser in]: ./sql-data-warehouse-load-with-bcp.md
[läsa in exempeldata]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell-cmdletar och REST-API:er]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[brandväggsregler]: ../sql-database-configure-firewall-settings.md

[Hur du installerar och konfigurerar Azure PowerShell]: ../powershell-install-configure.md
[så här skapar du ett SQL Data Warehouse från Azure-portalen]: ./sql-data-warehouse-get-started-provision.md
[Skapa en logisk Azure SQL Database-server med Azure-portalen]: ../sql-database/sql-database-get-started.md#create-logical-server-bk
[Skapa en logisk Azure SQL Database-server med PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[hur du kan skapa en resursgrupp]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Skapa databas (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft installationsprogram för webbplattform]: https://aka.ms/webpi-azps
[Priser för SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Kostnadsfri utvärderingsversion av Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure-krediter]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=Nov16_HO2-->


