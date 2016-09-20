<properties
   pageTitle="Skapa ett SQL Data Warehouse med hjälp av PowerShell | Microsoft Azure"
   description="Skapa ett SQL Data Warehouse med hjälp av PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Skapa ett SQL Data Warehouse med hjälp av PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

I den här artikeln visas hur du skapar ett SQL Data Warehouse med PowerShell.

## Krav

Du behöver följande för att komma igång:

- **Azure-konto**: Gå till [Kostnadsfri utvärderingsversion av Azure][] eller [MSDN Azure-krediter][] för att skapa ett konto.
- **Azure SQL Server**: Se [Skapa en logisk Azure SQL Database-server med Azure Portal][] eller [Skapa en logisk Azure SQL Database-server med PowerShell][] för mer information.
- **Resursgrupp**: Använd antingen samma resursgrupp som din Azure SQL-server eller se [hur du kan skapa en resursgrupp][].
- **PowerShell version 1.0.3 eller senare**: Du kan kontrollera din version genom att köra **Get-Module -ListAvailable -Name Azure**.  Den senaste versionen kan installeras från [Installationsprogram för Microsoft-webbplattform][].  För mer information om hur man installerar den senaste versionen finns i [Så här installerar och konfigurerar du Azure PowerShell][].

> [AZURE.NOTE] Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Se [Priser för SQL Data Warehouse][] för mer information om priser.

## Skapa ett SQL Data Warehouse

1. Öppna Windows PowerShell.
2. Kör den här cmdleten för att logga in på Azure Resource Manager.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Välj den prenumeration som du vill använda för din session.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Skapa databas. Det här exemplet skapas en databas med namnet ”mynewsqldw” med tjänstmål-nivån ”DW400” till servern ”sqldwserver1” som finns i resursgruppen med namnet ”mywesteuroperesgp1”.

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Erfordrade parametrar är:

- **RequestedServiceObjectiveName**: Antalet [DWU][] som du begär.  Värden som stöds är: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 och DW6000.
- **DatabaseName**: Namnet på det SQL Data Warehouse som du skapar.
- **ServerName**: namnet på den server som du använder för att skapa (måste vara V12).
- **ResourceGroupName**: Resursgruppen som du använder dig av.  Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
- **Version**: Måste vara ”DataWarehouse” för att skapa ett SQL Data Warehouse.

Valfria parametrar är:

- **CollationName**: Standardsortering om inte annat anges är SQL_Latin1_General_CP1_CI_AS.  Sorteringen kan inte ändras för en databas.
- **MaxSizeBytes**: Maximal standardstorlek för en databas är 10 GB.


Mer information om parameteralternativen finns i [New-AzureRmSqlDatabase][] och [Skapa databas (Azure SQL Data Warehouse)][].

## Nästa steg

När ditt SQL Data Warehouse är färdigetablerat, kan du testa att [läsa in exempeldata][] eller kolla hur man [utvecklar][], [läser in][] eller [migrerar][].

Om du vill veta mer om hur du hanterar SQL Data Warehouse programmässigt, kan du kolla vår artikel om hur man använder [PowerShell-cmdletar och REST-API:er][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrerar]: ./sql-data-warehouse-overview-migrate.md
[utvecklar]: ./sql-data-warehouse-overview-develop.md
[läser in]: ./sql-data-warehouse-load-with-bcp.md
[läsa in exempeldata]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell-cmdletar och REST-API:er]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[brandväggsregler]: ../sql-database-configure-firewall-settings.md

[Så här installerar och konfigurerar du Azure PowerShell]: ../powershell/powershell-install-configure.md
[så här skapar du ett SQL Data Warehouse från Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Skapa en logisk Azure SQL Database-server med Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Skapa en logisk Azure SQL Database-server med PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[hur du kan skapa en resursgrupp]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Skapa databas (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Installationsprogram för Microsoft-webbplattform]: https://aka.ms/webpi-azps
[Priser för SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Kostnadsfri utvärderingsversion av Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure-krediter]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F



<!--HONumber=sep16_HO1-->


