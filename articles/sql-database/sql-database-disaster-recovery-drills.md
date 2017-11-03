---
title: "Haveriberedskap för SQL-databasen | Microsoft Docs"
description: "Lär dig vägledning och bästa praxis för att utföra haveriberedskap med Azure SQL Database."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 07/31/2016
ms.author: sashan
ms.openlocfilehash: 8e395153fc9907107156c3412e5e0de554c83750
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="performing-disaster-recovery-drill"></a>Utför Återställningsgranskning för katastrofåterställning
Du rekommenderas att verifieringen av programmet beredskap för återställningsarbetsflöde utförs med jämna mellanrum. Verifiera programfunktioner och följderna av förlust av data och/eller störningar innebär att redundansväxlingen är bra engineering. Det är också ett krav som de flesta branschstandarder som en del av business continuity certifikatutfärdare.

Utför en katastrofåterställning återställningsgranskning består av:

* Simulering data nivå avbrott
* Återställa
* Validera programåterställning integritet post

Beroende på hur du [utformats för ditt program för affärskontinuitet](sql-database-business-continuity.md), arbetsflödet att köra detaljerade kan variera. Nedan beskrivs bästa praxis som utför en katastrofåterställning återställningsgranskning i samband med Azure SQL Database.

## <a name="geo-restore"></a>Geo-återställning
För att förhindra förlust av data när du utför en katastrofåterställning återställningsgranskning, rekommenderar vi den detaljerade som använder en testmiljö genom att skapa en kopia av produktionsmiljön och använder den för att kontrollera programmets redundans arbetsflöde.

#### <a name="outage-simulation"></a>Avbrott simulering
Om du vill simulera avbrottet kan du ta bort eller byta namn på källdatabasen. Detta medför anslutningsfel till programmet.

#### <a name="recovery"></a>Återställning
* Utföra geo-återställning av databasen till en annan server enligt beskrivningen [här](sql-database-disaster-recovery.md).
* Ändra konfigurationen för programmet för att ansluta till den återställda databasen och följ de [konfigurera en databas efter återställningen](sql-database-disaster-recovery.md) guiden för att slutföra återställningen.

#### <a name="validation"></a>Validering
* Slutför detaljerade genom att verifiera (inklusive anslutningssträngar, inloggningar, grundläggande funktioner testning eller andra verifieringar som en del av standardprogrammet signeringar procedurer) programmet integritet efter återställningen.

## <a name="geo-replication"></a>Geo-replikering
För en databas som skyddas med geo-replikering innebär ökad Övning planerad växling till den sekundära databasen. Planerad redundans garanterar att primärt och sekundära databaserna är synkroniserade när rollerna växlas. Till skillnad från en oplanerad redundansväxling leder åtgärden inte till dataförlust, så detaljerade kan utföras i produktionsmiljön.

#### <a name="outage-simulation"></a>Avbrott simulering
Om du vill simulera avbrottet kan inaktivera du webbprogram eller virtuella datorn är ansluten till databasen. Detta resulterar i anslutningsproblemet för webbklienter.

#### <a name="recovery"></a>Återställning
* Kontrollera programkonfigurationen i DR-region som pekar på den gamla sekundära som blir den nya primärt fullt tillgängliga.
* Utföra [planerad redundans](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) så att den sekundära databasen till en ny primär
* Följ den [konfigurera en databas efter återställningen](sql-database-disaster-recovery.md) guiden för att slutföra återställningen.

#### <a name="validation"></a>Validering
* Slutför detaljerade genom att verifiera (inklusive anslutningssträngar, inloggningar, grundläggande funktioner testning eller andra verifieringar som en del av standardprogrammet signeringar procedurer) programmet integritet efter återställningen.

## <a name="next-steps"></a>Nästa steg
* Läs om affärsscenarier kontinuitet i [kontinuitet scenarier](sql-database-business-continuity.md)
* Lär dig mer om Azure SQL Database automatiserad säkerhetskopieringar, se [SQL-databas automatisk säkerhetskopiering](sql-database-automated-backups.md)
* Läs om hur du använder automatisk säkerhetskopiering för återställning i [återställa en databas från säkerhetskopior service-initierad](sql-database-recovery-using-backups.md)
* Mer information om alternativ för snabbare återställning, se [aktiv geo-replikering](sql-database-geo-replication-overview.md)  
