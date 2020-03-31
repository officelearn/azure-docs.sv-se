---
title: Hantera beräkningsresurs för SQL-pool
description: Lär dig mer om prestandaskalningsfunktioner i en AZURE Synapse Analytics SQL-pool. Skala ut genom att justera DWUs eller sänka kostnaderna genom att pausa informationslagret.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4c6c4d97282387fbcee1d7e8b55b95c01e3dded5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351600"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Hantera beräkning i Azure Synapse Analytics informationslager

Lär dig mer om hur du hanterar beräkningsresurser i Azure Synapse Analytics SQL-pool. Sänk kostnaderna genom att pausa SQL-poolen eller skala informationslagret för att uppfylla prestandakrav. 

## <a name="what-is-compute-management"></a>Vad är beräkningshantering?

Arkitekturen för informationslager separerar lagring och beräkning, så att var och en kan skalas oberoende av varandra. Det gör att du kan skala om beräkningsresurserna för att uppfylla prestandabehoven oberoende av datalagringen. Du kan också pausa och återuppta beräkningsresurser. En naturlig konsekvens av den här arkitekturen är att [fakturering](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) för beräkning och lagring är separat. Om du inte behöver använda ditt informationslager under en tid kan du spara beräkningskostnader genom att pausa databearbetningen. 

## <a name="scaling-compute"></a>Skala beräkning

Du kan skala ut eller skala tillbaka beräkning genom att justera inställningen för [informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) för DIN SQL-pool. Prestanda för inläsning och körning av frågor ökar linjärt när du lägger till fler informationslagerenheter. 

Utskalningssteg finns i [Snabbstarten för Azure-portalen,](quickstart-scale-compute-portal.md) [PowerShell](quickstart-scale-compute-powershell.md)eller [T-SQL.](quickstart-scale-compute-tsql.md) Du kan också utföra utskalningsåtgärder med ett [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

För att utföra en skalningsåtgärd dödar SQL-poolen först alla inkommande frågor och återställer sedan transaktioner för att säkerställa ett konsekvent tillstånd. Skalningen utförs först när transaktionerna har återställts. För en skalningsåtgärd kopplar systemet lagringslagret från beräkningsnoderna, lägger till beräkningsnoder och sätter sedan i lager för lagringsmedia igen i beräkningslagret. Varje SQL-pool lagras som 60 distributioner, som distribueras jämnt till beräkningsnoderna. Om du lägger till fler beräkningsnoder läggs mer beräkningskraft till. När antalet beräkningsnoder ökar minskar antalet distributioner per beräkningsnod, vilket ger mer beräkningskraft för dina frågor. På samma sätt minskar minskande informationslagerenheter antalet beräkningsnoder, vilket minskar beräkningsresurserna för frågor.

Följande tabell visar hur antalet distributioner per beräkningsnod ändras när informationslagerenheterna ändras.  DW30000c ger 60 beräkningsnoder och uppnår mycket högre frågeprestanda än DW100c. 

| Informationslagerenheter  | \#av beräkningsnoder | \#av distributioner per nod |
| -------- | ---------------- | -------------------------- |
| DW100c (på andra)   | 1                | 60                         |
| DW200c (på andra)   | 1                | 60                         |
| DW300c (på andra)   | 1                | 60                         |
| DW400c (på andra)   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Hitta rätt storlek på informationslagerenheter

Om du vill se prestandafördelarna med att skala ut, särskilt för större informationslagerenheter, vill du använda minst en datauppsättning med 1 TB. Om du vill hitta det bästa antalet informationslagerenheter för DIN SQL-pool kan du prova att skala upp och ned. Kör några frågor med olika antal informationslagerenheter när du har läst in data. Eftersom skalning är snabb kan du prova olika prestandanivåer på en timme eller mindre. 

Rekommendationer för att hitta det bästa antalet enheter för informationslager:

- För en SQL-pool under utveckling, börja med att välja ett mindre antal informationslagerenheter.  En bra utgångspunkt är DW400c eller DW200c.
- Övervaka programmets prestanda genom att observera antalet datalagerenheter som valts jämfört med den prestanda du observerar.
- Anta en linjär skala och bestäm hur mycket du behöver för att öka eller minska informationslagerenheterna. 
- Fortsätt att göra justeringar tills du når en optimal prestandanivå för dina affärsbehov.

## <a name="when-to-scale-out"></a>När ska skalas ut

Skalning av informationslagerenheter påverkar dessa aspekter av prestanda:

- Linjärt förbättrar systemets prestanda för skanningar, aggregeringar och CTAS-satser.
- Ökar antalet läsare och författare för inläsning av data.
- Maximalt antal samtidiga frågor och samtidighetsplatser.

Rekommendationer för när informationslagerenheter ska skalas ut:

- Innan du utför en tung datainläsning eller omvandlingsåtgärd skalar du ut för att göra data tillgängliga snabbare.
- Under rusningstid skalar du ut för att rymma ett större antal samtidiga frågor. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Vad händer om skalning inte förbättrar prestanda?

Lägga till informationslagerenheter som ökar parallellismen. Om arbetet är jämnt fördelat mellan beräkningsnoderna förbättrar den ytterligare parallellismen frågeprestanda. Om utskalning inte ändrar dina resultat finns det några orsaker till att detta kan hända. Dina data kan vara skeva över distributionerna, eller frågor kan införa en stor mängd dataförflyttning. Mer om du vill undersöka problem med frågeprestanda finns i [Felsökning av prestanda](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pausa och återuppta beräkningar

Om du pausar beräkning tas lagringslagret bort från beräkningsnoderna. Beräkningsresurserna frigörs från ditt konto. Du debiteras inte för beräkning medan beräkningen pausas. Återuppta beräkning återansluter lagring till beräkningsnoderna och återupptar avgifter för Beräkning. När du pausar en SQL-pool:

* Beräknings- och minnesresurser returneras till poolen med tillgängliga resurser i datacentret
* Kostnader för informationslagerenheter är noll under pausens varaktighet.
* Datalagring påverkas inte och dina data förblir intakta. 
* Alla löpande eller köade åtgärder avbryts.

När du återupptar en SQL-pool:

* SQL-poolen hämtar beräknings- och minnesresurser för inställningen för informationslagerenheter.
* Beräkningsavgifterna för dina informationslagerenheter återupptas.
* Dina data blir tillgängliga.
* När SQL-poolen är online måste du starta om dina arbetsbelastningsfrågor.

Om du alltid vill att sql-poolen ska vara tillgänglig kan du överväga att skala ned den till den minsta storleken i stället för att pausa. 

Stegvisa och återupptar du steg i [Azure-portalen](pause-and-resume-compute-portal.md)eller [PowerShell.](pause-and-resume-compute-powershell.md) Du kan också använda [REST-API:et för paus](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) eller [REST-APIN för återställning](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tömma transaktioner före pausning eller skalning

Vi rekommenderar att befintliga transaktioner tillåts innan du initierar en paus- eller skalningsåtgärd.

När du pausar eller skalar SQL-poolen avbryts dina frågor bakom kulisserna när du initierar begäran om paus eller skalning. Att avbryta en enkel SELECT-fråga är en snabb åtgärd och påverkar nästan inte alls den tid det tar att pausa eller skala instansen.  Transaktionsfrågor, som ändrar data eller datastrukturen, kan däremot ta längre tid att stoppa. **Transaktionsfrågor måste per definition slutföras i sin helhet eller så måste ändringarna återställas.** Det kan ta lång tid att återställa arbetet som en transaktionsfråga har utfört, till och med längre tid än den ursprungliga ändringen som frågan tillämpade. Om du till exempel avbryter en fråga som tog bort rader och som redan har körts i en timme, kan det ta en timme för systemet att lägga till de borttagna raderna igen. Om du pausar eller skalar under pågående transaktioner kan det verka som åtgärden tar lång tid eftersom pausningen och skalningen måste vänta tills återställningen har slutförts innan de kan fortsätta.

Se även [Förstå transaktioner](sql-data-warehouse-develop-transactions.md)och [Optimera transaktioner](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisera beräkningshantering

Information om hur du automatiserar beräkningshanteringsåtgärderna finns i [Hantera beräkning med Azure-funktioner](manage-compute-with-azure-functions.md).

Var och en av skalnings-, paus- och återuppta-åtgärderna kan ta flera minuter att slutföra. Om du skalar, pausar eller återupptar automatiskt rekommenderar vi att du implementerar logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd. Genom att kontrollera SQL-pooltillståndet via olika slutpunkter kan du korrekt implementera automatisering av sådana åtgärder. 

Om du vill kontrollera SQL-pooltillståndet läser du [snabbstarten powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) eller [T-SQL.](quickstart-scale-compute-tsql.md#check-data-warehouse-state) Du kan också kontrollera SQL-pooltillståndet med ett [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Behörigheter

För att skala SQL-poolen krävs de behörigheter som beskrivs i [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Paus och återställning kräver SQL DB Contributor-behörigheten, särskilt Microsoft.Sql/servers/databases/action. [SQL DB Contributor](../../role-based-access-control/built-in-roles.md#sql-db-contributor)


## <a name="next-steps"></a>Nästa steg
Se hur du [guidar](manage-compute-with-azure-functions.md) för att hantera beräkning En annan aspekt av hantering av beräkningsresurser allokerar olika beräkningsresurser för enskilda frågor. Mer information finns i [Resursklasser för arbetsbelastningshantering](resource-classes-for-workload-management.md).
