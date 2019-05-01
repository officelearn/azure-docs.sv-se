---
title: Store Azure SQL Database-säkerhetskopior i upp till 10 år | Microsoft Docs
description: Lär dig hur Azure SQL Database stöder lagring fullständiga databassäkerhetskopieringar för upp till 10 år.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/23/2019
ms.openlocfilehash: 0f764ebbad53185f46c7166011e05493ed261d6a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696648"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Store Azure SQL Database-säkerhetskopior i upp till 10 år

Många program har föreskrifter, efterlevnad och andra affärsrelaterade syften som kräver att du behåller säkerhetskopior av databasen efter de 7-35 dagar som tillhandahålls av Azure SQL Database [automatiska säkerhetskopieringar](sql-database-automated-backups.md). Med hjälp av funktionen för långsiktig kvarhållning av säkerhetskopior (LTR), kan du lagra angiven SQL database fullständiga säkerhetskopieringar i [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) blob-lagring för upp till 10 år. Alla säkerhetskopior kan återställas till en ny databas.

> [!NOTE]
> LTR kan aktiveras för enkel och delade databaser. Det är ännu inte tillgängliga för instans-databaser i hanterade instanser. Du kan använda SQL Agent-jobb för att schemalägga [endast kopiering databassäkerhetskopior](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) som ett alternativ till LTR utöver 35 dagar.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Hur fungerar SQL-databas med långsiktig kvarhållning

Långsiktig kvarhållning av säkerhetskopior (LTR) utnyttjar de fullständiga databassäkerhetskopieringar som är [automatiskt skapade](sql-database-automated-backups.md) att aktivera återställning av tid för återställningspunkt (PITR). Om en LTR-princip har ställts kopieras säkerhetskopiorna till olika BLOB för långsiktig lagring. Kopieringsåtgärden är ett bakgrundsjobb som inte påverkar prestanda på databas-arbetsbelastning. LTR-säkerhetskopior bevaras under en tidsperiod som angetts av en LTR-princip. LTR-princip för varje SQL-databas kan också ange hur ofta LTR-säkerhetskopieringar skapas. Så här aktiverar du den flexibiliteten du kan definiera principen med en kombination av fyra parametrar: kvarhållningsperiod för veckovisa säkerhetskopior (W) kvarhållningsperiod för månadsvisa säkerhetskopior (M), kvarhållningsperiod för årliga säkerhetskopior (Y) och veckan på året (WeekOfYear). Om du anger W, en säkerhetskopiering varje vecka ska kopieras till långsiktig lagring. Om du anger M, kopieras en säkerhetskopiering under den första veckan varje månad till långsiktig lagring. Om du anger Y, kopieras en säkerhetskopiering under veckan som anges av WeekOfYear till långsiktig lagring. Varje säkerhetskopia sparas i långsiktig lagring för den tid som anges av dessa parametrar. Ändringar av LTR-principen gäller för framtida säkerhetskopieringar. Till exempel om den angivna WeekOfYear har passerat när principen är konfigurerad, skapas den första säkerhetskopian för LTR nästa år. 

Exempel på LTR-princip:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Den tredje fullständig säkerhetskopieringen varje år sparas i fem år.
   
- W=0, M=3, Y=0

   Den första fullständiga säkerhetskopian för varje månad sparas under tre månader.

- W=12, M=0, Y=0

   Varje fullständig säkerhetskopiering varje vecka är kvar i 12 veckor.

- W = 6, M = 12, Y = 10 WeekOfYear = 16

   Varje fullständig säkerhetskopiering varje vecka är kvar i sex veckor. Förutom första fullständig säkerhetskopiering varje månad, som är kvar i 12 månader. Förutom den fullständiga säkerhetskopior som gjorts på 16: e vecka på året, som är kvar i 10 år. 

I följande tabell visar takt och förfallodatum för långsiktig säkerhetskopiering för följande princip:

W = 12 veckor (84 dagar), M = 12 månader (365 dagar), Y = 10 år (3650 dagar), WeekOfYear = 15 (veckan efter den 15 April)

   ![LTR-exempel](./media/sql-database-long-term-retention/ltr-example.png)



Om du ändrar principen ovan och W = 0 (ingen veckovisa säkerhetskopior), intervall som uppsättning säkerhetskopior kommer att ändras som visas i tabellen ovan efter de markerade datum. Lagringsutrymmet som krävs för att dessa säkerhetskopior skulle minska därefter. 

> [!NOTE]
> Tidsinställningen för de enskilda LTR-säkerhetskopiorna styrs av Azure SQL Database. Du kan inte manuellt skapa en säkerhetskopia av LTR eller styra tidsinställningen för säkerhetskopian skapades.
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>GEO-replikering och långsiktig kvarhållning av säkerhetskopior

Om du använder aktiv geo-replikering eller redundansgrupper som kontinuitet för företag-lösning bör du förbereder för slutlig redundansväxlingar och konfigurera samma LTR-princip för geo-secondary-databas. Dina kostnader för lagring av LTR ökar vanligtvis inte eftersom säkerhetskopieringar inte genereras från de sekundära databaser. Först när sekundärt primära kommer säkerhetskopieringar att skapas. Det garanterar inte avbryts generering av LTR-säkerhetskopior när redundansen utlöses och primärt flyttar till den sekundära regionen. 

> [!NOTE]
> När den ursprungliga primära databasen återställs från ett avbrott som orsakade växling vid fel, blir det en ny sekundär. Därför kommer inte att återuppta säkerhetskopian skapades och den befintliga principen för LTR börjar inte gälla förrän den blir primärt igen. 

## <a name="configure-long-term-backup-retention"></a>Konfigurera långsiktig kvarhållning av säkerhetskopior

Information om hur du konfigurerar långsiktig kvarhållning av säkerhetskopior med Azure portal eller PowerShell finns i [hantera Azure SQL Database långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Återställa databasen från LTR backup

Om du vill återställa en databas från LTR-lagring, kan du välja en specifik säkerhetskopia baserat på dess tidsstämpel. Databasen kan återställas till en befintlig server i samma prenumeration som den ursprungliga databasen. Om du vill lära dig att återställa databasen från en LTR-säkerhetskopia med hjälp av Azure portal eller PowerShell, se [hantera Azure SQL Database långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Nästa steg

Eftersom databassäkerhetskopior skydda data från oavsiktliga skador eller tas bort, är det en viktig del av alla företagskontinuitet och strategi för katastrofåterställning. Läs mer om de andra SQL-databas lösningarna för affärskontinuitet, i [översikt över affärskontinuitet](sql-database-business-continuity.md).
