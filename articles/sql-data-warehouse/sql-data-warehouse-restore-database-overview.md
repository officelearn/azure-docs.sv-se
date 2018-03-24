---
title: Återställa ett Azure data warehouse - lokala och geo-redundant | Microsoft Docs
description: Översikt över alternativ för återställning av databasen för att återställa en databas i Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: ''
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: abf8f0b1005aec71812dc8ebfd12fe65250d7a0e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="sql-data-warehouse-restore"></a>SQL Data Warehouse-återställning
> [!div class="op_single_selector"]
> * [Översikt över][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

SQL Data Warehouse har både lokala och geografiska återställningar som en del av dess data warehouse disaster recovery-funktioner. Använd data warehouse säkerhetskopieringar för att återställa ditt datalager till en återställningspunkt i den primära regionen, eller geo-redundant säkerhetskopieringar för att återställa till en annan geografisk region. Den här artikeln förklarar specifika återställer data warehouse.

## <a name="what-is-a-data-warehouse-restore"></a>Vad är en återställning av data warehouse?
En återställning av data warehouse är ett nytt datalager som har skapats från en säkerhetskopia av en befintlig eller borttagna data warehouse. Återställda data warehouse återskapar säkerhetskopierade data warehouse vid en viss tid. Eftersom SQL Data Warehouse är ett distribuerat system, skapas en återställning av data warehouse från många säkerhetskopior som lagras i Azure BLOB. 

Återställning av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom den återskapar dina data efter oavsiktliga skador eller tas bort.

Mer information finns i:

* [SQL Data Warehouse-säkerhetskopieringar](sql-data-warehouse-backups.md)
* [Översikt över verksamhetskontinuitet](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Återställningspunkter för data warehouse
SQL Data Warehouse använder Azure Storage Blob ögonblicksbilder för att säkerhetskopiera primära datalagret som en fördel med att använda Azure Premium-lagring. Varje ögonblicksbild har en återställningspunkt som representerar den tid som ögonblicksbilden igång. Om du vill återställa ett datalager, Välj en återställningspunkt och utfärda ett kommando för återställning.  

SQL Data Warehouse återställs alltid säkerhetskopian till ett nytt datalager. Du kan hålla återställda data warehouse och det aktuella eller ta bort en av dem. Om du vill ersätta det aktuella datalagret med återställda data warehouse kan du byta namn på den.

Om du behöver återställa borttagna eller pausas datalager kan du [skapa ett supportärende](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>GEO-redundant återställning
Du kan återställa ditt datalager till en region som stöder Azure SQL Data Warehouse på din valda prestandanivå. Observera att 9000 och 18000 DWU inte stöds i alla regioner under förhandsgranskningen.

> [!NOTE]
> Om du vill utföra en återställning av geo-redundant måste du inte har valt utanför den här funktionen.
> 
> 

## <a name="restore-timeline"></a>Återställa tidslinjen
Du kan återställa en databas till vilken tillgänglig återställningspunkt under de senaste sju dagarna. Ögonblicksbilder starta alla fyra till åtta timmar och är tillgängliga i sju dagar. När en ögonblicksbild är äldre än sju dagar, den upphör att gälla och dess återställningspunkten är inte längre tillgänglig.

## <a name="restore-costs"></a>Återställa kostnader
Storage-tillägget för återställda data warehouse faktureras till Azure Premium Storage-sats. 

Om du pausar ett återställda data warehouse, debiteras du för lagring till Azure Premium Storage-sats. Fördelen med att pausa är inte debiteras du för DWU datorresurser.

Mer information om priser för SQL Data Warehouse finns [priser för SQL Data Warehouse](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Används för återställning
Den primära data warehouse återställning används för att återställa data efter oavsiktlig dataförlust eller skadade data.

Du kan också använda data warehouse återställning för att behålla en säkerhetskopia för längre än sju dagar. När säkerhetskopieringen har återställts har datalagret online och kan pausa under obestämd tid för att spara kostnader för beräkning. Pausad databas debiteras lagring till Azure Premium Storage-sats. 

## <a name="related-topics"></a>Relaterade ämnen
### <a name="scenarios"></a>Scenarier
* En översikt över verksamhetskontinuitet, se [översikt över verksamhetskontinuitet](../sql-database/sql-database-business-continuity.md)

<!-- ### Tasks -->

Återställ om du vill utföra en återställning av data warehouse med hjälp av:

* Azure portal, se [återställa ett data warehouse med Azure-portalen](sql-data-warehouse-restore-database-portal.md)
* PowerShell-cmdlets finns [återställa ett data warehouse med hjälp av PowerShell-cmdlets](sql-data-warehouse-restore-database-powershell.md)
* REST-API: er, se [återställa ett data warehouse med hjälp av REST-API: er](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
