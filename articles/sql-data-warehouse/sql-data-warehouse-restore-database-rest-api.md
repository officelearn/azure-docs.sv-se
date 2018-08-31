---
title: Återställa en Azure SQL Data Warehouse – REST API | Microsoft Docs
description: 'Återställa en Azure SQL Data Warehouse med hjälp av REST API: er.'
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 71236afd3f72497887f42667f971539ed294bef1
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307544"
---
# <a name="restore-an-azure-sql-data-warehouse-with-rest-apis"></a>Återställa en Azure SQL Data Warehouse med REST API: er
Återställa en Azure SQL Data Warehouse med hjälp av REST API: er.

## <a name="before-you-begin"></a>Innan du börjar
**Kontrollera din DTU-kapacitet.** Varje SQL Data Warehouse är värd för en logisk SQLServer (t.ex. myserver.database.windows.net) standardvärdet [DTU-kvot](../sql-database/sql-database-what-is-a-dtu.md).  Innan du kan återställa en SQL Data Warehouse, kontrollerar du att den SQLServer har tillräckligt med återstående DTU-kvot för den databas som återställs. Om du vill begära mer DTU, kan du [skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-an-active-or-paused-data-warehouse"></a>Återställ ett informationslager som är aktiva eller pausad
Att återställa ett informationslager:

1. Hämta listan över databasens återställningspunkter med hjälp av databasens återställningspunkter för Get-åtgärd.
2. Påbörja din återställningen med hjälp av den [skapa databasen restore-förfrågan](https://msdn.microsoft.com/library/azure/dn509571.aspx) igen.
3. Spåra status för återställningen med hjälp av den [databasen Åtgärdsstatus](https://msdn.microsoft.com/library/azure/dn720371.aspx) igen.

> [!NOTE]
> När återställningen har slutförts kan du konfigurera ditt återställda data warehouse genom att följa [konfigurera databasen efter återställningen](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="restore-a-deleted-data-warehouse"></a>Återställa ett borttaget informationslager
Att återställa ett borttaget informationslager:

1. Lista alla distributionslager återställningsbara borttagna data med hjälp av den [lista återställningsbara släppas databaser](https://msdn.microsoft.com/library/azure/dn509562.aspx) igen.
2. Hämta information för Borttaget informationslager som du vill återställa med hjälp av [Get återställningsbara utelämnade databas] [Get återställningsbara utelämnade databas] igen.
3. Påbörja din återställningen med hjälp av den [skapa databasen restore-förfrågan](https://msdn.microsoft.com/library/azure/dn509571.aspx) igen.
4. Spåra status för återställningen med hjälp av den [databasen Åtgärdsstatus](https://msdn.microsoft.com/library/azure/dn720371.aspx) igen.

> [!NOTE]
> För att konfigurera ditt informationslager när återställningen har slutförts, se [konfigurera databasen efter återställningen](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).
> 
> 

## <a name="next-steps"></a>Nästa steg
Läs om funktionerna för affärskontinuitet för Azure SQL Database-versionerna, den [översikt över Azure SQL Database affärskontinuitet](../sql-database/sql-database-business-continuity.md).
