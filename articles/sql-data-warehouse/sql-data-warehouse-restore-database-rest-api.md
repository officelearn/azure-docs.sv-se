---
title: Återställa en Azure SQL Data Warehouse - REST API | Microsoft Docs
description: 'Återställa en Azure SQL Data Warehouse med hjälp av REST API: er.'
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 2e1874fdf7c11d98d369072739c5937caffe6e96
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Återställa en Azure SQL Data Warehouse med REST API: er
Återställa en Azure SQL Data Warehouse med hjälp av REST API: er.

## <a name="before-you-begin"></a>Innan du börjar
**Kontrollera DTU-kapacitet.** Varje SQL Data Warehouse finns på en logisk SQLServer (t.ex. myserver.database.windows.net) som har ett standardvärde [DTU-kvot](../sql-database/sql-database-what-is-a-dtu.md).  Innan du kan återställa en SQL Data Warehouse, kontrollera att den SQL-servern har tillräckligt med återstående DTU-kvot för databasen som återställs. Om du vill begära mer DTU, kan du [skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Återställa en aktiv eller pausat datalagret
Att återställa ett datalager:

1. Hämta listan över återställningspunkter i databasen med hjälp av återställningspunkter för Get-databasen igen.
2. Börja din återställning med hjälp av den [skapandebegäran om återställning av databas](https://msdn.microsoft.com/library/azure/dn509571.aspx) igen.
3. Spåra statusen för återställningspunkten med hjälp av den [databasen Åtgärdsstatus](https://msdn.microsoft.com/library/azure/dn720371.aspx) igen.

> [!NOTE]
> När återställningen har slutförts kan du konfigurera ditt återställda data warehouse genom att följa [konfigurera databasen efter återställningen](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Återställa en borttagen datalagret
Att återställa borttagna datalager:

1. Lista över alla distributionslager återställningsbara borttagna data med hjälp av den [lista återställningsbara bort databaser](https://msdn.microsoft.com/library/azure/dn509562.aspx) igen.
2. Hämta information för borttagna datalagret som du vill återställa med hjälp av [Get återställningsbara borttagna] [Get återställningsbara borttagna databasen] Databasåtgärden.
3. Börja din återställning med hjälp av den [skapandebegäran om återställning av databas](https://msdn.microsoft.com/library/azure/dn509571.aspx) igen.
4. Spåra statusen för återställningspunkten med hjälp av den [databasen Åtgärdsstatus](https://msdn.microsoft.com/library/azure/dn720371.aspx) igen.

> [!NOTE]
> Om du vill konfigurera ditt data warehouse när återställningen har slutförts, se [konfigurera databasen efter återställningen](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna för verksamhetskontinuitet Azure SQL Database version, Läs den [översikt över verksamhetskontinuitet i Azure SQL Database](../sql-database/sql-database-business-continuity.md).
