---
title: Accelererad återställning av databaser
description: Azure SQL Database har en ny funktion som ger snabb och konsekvent återställning av databaser, omedelbar återställning av transaktioner och aggressiv logg trunkering för enskilda databaser och poolade databaser i Azure SQL Database och databaser i Azure SQL Data Lager.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238665"
---
# <a name="accelerated-database-recovery"></a>Snabbare återställning av databaser

**Accelererad databasåterställning (ADR)** är en SQL-databasmotorfunktion som avsevärt förbättrar databastillgängligheten, särskilt i närvaro av långvariga transaktioner, genom att omforma sql-databasmotorns återställningsprocess. ADR är för närvarande tillgängligt för single, elastisk pool och hanterad instans i Azure SQL Database (för närvarande i förhandsversion). De främsta fördelarna med alternativ tvistlösning är:

- **Snabb och konsekvent databasåterställning**

  Med ADR påverkar långvariga transaktioner inte den totala återställningstiden, vilket möjliggör snabb och konsekvent databasåterställning oavsett antalet aktiva transaktioner i systemet eller deras storlek.

- **Omedelbar återställning av transaktioner**

  Med ADR är återställning av transaktioner ögonblicklig, oavsett vilken tid transaktionen har varit aktiv eller antalet uppdateringar som har utförts.

- **Aggressiv logg trunkering**

  Med ADR är transaktionsloggen aggressivt trunkerad, även i närvaro av aktiva långvariga transaktioner, vilket förhindrar att den växer utom kontroll.

## <a name="the-current-database-recovery-process"></a>Den aktuella återställningsprocessen för databasen

Databasåterställning i SQL Server följer [ARIES-återställningsmodellen](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) och består av tre faser, som illustreras i följande diagram och förklaras mer i detalj efter diagrammet.

![nuvarande återställningsprocessen](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analysfas**

  Vidarebefordra genomsökning av transaktionsloggen från början av den senast lyckade kontrollpunkten (eller den äldsta smutsiga sidan LSN) till slutet, för att fastställa tillståndet för varje transaktion vid den tidpunkt då SQL Server stoppades.

- **Gör om fas**

  Vidarebefordra genomsökning av transaktionsloggen från den äldsta oengagerade transaktionen till slutet, för att föra databasen till det tillstånd den var vid tidpunkten för kraschen genom att göra om alla genomförda åtgärder.

- **Ångra fas**

  För varje transaktion som var aktiv vid tidpunkten för kraschen, korsar loggen bakåt, ångra de åtgärder som transaktionen utförs.

Baserat på den här designen är den tid det tar för SQL-databasmotorn att återställa från en oväntad omstart (ungefär) proportionell mot storleken på den längsta aktiva transaktionen i systemet vid tidpunkten för kraschen. Återställning kräver en återställning av alla ofullständiga transaktioner. Den tid som krävs står i proportion till det arbete som transaktionen har utfört och den tid den har varit aktiv. Därför kan SQL Server-återställningsprocessen ta lång tid i närvaro av långvariga transaktioner (till exempel stora massinfogningsåtgärder eller indexuppbyggnadsåtgärder mot en stor tabell).

Dessutom kan det ta lång tid att avbryta/återställa en stor transaktion baserat på den här designen eftersom den använder samma Ångra återställningsfas som beskrivs ovan.

Dessutom kan SQL-databasmotorn inte trunkera transaktionsloggen när det finns långvariga transaktioner eftersom deras motsvarande loggposter behövs för återställnings- och återställningsprocesserna. Som ett resultat av denna design av SQL-databasmotorn, vissa kunder som används för att möta problemet att storleken på transaktionsloggen växer mycket stor och förbrukar enorma mängder enhetsutrymme.

## <a name="the-accelerated-database-recovery-process"></a>Den accelererade återställningsprocessen för databasen

ADR åtgärdar ovanstående problem genom att helt omforma återställningsprocessen för SQL-databasmotorn till:

- Gör det konstant tid / ögonblick genom att undvika att behöva skanna loggen från / till början av den äldsta aktiva transaktionen. Med ADR bearbetas transaktionsloggen endast från den senast lyckade kontrollpunkten (eller äldsta LSN (Log Sequence Number) (Dirty Page Log Sequence Number). Därför påverkas inte återställningstiden av långvariga transaktioner.
- Minimera det transaktionsloggutrymme som krävs eftersom det inte längre finns något behov av att bearbeta loggen för hela transaktionen. Därför kan transaktionsloggen trunkeras aggressivt när kontrollpunkter och säkerhetskopior inträffar.

På en hög nivå uppnår ADR snabb databasåterställning genom att versionsa alla fysiska databasändringar och endast ångra logiska åtgärder, som är begränsade och kan ångras nästan omedelbart. Alla transaktioner som var aktiva vid tidpunkten för en krasch markeras som avbrutna och därför kan alla versioner som genereras av dessa transaktioner ignoreras av samtidiga användarfrågor.

Adr-återställningsprocessen har samma tre faser som den aktuella återställningsprocessen. Hur dessa faser fungerar med ADR illustreras i följande diagram och förklaras mer i detalj efter diagrammet.

![Återställningsprocessen för ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analysfas**

  Processen förblir densamma som tidigare med tillägg av rekonstruera sLog och kopiera loggposter för icke-versionsbaserade åtgärder.
  
- **Gör om** fas

  Uppdelad i två faser (P)
  - Fas 1

      Gör om från sLog (äldsta oengagerade transaktionen fram till sista kontrollpunkten). Gör om är en snabb åtgärd eftersom det bara behöver bearbeta några poster från sLog.
      
  - Fas 2

     Gör om från transaktionsloggen startar från den senaste kontrollpunkten (i stället för äldsta obekräftade transaktioner)
     
- **Ångra fas**

   Ångra-fasen med ADR slutförs nästan omedelbart med hjälp av sLog för att ångra icke-versionsbaserade åtgärder och Beständig version Store (PVS) med Logisk återställning för att utföra versionsbaserad Ångra på radnivå.

## <a name="adr-recovery-components"></a>Komponenter för ÅTERSTÄLLNING AV ADR

De fyra viktigaste komponenterna i ADR är:

- **Beständig version Store (PVS)**

  Den beständiga versionsarkivet är en ny SQL-databasmotormekanism för att bevara `tempdb` radversionerna som genereras i själva databasen i stället för det traditionella versionsarkivet. PVS möjliggör resursisolering och förbättrar tillgängligheten för läsbara sekundärer.

- **Logisk återställning**

  Logisk återställning är den asynkrona process som ansvarar för att utföra versionsbaserad Ångra på radnivå – vilket ger omedelbar återställning av transaktioner och ångrar för alla versionsbaserade åtgärder. Logisk återställning sker genom:

  - Hålla reda på alla avbrutna transaktioner och markera dem osynliga för andra transaktioner. 
  - Utför återställning med hjälp av PVS för alla användartransaktioner, i stället för att fysiskt skanna transaktionsloggen och ångra ändringar en i taget.
  - Släppa alla lås omedelbart efter transaktionen avbryts. Eftersom abort innebär helt enkelt märkning förändringar i minnet, är processen mycket effektiv och därför lås behöver inte hållas under en lång tid.

- **Slit**

  sLog är en sekundär loggström i minnet som lagrar loggposter för icke-versionsbaserade åtgärder (till exempel ogiltigförklaring av metadatacache, låsinhämtningar och så vidare). SLog är:

  - Låg volym och i minnet
  - Sparade på disken genom att serialiseras under kontrollpunktsprocessen
  - Regelbundet trunkeras som transaktioner begå
  - Accelererar gör om och ångrar genom att endast bearbeta icke-versionsbaserade åtgärder  
  - Aktiverar aggressiv transaktionslogg trunkering genom att bevara endast de loggposter som krävs

- **Renare**

  Dammsugaren är den asynkrona process som vaknar regelbundet och rensar sidversioner som inte behövs.

## <a name="accelerated-database-recovery-patterns"></a>Snabbare databasåterställningsmönster

Följande typer av arbetsbelastningar drar störst nytta av ADR:

- Arbetsbelastningar med långvariga transaktioner.
- Arbetsbelastningar som har sett fall där aktiva transaktioner orsakar transaktionsloggen att växa avsevärt.  
- Arbetsbelastningar som har upplevt långa perioder av databas otillgänglighet på grund av SQL Server tidskrävande återställning (till exempel oväntad SQL Server omstart eller manuell transaktionsåterställning).

