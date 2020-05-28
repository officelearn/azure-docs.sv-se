---
title: Accelererad återställning av databaser
description: Azure SQL Database har en ny funktion som ger snabb och konsekvent databas återställning, återställning av momentan transaktion och aggressiv logg trunkering för enskilda databaser och databaser i pooler i Azure SQL Database och databaser i Azure SQL Data Warehouse.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 05/19/2020
ms.openlocfilehash: f259d423f465d93031c3a72855fd7aac4e320573
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684972"
---
# <a name="accelerated-database-recovery"></a>Accelererad databas återställning

**Accelererad databas återställning (ADR)** är en funktion för SQL Database-motor som avsevärt förbättrar databasens tillgänglighet, särskilt i närvaro av tids krävande transaktioner genom att omkonstruera återställnings processen för SQL Database Engine. ADR är för närvarande tillgängligt för Azure SQL Database enskild, elastisk pool och hanterad instans och databaser i Azure SQL Data Warehouse (för närvarande i för hands version). De främsta fördelarna med ADR är:

- **Snabb och konsekvent databas återställning**

  Med ADR påverkar tids krävande transaktioner inte den totala återställnings tiden, vilket möjliggör snabb och konsekvent databas återställning oavsett antalet aktiva transaktioner i systemet eller deras storlek.

- **Återställning av momentan transaktion**

  I och med ADR är transaktions återställningen momentan, oavsett vilken tid transaktionen har varit aktiv eller antalet uppdateringar som har utförts.

- **Aggressiv logg trunkering**

  Med hjälp av ADR trunkeras transaktions loggen aggressivt, även om det finns aktiva långvariga transaktioner, vilket förhindrar att den växer ur kontroll.

## <a name="standard-database-recovery-process"></a>Standard process för databas återställning

Databas återställningen i SQL Server följer [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) -återställnings modellen och består av tre faser som illustreras i följande diagram och förklaras i detalj efter diagrammet.

![aktuell återställnings process](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analys fas**

  Vidarebefordra genomsökningen av transaktions loggen från början av den senaste lyckade kontroll punkten (eller den äldsta skadade sidans LSN) fram till slutet för att fastställa statusen för varje transaktion vid tidpunkten SQL Server stoppas.

- **Gör om fas**

  Vidarebefordra genomsökningen av transaktions loggen från den äldsta ej allokerade transaktionen till slutet för att flytta databasen till det tillstånd den hade vid tidpunkten för kraschen genom att göra om alla genomförda åtgärder.

- **Ångra fas**

  För varje transaktion som var aktiv vid tidpunkten för kraschen, passerar loggen bakåt, ångrar de åtgärder som den här transaktionen utförde.

Baserat på den här designen är den tid det tar för SQL Database-motorn att återställas från en oväntad omstart (ungefär) som är proportionell mot storleken på den längsta aktiva transaktionen i systemet vid tidpunkten för kraschen. Återställning kräver en återställning av alla ofullständiga transaktioner. Den tids period som krävs är proportionell mot det arbete som transaktionen utförts och den tid som den har varit aktiv. Därför kan SQL Server återställnings processen ta lång tid i närvaro av långvariga transaktioner (t. ex. stora Mass infognings åtgärder eller index Bygg åtgärder mot en stor tabell).

Även om du avbryter/återställer en stor transaktion som baseras på den här designen kan det ta lång tid att använda samma åtgärd för att ångra återställning enligt beskrivningen ovan.

Dessutom kan SQL Database-motorn inte trunkera transaktions loggen när det finns tids krävande transaktioner eftersom deras motsvarande logg poster behövs för återställnings-och återställnings processerna. Som ett resultat av den här designen av SQL Database-motorn, har vissa kunder använt för att möta problemet att storleken på transaktions loggen växer mycket stor och använder enorma mängder disk utrymme.

## <a name="the-accelerated-database-recovery-process"></a>Återställnings processen för påskyndad databas

I ADR åtgärdas ovanstående problem genom att processen för återställning av SQL Database-motorn helt omkonstrueras för att:

- Gör det konstant tid/omedelbar genom att undvika att behöva söka igenom loggen från/till början av den äldsta aktiva transaktionen. Med hjälp av ADR bearbetas transaktions loggen bara från den senaste lyckade kontroll punkten (eller äldsta Felaktiga sid nummer för Page log). Därför påverkas inte återställnings tiden av tids krävande transaktioner.
- Minimera det begärda transaktions logg utrymmet eftersom det inte längre behövs att bearbeta loggen för hela transaktionen. Därför kan transaktions loggen trunkeras aggressivt när kontroll punkter och säkerhets kopieringar sker.

Med en hög nivå uppnår ADR snabb databas återställning genom att versions hantering av alla fysiska databas ändringar och bara att ångra logiska åtgärder som är begränsade och kan ångras nästan omedelbart. Alla transaktioner som var aktiva vid tidpunkten för en krasch markeras som avbrutna och därför kan alla versioner som genereras av dessa transaktioner ignoreras av samtidiga användar frågor.

Återställnings processen i ADR har samma tre faser som den aktuella återställnings processen. Hur de här faserna fungerar med ADR illustreras i följande diagram och förklaras i detalj efter diagrammet.

![Process för ADR-återställning](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analys fas**

  Processen förblir densamma som innan med tillägg av omkonstruktion av sLog och kopiering av logg poster för icke-versioner av åtgärder.
  
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

  Det sparade versions lagret är en ny mekanism för SQL Database Engine för att bevara de rad versioner som genererats i själva databasen i stället för i det traditionella `tempdb` versions lagret. PVS möjliggör resurs isolering samt bättre tillgänglighet för läsbara sekundär servrar.

- **Logisk återställning**

  Logisk återställning är den asynkrona process som ansvarar för att utföra versions hantering på radnivå som ger omedelbar transaktions återställning och ångra för alla versions åtgärder. Logisk återställning utförs av:

  - Hålla reda på alla avbrutna transaktioner och markera dem som osynliga för andra transaktioner. 
  - Utföra återställningar med hjälp av PVS för alla användar transaktioner, i stället för att fysiskt genomsöka transaktions loggen och ångra ändringar en i taget.
  - Frigör alla Lås omedelbart efter att transaktionen har avbrutits. Eftersom avbrott innebär att du bara markerar ändringar i minnet är processen mycket effektiv och därför behöver inte låsen hållas under en längre tid.

- **sLog**

  sLog är en sekundär logg ström i minnet som lagrar logg poster för icke-versioner av åtgärder (till exempel ogiltighet av metadata-cache, Lås förvärv och så vidare). SLog är:

  - Låg volym och minnes intern
  - Sparas på disken genom att serialiseras under kontroll punkts processen
  - Trunkeras med jämna mellanrum när transaktioner genomfördes
  - Påskyndar göra om och ångra bara genom att bearbeta de åtgärder som inte är versioner  
  - Möjliggör aggressiv transaktions logg trunkering genom att bevara de nödvändiga logg posterna

- **Rengörings**

  Cleaner är den asynkrona processen som aktiverar regelbundet och rensar sid versioner som inte behövs.

## <a name="accelerated-database-recovery-patterns"></a>Påskyndade databas återställnings mönster

Följande typer av arbets belastningar förmånen är de flesta från ADR:

- Arbets belastningar med tids krävande transaktioner.
- Arbets belastningar som har visat fall där aktiva transaktioner gör att transaktions loggen växer avsevärt.  
- Arbets belastningar som har haft långa perioder av databasen är inte tillgängliga på grund av SQL Server långvarig återställning (till exempel oväntad SQL Server omstart eller manuell transaktions återställning).

