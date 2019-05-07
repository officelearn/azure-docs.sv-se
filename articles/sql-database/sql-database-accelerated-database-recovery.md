---
title: Accelererad databasåterställning – Azure SQL Database | Microsoft Docs
description: Azure SQL-databasen har en ny funktion som ger snabb och konsekvent databasåterställning och omedelbar transaktionsåterställning aggressiva loggtrunkering för enskilda databaser och databaser i en pool i Azure SQL Database och databaser i Azure SQL Data Datalager.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 77bc33747964a5f4ee1a67aba777dc3ed76b9a51
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073455"
---
# <a name="accelerated-database-recovery"></a>Accelererad databasåterställning

**Accelerated Database Recovery (ADR)** en ny SQL database engine-funktion som avsevärt förbättrar databastillgänglighet, särskilt om det förekommer långa körs transaktioner, genom att göra om SQL database engine återställningsprocessen. Regel för automatisk distribution är för närvarande tillgängligt för enskilda databaser och databaser i en pool i Azure SQL Database och databaser i Azure SQL Data Warehouse. De främsta fördelarna med ADR är:

- **Snabbt och konsekvent databasåterställning**

  Med ADR påverkar långvariga transaktioner inte den övergripande återställningstid aktiverar snabbt och konsekvent databasåterställning oavsett antal aktiva transaktioner i systemet eller deras storlek.

- **Omedelbar transaktionsåterställning**

  Med automatisk Distributionsregel är transaktionsåterställning omedelbara, oavsett den tid som transaktionen har varit aktivt eller antalet uppdateringar som har utförts.

- **Aggressiva loggtrunkering**

  Med automatisk Distributionsregel trunkeras transaktionsloggen Aggressivt, även om det förekommer aktiva långvariga transaktioner, vilket förhindrar växande alldeles.

## <a name="the-current-database-recovery-process"></a>Den aktuella databas återställningsprocessen

Databasåterställning i SQL Server följer den [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) återställningsmodellen och består av tre faser, som illustreras i följande diagram och förklaras i detalj följa diagrammet.

![aktuella återställningsprocessen](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Analysfasen**

  Vidarebefordra avsökning av transaktionsloggen från början av den senaste kontrollpunkten (eller äldsta dirty sidan LSN) tills du är klar att bestämma tillståndet för varje transaktion när SQL Server har stoppats.

- **Gör om fas**

  Vidarebefordra avsökning av transaktionsloggen från den äldsta ogenomförda transaktionen, tills att databasen till tillståndet den var vid tidpunkten för kraschen genom att göra om alla allokerade åtgärder.

- **Ångra fas**

  För varje transaktion som var aktivt från och med tidpunkten för kraschen inom loggen bakåtkompatibilitet, ångra åtgärder som utförs av den här transaktionen.

Baserat på den här designen kan är den tid det tar SQL database engine att återställa från en oväntad omstart (ungefär) proportion till storleken på den längsta aktiv transaktionen i systemet vid tidpunkten för kraschen. Recovery kräver en återställning av alla ofullständiga transaktioner. Hur lång tid som krävs är proportionell mot det arbete som transaktionen har utförts och tid det har varit aktiv. Därför kan återställningsprocessen för SQL Server ta lång tid i förekomsten av långvariga transaktioner (t.ex. stora massinfogning index build åtgärder mot en stor tabell).

Dessutom kan avbryter/återställa en stor transaktion baserat på den här designen också ta lång tid eftersom den använder samma recovery ångringsfasen enligt beskrivningen ovan.

Dessutom kan SQL database engine kan inte trunkera transaktionsloggen när det är långa körs transaktioner eftersom deras motsvarande poster krävs för återställning och rollback-processer. Till följd av den här designen av SQL database engine inför vissa kunder problemet att storleken på transaktionsloggen växer mycket stora och förbrukar stora mängder diskutrymme.

## <a name="the-accelerated-database-recovery-process"></a>Processen accelererad databasåterställning

ADR löser dessa frågor genom att helt ny design återställningsprocessen för SQL database engine till:

- Gör det konstanta tid/snabbmeddelanden genom att inte behöva skanna loggen från/till början av den äldsta aktiva transaktionen. Med automatisk Distributionsregel bearbetas bara transaktionsloggen från den senaste kontrollpunkten (eller äldsta dirty sidan Log Sequence Number (LSN)). Därför återställningstid påverkas inte av långa köra transaktioner.
- Minimera krävs transaktionsloggutrymmet eftersom det finns inte längre behöver bearbeta loggen för hela transaktionen. Därför transaktionsloggen kan trunkeras aggressivt som kontrollpunkter och säkerhetskopieras.

På en högnivå uppnår ADR snabb databasåterställning genom versionshantering alla ändringar av den fysiska databasen och bara ångra logiska åtgärder, som är begränsade och går att ångra nästan omedelbart. Alla transaktioner som var aktiv vid tidpunkten havererade är markerade som avbrutits och därför alla versioner som genereras av de här transaktionerna kan ignoreras av samtidiga användarfrågor.

Den automatiska Distributionsregel återställningsprocessen har samma tre faser som den aktuella återställningsprocessen. Hur faserna fungerar med ADR illustreras i följande diagram och förklaras i detalj följa diagrammet.

![ADR återställningsprocessen](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Analysfasen**

  Processen är densamma som i dag med hjälp av rekonstruera sLog och kopiering av loggposter för åtgärder som inte är versionshanterat.
  
- **Gör om** fas

  Uppdelat i två faser (P)
  - Fas 1

      Gör om från sLog (äldsta ogenomförda transaktioner upp till senaste kontrollpunkten). Gör om-åtgärd är en snabb åtgärd som bara behöver bearbetas några poster från sLog.
      
  - Fas 2

     Gör om från transaktionsloggen startar från senaste kontrollpunkten (i stället för äldsta ogenomförda transaktioner)
     
- **Ångra fas**

   Ångringsfasen med ADR har slutförts nästan omedelbart med hjälp av sLog för att ångra åtgärder som inte är versionshanterat och beständiga Version Store (PVS) med logiska återgå till att utföra rad nivå version-baserade ångra.

## <a name="adr-recovery-components"></a>ADR recovery-komponenter

Det finns fyra viktiga komponenter i ADR:

- **Beständiga Version Store (PVS)**

  Det beständiga versionsarkivet är en ny SQL database engine-mekanism för att spara radversioner som genererats i själva databasen i stället för den traditionella för `tempdb` versionsarkivet. PVS möjliggör resursisolering samt förbättrar tillgängligheten för läsbara sekundära databaser.

- **Logiska återgå**

  Logisk återställa ansvarar asynkrona processen för att utföra på radnivå baserat på version Ångra – vilket ger omedelbar transaktionsåterställning och ångra för alla åtgärder som en ny version.

  - Håller reda på alla avbrutna transaktioner
  - Utför återställning med hjälp av PVS för alla användartransaktioner
  - Versioner alla Lås omedelbart efter att transaktionen Avbryt

- **sLog**

  sLog är en sekundär InMemory-loggström att butiker logga poster för inte är versionshanterat åtgärder (till exempel metadata cache ogiltigförklarade, lås anskaffning och så vidare). SLog är:

  - Låg volym och i minnet
  - Sparas på disken genom att serialisera under kontrollpunktsprocessen
  - Med jämna mellanrum trunkeras som transaktioner incheckning
  - Går det snabbare att göra om och ångra genom att bearbeta endast åtgärderna som inte är versionshanterat  
  - Aktiverar aggressiva transaktion trunkering av loggen genom att bevara de nödvändiga loggposter

- **Tydligare**

  Rensaren är en asynkron process som aktiveras med jämna mellanrum och rensar sidan-versioner som inte behövs.

## <a name="who-should-consider-accelerated-database-recovery"></a>Vilka bör överväga accelererad databasåterställning

Följande typer av kunder bör överväga att aktivera automatisk Distributionsregel:

- Kunder som har arbetsbelastningar med långa pågående transaktioner.
- Kunder som har sett fall där aktiva transaktioner orsakar transaktionsloggen att växa avsevärt.  
- Kunder som har haft långa perioder av inaktivitet för databasen på grund av SQL Server som körs länge recovery (till exempel oväntat SQL Server startas om eller manuell transaktionsåterställning).

