<properties
   pageTitle="Skapa ett SQL Data Warehouse i Azure Portal | Microsoft Azure"
   description="Lär dig hur du skapar ett Azure SQL Data Warehouse i Azure Portal"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Skapa ett Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

I de här självstudierna kommer du att använda Azure Portal för att skapa ett SQL Data Warehouse som innehåller en AdventureWorksDW-exempeldatabas.


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]


1. Logga in på [Azure Portal](https://portal.azure.com).

2. Klicka på **+ Ny** > **Data + lagring** > **SQL Data Warehouse**.

    ![Skapa](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. I bladet **SQL Data Warehouse**, anger du informationen som behövs och trycker på Skapa för att skapa.

    ![Skapa databas](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Server**: Vi rekommenderar att du först väljer server.  Du kan välja en befintlig server eller [skapa en ny](./sql-data-warehouse-get-started-new-server.md). 

    - **Databasnamn**: Namnet som kommer användas för att referera till ditt SQL Data Warehouse.  Det måste vara unikt för servern.
    
    - **Prestanda**: Vi rekommenderar att börja med 400 DWU:er. Du kan flytta skjutreglaget till vänster eller höger för att justera prestanda för ditt informationslager, eller skala upp eller ner efter det har skapats.  Läs mer om DWU:er i vår dokumentation om [skalning](./sql-data-warehouse-manage-compute-overview.md) eller på vår [prissida](https://azure.microsoft.com/en-us/pricing/details/sql-data-warehouse/). 

    - **Prenumeration**: Välj den prenumeration som det här SQL Data Warehouse kommer faktureras till.

    - **Resursgrupp**: Resursgrupper är behållare som hjälper dig att hantera en samling Azure-resurser. Läs mer om [resursgrupper](../azure-portal/resource-group-portal.md).

    - **Välj källa**: Klicka på **Välj källa** > **Exempel**. Eftersom det bara finns en exempeldatabas just nu, kommer Azure att automatiskt fylla i **Välj exempel**-alternativet med AdventureWorksDW när du väljer Exempel.

4. Klicka på **Skapa**, för att skapa ditt SQL Data Warehouse.

5. Vänta några minuter så är ditt SQL Data Warehouse klart. När det är klart, ska du komma tillbaka till [Azure-portalen](https://portal.azure.com). Du hittar ditt SQL Data Warehouse på instrumentpanelen, listat under dina SQL-databaser, eller i den resursgrupp som du skapade den i. 

    ![Portal-vy](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL DataBase create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## Nästa steg

Nu när du har skapat ett SQL Data Warehouse, är du redo att [Ansluta](./sql-data-warehouse-get-started-connect.md) och börja ställa frågor.

Se [översikt över inläsning](./sql-data-warehouse-overview-load.md) för att läsa in data i SQL Data Warehouse.

Om du försöker migrera en befintlig databas till SQL Data Warehouse, kan du se [Migreringsöversikt](./sql-data-warehouse-overview-migrate.md) eller använda dig av [Migreringsverktyget](./sql-data-warehouse-migrate-migration-utility.md).




<!--HONumber=Jun16_HO2-->


