---
title: Kända problem och begränsningar med online-migreringar till Azure SQL Database Hanterad instans
description: Läs om kända problem/migrerings begränsningar som är associerade med online-migreringar till Azure SQL Database Hanterad instans.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77648673"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Kända problem/migrerings begränsningar med online-migreringar till Azure SQL Database Hanterad instans

Kända problem och begränsningar som är associerade med online-migrering från SQL Server till Azure SQL Database Hanterad instans beskrivs nedan.

> [!IMPORTANT]
> Migrering av SQL_variant data typer stöds inte för migrering av SQL Server Azure SQL Database.

## <a name="backup-requirements"></a>Säkerhets kopierings krav

- **Säkerhets kopior med kontroll Summa**

    Azure Database Migration Service använder säkerhets kopierings-och återställnings metoden för att migrera dina lokala databaser till SQL Database Hanterad instans. Azure Database Migration Service stöder endast säkerhets kopieringar som skapats med kontroll summa.

    [Aktivera eller inaktivera kontroll summor för säkerhets kopiering under säkerhets kopiering eller återställning (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Om du tar databas säkerhets kopior med komprimering, är kontroll summan ett standard beteende om det inte uttryckligen inaktive ras.

    Om du väljer att låta Azure Database Migration Service tas med offline-migrering, och om du väljer **att låta...**, kommer Azure Database migration service att ta säkerhets kopian av databasen med alternativet kontroll Summa aktiverat.

- **Säkerhetskopieringsmedia**

    Var noga med att ta varje säkerhets kopia på ett separat säkerhets kopierings medium (säkerhetskopieringsfiler). Azure Database Migration Service stöder inte säkerhets kopieringar som läggs till i en enda säkerhets kopia. Ta fullständig säkerhets kopiering och logg säkerhets kopior för att separera säkerhetskopieringsfiler.

## <a name="data-and-log-file-layout"></a>Layout för data och logg fil

- **Antal loggfiler**

    Azure Database Migration Service stöder inte databaser med flera loggfiler. Om du har flera loggfiler kan du krympa och organisera om dem i en enda transaktions logg fil. Eftersom du inte kan fjärrans luta till loggfiler som inte är tomma måste du säkerhetskopiera logg filen först.

## <a name="sql-server-features"></a>SQL Server funktioner

- **FileStream/FileTable**

    SQL Database hanterade instansen stöder för närvarande inte FileStream och FileTable. För arbets belastningar som är beroende av dessa funktioner rekommenderar vi att du väljer SQL-servrar som körs på virtuella Azure-datorer som Azure-mål.

- **InMemory-tabeller**

    InMemory OLTP är tillgängligt i Premium-och Affärskritisk-nivåerna för SQL Database Hanterad instans. Generell användning nivån har inte stöd för minnes intern OLTP.

## <a name="migration-resets"></a>Återställer migrering

- **Distributioner**

    SQL Database Hanterad instans är en PaaS-tjänst med automatisk uppdatering och versions uppdateringar. Vid migreringen av SQL Database hanterade instansen kan icke-kritiska uppdateringar hjälpa upp till 36 timmar. Efteråt (och för kritiska uppdateringar) återställs processen till ett fullständigt återställnings tillstånd om migreringen avbryts.

    Migrerings-start punkt kan bara anropas efter att den fullständiga säkerhets kopieringen har återställts och fångats upp med alla logg säkerhets kopior. Om migrering för produktion påverkas kan du kontakta [aliaset för Azure DMS-feedback](mailto:dmsfeedback@microsoft.com).
