---
title: Skapa ett SQL Data Warehouse i Azure Portal | Microsoft Docs
description: "Lär dig hur du skapar ett Azure SQL Data Warehouse i Azure Portal"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cf6e81d6a74c4a5beff91a3df90780a83bf1327


---
# <a name="create-an-azure-sql-data-warehouse"></a>Skapa ett Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Azure-portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
> 
> 

I den här självstudien kommer du att använda Azure Portal för att skapa ett SQL Data Warehouse som innehåller en AdventureWorksDW-exempeldatabas.

## <a name="prerequisites"></a>Krav
Du behöver följande för att komma igång:

* **Azure-konto**: Gå till [Kostnadsfri utvärderingsversion av Azure][Kostnadsfri utvärderingsversion av Azure] eller [MSDN Azure-krediter][MSDN Azure-krediter] för att skapa ett konto.
* **Azure SQL Server**: Se [Skapa en logisk Azure SQL Database-server med Azure-portalen][Skapa en logisk Azure SQL Database-server med Azure-portalen] för mer information.

> [!NOTE]
> Att skapa ett SQL Data Warehouse kan resultera i en ny fakturerbar tjänst.  Se [Priser för SQL Data Warehouse][Priser för SQL Data Warehouse] för mer information.
> 
> 

## <a name="create-a-sql-data-warehouse"></a>Skapa ett SQL Data Warehouse
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **+ Ny** > **Data + lagring** > **SQL Data Warehouse**.
   
    ![Skapa](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. I bladet **SQL Data Warehouse**, anger du informationen som behövs och trycker på Skapa för att skapa.
   
    ![Skapa databas](./media/sql-data-warehouse-get-started-provision/create-database.png)
   
   * **Server**: Vi rekommenderar att du först väljer server.  
   * **Databasnamn**: Namnet som används för att referera till ditt SQL Data Warehouse.  Det måste vara unikt för servern.
   * **Prestanda**: Vi rekommenderar att börja med 400 [DWU:er][DWU]. Du kan flytta skjutreglaget till vänster eller höger för att justera prestanda för ditt informationslager, eller skala upp eller ner efter det har skapats.  Läs mer om DWU:er i vår dokumentation om [skalning](sql-data-warehouse-manage-compute-overview.md) eller på vår [prissida][Priser för SQL Data Warehouse]. 
   * **Prenumeration**: Välj den [prenumeration] som detta SQL Data Warehouse kommer faktureras till.
   * **Resursgrupp**: [Resursgrupper][Resursgrupp] är behållare som hjälper dig att hantera en samling Azure-resurser. Läs mer om [resursgrupper](../azure-resource-manager/resource-group-overview.md).
   * **Välj källa**: Klicka på **Välj källa** > **Exempel**. Azure fyller automatiskt i alternativet **Välj exempel** med AdventureWorksDW.

> [!NOTE]
> Standardsortering för ett SQL Data Warehouse är SQL_Latin1_General_CP1_CI_AS. Om en annan sortering behövs kan [T-SQL][T-SQL] användas för att skapa databasen med en annan sortering.
> 
> 

1. Klicka på **Skapa**, för att skapa ditt SQL Data Warehouse.
2. Vänta några minuter. När datalagret är klart bör du komma tillbaka till [Azure Portal](https://portal.azure.com). Du hittar ditt SQL Data Warehouse på instrumentpanelen, listat under dina SQL-databaser, eller i den resursgrupp som du skapade den i. 
   
    ![Portal-vy](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat ett SQL Data Warehouse är du redo att [ansluta](sql-data-warehouse-connect-overview.md) och börja ställa frågor.

Se [översikt över inläsning](sql-data-warehouse-overview-load.md) för att läsa in data i SQL Data Warehouse.

Om du försöker migrera en befintlig databas till SQL Data Warehouse, kan du se [Migreringsöversikt](sql-data-warehouse-overview-migrate.md) eller använda dig av [Migreringsverktyget](sql-data-warehouse-migrate-migration-utility.md).

Brandväggsregler kan också konfigureras med hjälp av Transact-SQL. Mer information finns i [sp_set_firewall_rule][sp_set_firewall_rule] and [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

Det är en bra idé att titta på [Bästa praxis][Bästa praxis].

<!--Article references-->
[Skapa en logisk Azure SQL Database-server med Azure Portal]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Skapa en logisk Azure SQL Database-server med PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resursgrupper]: ../resource-group-template-deploy-portal.md
[Bästa praxis]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[prenumeration]: ../azure-glossary-cloud-terminology.md#subscription
[resursgrupp]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[Priser för SQL Data Warehouse]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Kostnadsfri utvärderingsversion av Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure-krediter]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F




<!--HONumber=Nov16_HO2-->


