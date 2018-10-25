---
title: Store Azure SQL Database-säkerhetskopior i upp till 10 år | Microsoft Docs
description: Lär dig hur Azure SQL Database stöder lagring fullständiga databassäkerhetskopieringar för upp till 10 år.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/24/2018
ms.openlocfilehash: 7fe34423e706054daf84eaa8baf45fe201a661c9
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026185"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Store Azure SQL Database-säkerhetskopior i upp till 10 år

Många program har föreskrifter, efterlevnad och andra affärsrelaterade syften som kräver att du behåller säkerhetskopior av databasen efter de 7-35 dagar som tillhandahålls av Azure SQL Database [automatiska säkerhetskopieringar](sql-database-automated-backups.md). Med hjälp av funktionen för långsiktig kvarhållning av säkerhetskopior (LTR), kan du lagra angiven SQL database fullständiga säkerhetskopieringar i [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) blob-lagring för upp till 10 år. Du kan sedan återställa alla säkerhetskopiering som en ny databas.

> [!NOTE]
> LTR kan aktiveras på de databaser som finns i Azure SQL Database logiska servrar. Det är inte ännu tillgängliga för databaser som finns i hanterade instanser.
> 

## <a name="how-sql-database-long-term-retention-works"></a>Hur fungerar SQL-databas med långsiktig kvarhållning

Långsiktig kvarhållning av säkerhetskopior (LTR) utnyttjar de fullständiga databassäkerhetskopieringar som är [automatiskt skapade](sql-database-automated-backups.md) att aktivera återställning av tid för återställningspunkt (PITR). Dessa säkerhetskopior kopieras till olika lagringsblobar om LTR-princip har konfigurerats.
Du kan konfigurera en LTR-princip för varje SQL-databas och ange hur ofta du behöver kopiera säkerhetskopieringar till långsiktig lagring-BLOB. Så här aktiverar du den flexibiliteten du kan definiera principen med en kombination av fyra parametrar: kvarhållningsperiod för veckovisa säkerhetskopior (W) kvarhållningsperiod för månadsvisa säkerhetskopior (M), kvarhållningsperiod för årliga säkerhetskopior (Y) och veckan på året (WeekOfYear). Om du anger W, en säkerhetskopiering varje vecka ska kopieras till långsiktig lagring. Om du anger M, kopieras en säkerhetskopiering under den första veckan varje månad till långsiktig lagring. Om du anger Y, kopieras en säkerhetskopiering under veckan som anges av WeekOfYear till långsiktig lagring. Varje säkerhetskopia sparas i långsiktig lagring för den tid som anges av dessa parametrar. 

Exempel:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   3 fullständig säkerhetskopiering varje år är kvar i fem år.
- W = 0, M = 3, Y = 0

   Den första fullständiga säkerhetskopian för varje månad sparas i tre månader.

- W = 12, M = 0, Y = 0

   Varje fullständig säkerhetskopiering varje vecka är kvar i 12 veckor.

- W = 6, M = 12, Y = 10 WeekOfYear = 16

   Varje fullständig säkerhetskopiering varje vecka är kvar i 6 veckor. Förutom första fullständig säkerhetskopiering varje månad, som är kvar i 12 månader. Förutom den fullständiga säkerhetskopior som gjorts på 16: e vecka på året, som är kvar i 10 år. 

I följande tabell visar takt och förfallodatum för långsiktig säkerhetskopiering för följande princip:

W = 12 veckor (84 dagar), M = 12 månader (365 dagar), Y = 10 år (3650 dagar), WeekOfYear = 15 (veckan efter den 15 April)

   ![LTR-exempel](./media/sql-database-long-term-retention/ltr-example.png)


 
Om du skulle ändra principen ovan och W = 0 (ingen veckovisa säkerhetskopior), intervall som uppsättning säkerhetskopior skulle ändras som visas i tabellen ovan efter de markerade datum. Lagringsutrymmet som krävs för att dessa säkerhetskopior skulle minska därefter. 

> [!NOTE]
1. LTR-kopior skapas av Azure storage-tjänsten så att kopieringen inte påverkar prestanda på den befintliga databasen.
2. Principen gäller för framtida säkerhetskopieringar. T.ex. Om den angivna WeekOfYear har passerat när principen är konfigurerad, skapas den första säkerhetskopian för LTR nästa år. 
3. Om du vill återställa en databas från LTR-lagring, kan du välja en specifik säkerhetskopia baserat på dess tidsstämpel.   Databasen kan återställas till en befintlig server i samma prenumeration som den ursprungliga databasen. 
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>GEO-replikering och långsiktig kvarhållning av säkerhetskopior

Om du använder aktiv geo-replikering eller växla över grupper som din lösning på kontinuitet för företag du förbereder för slutlig redundansväxlingar och konfigurera samma LTR-princip för geo-secondary-databas. Det här ökar inte dina kostnader för LTR-lagring som säkerhetskopieringar inte genereras från de sekundära databaser. Först när sekundärt primära kommer säkerhetskopieringar att skapas. Det här sättet som garanterar du icke avbryts generering av LTR-säkerhetskopior när redundansen utlöses och primärt flyttas till den sekundära regionen. 

> [!NOTE]
När den ursprungliga primära databasen återställs från avbrott som orsakar till redundans, blir det en ny sekundär. Därför kommer inte att återuppta säkerhetskopian skapades och den befintliga principen för LTR börjar inte gälla förrän den blir primärt igen. 
> 

## <a name="configure-long-term-backup-retention"></a>Konfigurera långsiktig kvarhållning av säkerhetskopior

Om du vill lära dig mer om att konfigurera långsiktig kvarhållning av säkerhetskopior med hjälp av Azure portal eller PowerShell, se [konfigurera långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Nästa steg

Eftersom databassäkerhetskopior skydda data från oavsiktliga skador eller tas bort, är det en viktig del av alla företagskontinuitet och strategi för katastrofåterställning. Läs mer om de andra SQL-databas lösningarna för affärskontinuitet, i [översikt över affärskontinuitet](sql-database-business-continuity.md).
