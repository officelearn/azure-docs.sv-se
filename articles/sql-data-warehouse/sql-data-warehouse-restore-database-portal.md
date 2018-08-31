---
title: Återställa Azure SQL Data Warehouse (Azure portal) | Microsoft Docs
description: Azure portal uppgifter för att återställa Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 8752bc1977bac75928651a62576272ffc673ee7e
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43306308"
---
# <a name="restore-azure-sql-data-warehouse-portal"></a>Återställa Azure SQL Data Warehouse (portal)
> [!div class="op_single_selector"]
> * [Översikt över][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
>
>
I den här artikeln får lära du dig att återställa Azure SQL Data Warehouse med hjälp av Azure portal.

## <a name="before-you-begin"></a>Innan du börjar
**Kontrollera din DTU-kapacitet.** Varje instans av SQL Data Warehouse finns på en SQL-server (t.ex, myserver.database.windows.net) som har en kvot för standard data throughput unit (DTU). Innan du kan återställa SQL Data Warehouse, kan du kontrollera att SQLServer har tillräckligt återstående DTU-kvot för den databas som du återställa. Läs hur du beräkna DTU-kvot eller begär mer dtu: er i [begära en DTU-kvot ändring][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Återställ en databas med aktiv eller pausad
Du återställer en databas:

1. Logga in på [Azure Portal][Azure portal].
2. I den vänstra rutan väljer **Bläddra**, och välj sedan **SQL-servrar**.

    ![Välj Bläddra > SQL-servrar](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Hitta din server och välj den sedan.

    ![Välj din server](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Hitta den instansen av SQL Data Warehouse som du vill återställa från och välj den sedan.

    ![Välj instansen av SQL Data Warehouse för att återställa](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Högst upp på bladet informationslager, Välj **återställa**.

    ![Välj återställning](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Ange en ny **databasnamn**.
7. Välj senast **återställningspunkt**.

   Kontrollera att du väljer den senaste återställningspunkten. Eftersom återställningspunkter visas i Coordinated Universal Time (UTC), kanske inte den senaste återställningspunkten standardalternativet.

      ![Välj en återställningspunkt](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Välj **OK**.
9. Databasen återställningsprocessen startar och du kan använda **meddelanden** att övervaka processen.

> [!NOTE]
> När återställningen är klar kan du konfigurera din återställda databas genom att följa [konfigurera databasen efter återställningen][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
Så här återställer en borttagen databas:

1. Logga in på [Azure Portal][Azure portal].
2. I den vänstra rutan väljer **Bläddra**, och välj sedan **SQL-servrar**.

    ![Välj Bläddra > SQL-servrar](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Hitta din server och välj den sedan.

    ![Välj din server](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Rulla ned till den **Operations** avsnittet på bladet för din server.
5. Välj den **borttagna databaser** panelen.

    ![Välj panelen borttagna databaser](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Välj den borttagna databasen som du vill återställa.

    ![Välj en databas för att återställa](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Ange en ny **databasnamn**.

    ![Lägga till ett namn för databasen](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Välj **OK**.
9. Databasen återställningsprocessen startar och du kan använda **meddelanden** att övervaka processen.

> [!NOTE]
> För att konfigurera databasen efter återställningen har slutförts, se [konfigurera databasen efter återställningen][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna för affärskontinuitet för Azure SQL Database-versioner, läsa den [översikt över Azure SQL Database affärskontinuitet][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure portal]: https://portal.azure.com/
