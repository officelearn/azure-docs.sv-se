---
title: Katastrof återställnings övningar
description: Lär dig mer om vägledning och bästa praxis för att använda Azure SQL Database för att utföra haveri beredskap.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73825859"
---
# <a name="performing-disaster-recovery-drill"></a>Utför katastrof återställnings granskning

Vi rekommenderar att validering av program beredskap för arbets flöde för återställning utförs med jämna mellanrum. Att kontrol lera programmets beteende och konsekvenserna av data förlust och/eller störningar i redundansväxlingen är en bra tekniker. Det är också ett krav för de flesta bransch standarder som en del av certifieringen för affärs kontinuitet.

Att utföra en haveri beredskaps granskning består av:

* Simulera avbrott i data skiktet
* Återställa
* Verifiera program integritet efter återställning

Beroende på hur du har [utformat ditt program för affärs kontinuitet](sql-database-business-continuity.md)kan arbets flödet för att köra detalj nivån variera. I den här artikeln beskrivs de bästa metoderna för att utföra en haveri beredskaps granskning i samband med Azure SQL Database.

## <a name="geo-restore"></a>Geo-återställning

För att förhindra data förlust vid haveri beredskap, utför du detalj granskningen med en test miljö genom att skapa en kopia av produktions miljön och använda den för att verifiera programmets arbets flöde för redundans.

### <a name="outage-simulation"></a>Simulering av avbrott

För att simulera avbrottet kan du byta namn på käll databasen. Den här namn ändringen orsakar program anslutnings fel.

### <a name="recovery"></a>Återställning

* Utför geo-återställning av databasen till en annan server enligt beskrivningen [här](sql-database-disaster-recovery.md).
* Ändra program konfigurationen för att ansluta till den återställda databasen och följ återställnings hand boken [Konfigurera en databas efter återställnings](sql-database-disaster-recovery.md) guide.

### <a name="validation"></a>Validering

Slutför detalj nivån genom att kontrol lera program integritet efter återställning (inklusive anslutnings strängar, inloggningar, grundläggande funktioner testning eller andra validerings delar av standard program signerings procedurer).

## <a name="failover-groups"></a>Redundansgrupper

För en databas som skyddas med hjälp av failover-grupper, innebär gransknings övningen Planerad redundans till den sekundära servern. Den planerade redundansväxlingen säkerställer att den primära databasen och de sekundära databaserna i gruppen för växling vid fel är synkroniserad när rollerna växlas. Till skillnad från oplanerad redundansväxling leder den här åtgärden inte till data förlust, så att detalj nivån kan utföras i produktions miljön.

### <a name="outage-simulation"></a>Simulering av avbrott

För att simulera avbrottet kan du inaktivera webb programmet eller den virtuella datorn som är ansluten till databasen. Den här simuleringen av avbrott resulterar i anslutnings problem för webb klienter.

### <a name="recovery"></a>Återställning

* Kontrol lera att program konfigurationen i DR-regionen pekar på den tidigare sekundära, som blir den fullt tillgängliga nya primära servern.
* Initiera [planerad redundansväxling](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) av gruppen för redundans från den sekundära servern.
* Slutför återställningen genom att följa guiden [Konfigurera en databas efter återställnings](sql-database-disaster-recovery.md) guide.

### <a name="validation"></a>Validering

Slutför detalj nivån genom att verifiera program integritet efter återställning (inklusive anslutning, grundläggande funktioner testning eller andra verifieringar som krävs för gransknings signeringar).

## <a name="next-steps"></a>Nästa steg

* Mer information om affärs kontinuitets scenarier finns i [kontinuitets scenarier](sql-database-business-continuity.md).
* Mer information om hur du Azure SQL Database automatiserade säkerhets kopieringar finns [SQL Database automatiska säkerhets kopieringar](sql-database-automated-backups.md)
* Information om hur du använder automatiska säkerhets kopieringar för återställning finns i [återställa en databas från de säkerhets kopior som initieras av tjänsten](sql-database-recovery-using-backups.md).
* Läs mer om snabbare återställnings alternativ i aktiva grupper för [geo-replikering](sql-database-active-geo-replication.md) och [Automatisk redundans](sql-database-auto-failover-group.md).
