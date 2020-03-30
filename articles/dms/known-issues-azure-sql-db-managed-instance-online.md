---
title: Kända problem och begränsningar med onlinemigreringar till Azure SQL Database hanterad instans
description: Lär dig mer om kända problem/migreringsbegränsningar som är associerade med onlinemigreringar till Azure SQL Database-hanterad instans.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77648673"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Kända problem/migreringsbegränsningar med onlinemigreringar till Azure SQL Database-hanterad instans

Kända problem och begränsningar som är associerade med onlinemigreringar från SQL Server till Azure SQL Database-hanterad instans beskrivs nedan.

> [!IMPORTANT]
> Med onlinemigreringar av SQL Server till Azure SQL Database stöds inte migrering av SQL_variant datatyper.

## <a name="backup-requirements"></a>Krav för säkerhetskopiering

- **Säkerhetskopior med kontrollsumma**

    Azure Database Migration Service använder metoden för säkerhetskopiering och återställning för att migrera dina lokala databaser till SQL Database-hanterad instans. Azure Database Migration Service stöder endast säkerhetskopior som skapats med hjälp av kontrollsumma.

    [Aktivera eller inaktivera kontroller av säkerhetskopiering under säkerhetskopiering eller återställning (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Om du tar säkerhetskopiorna för databasen med komprimering är kontrollsumman ett standardbeteende om inte uttryckligen inaktiverats.

    Med offlinemigreringar, om du väljer **jag ska låta Azure Database Migration Service...**, sedan Azure Database Migration Service kommer att ta databasen backup med kontrollsumma alternativet aktiverat.

- **Medie för säkerhetskopiering**

    Se till att ta varje säkerhetskopia på en separat säkerhetskopieringsmedia (säkerhetskopior). Azure Database Migration Service stöder inte säkerhetskopior som läggs till i en enda säkerhetskopia. Ta full backup och logga säkerhetskopior för att separera säkerhetskopior.

## <a name="data-and-log-file-layout"></a>Data- och loggfilslayout

- **Antal loggfiler**

    Azure Database Migration Service stöder inte databaser med flera loggfiler. Om du har flera loggfiler förminsklarar och omorganiserar du dem till en enda transaktionsloggfil. Eftersom du inte kan fjärrstyra för att logga filer som inte är tomma måste du säkerhetskopiera loggfilen först.

## <a name="sql-server-features"></a>SQL Server-funktioner

- **FileStream/FileTables**

    SQL Database-hanterad instans stöder för närvarande inte FileStream och FileTables. För arbetsbelastningar som är beroende av dessa funktioner rekommenderar vi att du väljer SQL-servrar som körs på virtuella Azure-datorer som ditt Azure-mål.

- **Minnestabeller**

    OLTP i minnet är tillgängligt i nivåerna Premium och affärskritisk för sql Database-hanterad instans. Nivån för allmänt ändamål stöder inte OLTP i minnet.

## <a name="migration-resets"></a>Återställning av migrering

- **Distributioner**

    SQL Database hanterad instans är en PaaS-tjänst med automatisk korrigering och versionsuppdateringar. Under migrering av sql database-hanterade instansen är icke-kritiska uppdateringar hjälp upp till 36 timmar. Efteråt (och för viktiga uppdateringar), om migreringen störs, återställs processen till ett fullständigt återställningstillstånd.

    Migration cutover kan endast anropas efter att hela säkerhetskopian har återställts och hinner ikapp alla loggsäkerhetskopior. Om din produktionsmigreringsrover påverkas kontaktar du [Azure DMS Feedback alias](mailto:dmsfeedback@microsoft.com).
