---
title: Accelererad databas återställning – Azure SQL Database
description: Azure SQL Database har en ny funktion som ger snabb och konsekvent databas återställning, återgång av momentan transaktion och aggressiv logg trunkering för enskilda databaser och databaser i pooler i Azure SQL Database och databaser i Azure SQL data Lagerinleveransen.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: e66b3e6563d796cc7b59e82233bd1b22bc906c6e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691350"
---
# <a name="accelerated-database-recovery"></a>Accelererad databas återställning

**Accelererad databas återställning (ADR)** är en ny funktion i SQL Database Engine som avsevärt förbättrar databasens tillgänglighet, särskilt i närvaro av tids krävande transaktioner genom att omkonstruera återställnings processen för SQL Database Engine. ADR är för närvarande tillgängligt för enskilda databaser och databaser i pooler i Azure SQL Database och databaser i Azure SQL Data Warehouse (för närvarande i offentlig för hands version). De främsta fördelarna med ADR är:

- **Snabb och konsekvent databas återställning**

  Med ADR påverkar tids krävande transaktioner inte den totala återställnings tiden, vilket möjliggör snabb och konsekvent databas återställning oavsett antalet aktiva transaktioner i systemet eller deras storlek.

- **Återställning av momentan transaktion**

  I och med ADR är transaktions återställningen momentan, oavsett vilken tid transaktionen har varit aktiv eller antalet uppdateringar som har utförts.

- **Aggressiv logg trunkering**

  Med hjälp av ADR trunkeras transaktions loggen aggressivt, även om det finns aktiva tids krävande transaktioner, vilket förhindrar att den växer ur kontroll.

## <a name="the-current-database-recovery-process"></a>Den aktuella databas återställnings processen

Databas återställningen i SQL Server följer [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) -återställnings modellen och består av tre faser som illustreras i följande diagram och förklaras i detalj efter diagrammet.

![aktuell återställnings process](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analys fas**

  Vidarebefordra genomsökningen av transaktions loggen från början av den senaste lyckade kontroll punkten (eller den äldsta skadade sidans LSN) fram till slutet för att fastställa statusen för varje transaktion vid tidpunkten SQL Server stoppas.

- **Gör om fas**

  Vidarebefordra genomsökningen av transaktions loggen från den äldsta ej allokerade transaktionen till slutet för att flytta databasen till det tillstånd den hade vid tidpunkten för kraschen genom att göra om alla genomförda åtgärder.

- **Ångra fas**

  För varje transaktion som var aktiv vid tidpunkten för kraschen, passerar loggen bakåt, ångrar de åtgärder som den här transaktionen utförde.

Baserat på den här designen är den tid det tar för SQL Database-motorn att återställas från en oväntad omstart (ungefär) som är proportionell mot storleken på den längsta aktiva transaktionen i systemet vid tidpunkten för kraschen. Återställning kräver en återställning av alla ofullständiga transaktioner. Den tids period som krävs är proportionell mot det arbete som transaktionen utförts och den tid som den har varit aktiv. Därför kan SQL Server återställnings processen ta lång tid i närvaron av tids krävande transaktioner (till exempel stora Mass infognings åtgärder eller index Bygg åtgärder mot en stor tabell).

Även om du avbryter/återställer en stor transaktion som baseras på den här designen kan det ta lång tid att använda samma åtgärd för att ångra återställning enligt beskrivningen ovan.

Dessutom kan SQL Database-motorn inte trunkera transaktions loggen när det finns tids krävande transaktioner eftersom deras motsvarande logg poster behövs för återställnings-och återställnings processerna. Till följd av den här designen av SQL Database-motorn är vissa kunder till problem att storleken på transaktions loggen växer mycket stor och använder enorma mängder disk utrymme.

## <a name="the-accelerated-database-recovery-process"></a>Återställnings processen för påskyndad databas

I ADR åtgärdas ovanstående problem genom att processen för återställning av SQL Database-motorn helt omkonstrueras för att:

- Gör det konstant tid/omedelbar genom att undvika att behöva söka igenom loggen från/till början av den äldsta aktiva transaktionen. Med hjälp av ADR bearbetas transaktions loggen bara från den senaste lyckade kontroll punkten (eller äldsta Felaktiga sid nummer för Page log). Därför påverkas inte återställnings tiden av tids krävande transaktioner.
- Minimera det begärda transaktions logg utrymmet eftersom det inte längre behövs att bearbeta loggen för hela transaktionen. Därför kan transaktions loggen trunkeras aggressivt när kontroll punkter och säkerhets kopieringar sker.

Med en hög nivå uppnår ADR snabb databas återställning genom att versions hantering av alla fysiska databas ändringar och bara att ångra logiska åtgärder som är begränsade och kan ångras nästan omedelbart. Alla transaktioner som var aktiva vid tidpunkten för en krasch markeras som avbrutna och därför kan alla versioner som genereras av dessa transaktioner ignoreras av samtidiga användar frågor.

Återställnings processen i ADR har samma tre faser som den aktuella återställnings processen. Hur de här faserna fungerar med ADR illustreras i följande diagram och förklaras i detalj efter diagrammet.

![Process för ADR-återställning](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analys fas**

  Processen förblir densamma som idag med tillägg av omkonstruktion av sLog och kopiering av logg poster för icke-versioner av åtgärder.
  
- **Gör om** fas

  Uppdelat i två faser (P)
  - Fas 1

      Gör om från sLog (äldsta ej allokerade transaktion upp till den sista kontroll punkten). REG är en snabb åtgärd eftersom det bara behöver bearbeta några poster från sLog.
      
  - Fas 2

     Gör om från transaktions loggen startar från den senaste kontroll punkten (i stället för den äldsta ej allokerade transaktionen)
     
- **Ångra fas**

   Ã... ngra-fasen med ADR slutförs nästan omedelbart genom att använda sLog för att ångra icke-versioner av åtgärder och beständiga versions lager (PVS) med logisk återställning för att utföra versions hantering på radnivå.

## <a name="adr-recovery-components"></a>Återställnings komponenter i ADR

De fyra viktiga komponenterna i ADR är:

- **Beständiga versions lager (PVS)**

  Det beständiga versions arkivet är en ny mekanism för SQL Database-motor för att bevara de rad versioner som genereras i själva databasen i stället för den traditionella `tempdb` versions lagringen. PVS möjliggör resurs isolering samt bättre tillgänglighet för läsbara sekundär servrar.

- **Logisk återställning**

  Logisk återställning är den asynkrona process som ansvarar för att utföra versions hantering på radnivå som ger omedelbar transaktions återställning och ångra för alla versions åtgärder.

  - Håller reda på alla avbrutna transaktioner
  - Utför återställning med PVS för alla användar transaktioner
  - Frigör alla Lås omedelbart efter att transaktionen har avbrutits

- **sLog**

  sLog är en sekundär logg ström i minnet som lagrar logg poster för icke-versioner av åtgärder (till exempel ogiltighet av metadata-cache, Lås förvärv och så vidare). SLog är:

  - Låg volym och minnes intern
  - Sparas på disken genom att serialiseras under kontroll punkts processen
  - Trunkeras med jämna mellanrum när transaktioner genomfördes
  - Påskyndar göra om och ångra bara genom att bearbeta de åtgärder som inte är versioner  
  - Möjliggör aggressiv transaktions logg trunkering genom att bevara de nödvändiga logg posterna

- **Rengörings**

  Cleaner är den asynkrona processen som aktiverar regelbundet och rensar sid versioner som inte behövs.

## <a name="who-should-consider-accelerated-database-recovery"></a>Vem bör överväga accelererad databas återställning

Följande typer av kunder bör överväga att aktivera ADR:

- Kunder som har arbets belastningar med tids krävande transaktioner.
- Kunder som har sett fall där aktiva transaktioner gör att transaktions loggen växer avsevärt.  
- Kunder som har haft långa perioder av databasen är inte tillgängliga på grund av SQL Server långvarig återställning (till exempel oväntad SQL Server omstart eller manuell transaktions återställning).

