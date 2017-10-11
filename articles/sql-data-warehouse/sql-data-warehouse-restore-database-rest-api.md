---
title: "Återställa en Azure SQL Data Warehouse (REST-API) | Microsoft Docs"
description: "REST API-uppgifterna för att återställa en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: fca922c6-b675-49c7-907e-5dcf26d451dd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
ms.openlocfilehash: 8656607611e7518e42b51b91774f55abec15c228
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Återställa en Azure SQL Data Warehouse (REST-API)
> [!div class="op_single_selector"]
> * [Översikt över][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

I den här artikeln får du lära dig hur du återställer en Azure SQL Data Warehouse med hjälp av REST-API.

## <a name="before-you-begin"></a>Innan du börjar
**Kontrollera DTU-kapacitet.** Varje SQL Data Warehouse finns på en SQLServer (t.ex. myserver.database.windows.net) som har en standard DTU-kvot.  Innan du kan återställa en SQL Data Warehouse, kontrollera att den SQL-servern har tillräckligt med återstående DTU-kvot för databasen som återställs. Information om hur du beräkna DTU behövs eller begära mer DTU finns [begär en ändring för DTU-kvot][Request a DTU quota change].

## <a name="restore-an-active-or-paused-database"></a>Återställa en databas för aktiva eller pausats
Att återställa en databas:

1. Hämta listan över återställningspunkter i databasen med hjälp av återställningspunkter för Get-databasen igen.
2. Börja din återställning med hjälp av den [skapandebegäran om återställning av databas] [ Create database restore request] igen.
3. Spåra statusen för återställningspunkten med hjälp av den [databasen Åtgärdsstatus] [ Database operation status] igen.

> [!NOTE]
> När återställningen har slutförts kan du konfigurera din återställda databas genom att följa [konfigurera databasen efter återställningen][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Återställa en borttagen databas
Att återställa en borttagen databas:

1. Lista över alla dina återställningsbara borttagna databaser med hjälp av den [lista återställningsbara bort databaser] [ List restorable dropped databases] igen.
2. Hämta information för den borttagna databasen som du vill återställa med hjälp av den [Get återställningsbara släppa databasen] [ Get restorable dropped database] igen.
3. Börja din återställning med hjälp av den [skapandebegäran om återställning av databas] [ Create database restore request] igen.
4. Spåra statusen för återställningspunkten med hjälp av den [databasen Åtgärdsstatus] [ Database operation status] igen.

> [!NOTE]
> För att konfigurera databasen efter återställningen har slutförts, se [konfigurera databasen efter återställningen][Configure your database after recovery].
> 
> 

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna för verksamhetskontinuitet Azure SQL Database version, Läs den [översikt över verksamhetskontinuitet i Azure SQL Database][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Create database restore request]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Database operation status]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Get restorable dropped database]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[List restorable dropped databases]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
