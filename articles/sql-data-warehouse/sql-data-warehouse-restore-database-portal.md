---
title: "Återställa Azure SQL Data Warehouse (Azure portal) | Microsoft Docs"
description: "Azure portal uppgifter för att återställa Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 6be187784a68174b63f7fc068c772826a0db52c6
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
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
**Kontrollera DTU-kapacitet.** Varje instans av SQL Data Warehouse finns i en SQL-server (till exempel myserver.database.windows.net) som har en kvot som standard data genomströmning enhet (DTU). Innan du kan återställa SQL Data Warehouse, kan du kontrollera att SQLServer har tillräckligt återstående DTU-kvot för den databas som du återställa. Om du vill lära dig att beräkna DTU-kvot eller begära mer dtu: er, se [begär en ändring för DTU-kvot][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Återställa en databas för aktiva eller pausats
Att återställa en databas:

1. Logga in på [Azure Portal][Azure portal].
2. I den vänstra rutan, Välj **Bläddra**, och välj sedan **SQL-servrar**.

    ![Välj Bläddra > SQL-servrar](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Hitta din server och markerar den.

    ![Markera din server](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. Hitta instans av SQL Data Warehouse som du vill återställa och välj sedan den.

    ![Välj instansen av SQL Data Warehouse att återställa](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Längst upp i bladet Data Warehouse, Välj **återställa**.

    ![Välj återställning](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. Ange ett nytt **databasnamnet**.
7. Välj senast **återställningspunkt**.

   Se till att välja den senaste återställningspunkten. Eftersom återställningspunkter visas i Coordinated Universal Time (UTC), kanske inte den senaste återställningspunkten standardalternativet.

      ![Välj en återställningspunkt](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8. Välj **OK**.
9. Databasen återställningsprocessen startar och du kan använda **meddelanden** att övervaka processen.

> [!NOTE]
> När återställningen är klar kan du konfigurera din återställda databas genom att följa [konfigurera databasen efter återställningen][Configure your database after recovery].
>
>

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
Att återställa en borttagen databas:

1. Logga in på [Azure Portal][Azure portal].
2. I den vänstra rutan, Välj **Bläddra**, och välj sedan **SQL-servrar**.

    ![Välj Bläddra > SQL-servrar](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. Hitta din server och markerar den.

    ![Markera din server](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. Rulla ned till den **Operations** avsnitt på bladet för din server.
5. Välj den **bort databaser** panelen.

    ![Markera rutan borttagna databaser](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. Välj den borttagna databasen som du vill återställa.

    ![Välj en databas som ska återställas](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. Ange ett nytt **databasnamnet**.

    ![Lägga till ett namn för databasen](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8. Välj **OK**.
9. Databasen återställningsprocessen startar och du kan använda **meddelanden** att övervaka processen.

> [!NOTE]
> För att konfigurera databasen efter återställningen har slutförts, se [konfigurera databasen efter återställningen][Configure your database after recovery].
>
>

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna för verksamhetskontinuitet Azure SQL Database version, läsa den [översikt över verksamhetskontinuitet i Azure SQL Database][Azure SQL Database business continuity overview].

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
