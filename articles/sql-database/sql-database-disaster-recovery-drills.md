---
title: Borrmaskiner för katastrofåterställning
description: Lär dig vägledning och metodtips för att använda Azure SQL Database för att utföra övningar för haveriberedskap.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 3ca00a03976ae38b7956616b8287220a7bc5998c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825859"
---
# <a name="performing-disaster-recovery-drill"></a>Utföra borrmaskin för katastrofåterställning

Vi rekommenderar att validering av programberedskap för återställningsarbetsflöde utförs regelbundet. Att verifiera programmets beteende och konsekvenserna av dataförlust och/eller störningar som redundans innebär är en bra teknisk praxis. Det är också ett krav enligt de flesta branschstandarder som en del av kontinuitet certifiering.

Utföra en katastrofåterställning borr består av:

* Simulera avbrott på datanivå
* Återställa
* Verifiera återställning efter programintegritet efter återställning

Beroende på hur du [har utformat programmet för kontinuitet](sql-database-business-continuity.md)i verksamheten kan arbetsflödet för att köra övningen variera. I den här artikeln beskrivs de bästa metoderna för att utföra en borrmaskin för haveriberedskap i samband med Azure SQL Database.

## <a name="geo-restore"></a>Geo-återställning

För att förhindra potentiell dataförlust när du utför en haveriberedskapsövning utför du borren med hjälp av en testmiljö genom att skapa en kopia av produktionsmiljön och använda den för att verifiera programmets redundansarbetsflöde.

### <a name="outage-simulation"></a>Simulering av avbrott

Om du vill simulera avbrottet kan du byta namn på källdatabasen. Den här namnändringen orsakar programanslutningsfel.

### <a name="recovery"></a>Återställning

* Utför geoåterställning av databasen till en annan server enligt beskrivningen [här](sql-database-disaster-recovery.md).
* Ändra programkonfigurationen för att ansluta till den återställda databasen och följ [guiden Konfigurera en databas efter återställning](sql-database-disaster-recovery.md) för att slutföra återställningen.

### <a name="validation"></a>Validering

Slutför övningen genom att verifiera programmets integritet efter återställning (inklusive anslutningssträngar, inloggningar, grundläggande funktionstestning eller annan valideringsdel av standardinloggningsprocedurer för program).

## <a name="failover-groups"></a>Redundansgrupper

För en databas som är skyddad med redundansgrupper innebär övningen planerad redundans till den sekundära servern. Den planerade redundansen säkerställer att de primära och sekundära databaserna i redundansgruppen förblir synkroniserade när rollerna växlas. Till skillnad från den oplanerade redundansen resulterar den här åtgärden inte i dataförlust, så borren kan utföras i produktionsmiljön.

### <a name="outage-simulation"></a>Simulering av avbrott

Om du vill simulera avbrottet kan du inaktivera webbprogrammet eller den virtuella datorn som är ansluten till databasen. Den här avbrottssimuleringen resulterar i anslutningsfel för webbklienterna.

### <a name="recovery"></a>Återställning

* Kontrollera att programkonfigurationen i DR-regionen pekar på den tidigare sekundära, som blir den helt tillgängliga nya primära.
* Initiera [planerad redundans för](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) redundansgruppen från den sekundära servern.
* Följ [guiden Konfigurera en databas efter återställning](sql-database-disaster-recovery.md) för att slutföra återställningen.

### <a name="validation"></a>Validering

Slutför övningen genom att verifiera programmets integritet efter återställning (inklusive anslutning, grundläggande funktionstestning eller andra valideringar som krävs för övningsloggningar).

## <a name="next-steps"></a>Nästa steg

* Mer information om scenarier för affärskontinuitet finns i [Kontinuitetsscenarier](sql-database-business-continuity.md).
* Mer information om automatiska säkerhetskopior i Azure SQL Database finns i [automatiska säkerhetskopieringar i SQL Database](sql-database-automated-backups.md)
* Mer information om hur du använder automatiska säkerhetskopior för återställning finns i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
* Mer information om snabbare återställningsalternativ finns i [Aktiva geo-replikerings-](sql-database-active-geo-replication.md) och [automatisk redundansgrupper](sql-database-auto-failover-group.md).
