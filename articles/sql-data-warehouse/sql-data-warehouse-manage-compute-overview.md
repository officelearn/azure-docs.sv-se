---
title: Hantera beräknings resurs för SQL-pool
description: 'Lär dig mer om funktionerna för skalning av prestanda i en Azure Synapse Analytics SQL-pool. Skala ut genom att justera DWU: er eller minska kostnaderna genom att pausa data lagret.'
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ef860fb607a4f241e6428b714b022058a4697228
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197291"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Hantera beräkning i Azure Synapse Analytics data Warehouse

Lär dig mer om att hantera beräknings resurser i Azure Synapse Analytics SQL-poolen. Sänk kostnaderna genom att pausa SQL-poolen eller skala data lagret så att det uppfyller prestanda kraven. 

## <a name="what-is-compute-management"></a>Vad är beräknings hantering?

Arkitekturen i informations lagret särskiljer lagring och beräkning, vilket gör att de kan skalas oberoende av varandra. Det innebär att du kan skala beräkning för att uppfylla prestanda krav oberoende av data lagring. Du kan också pausa och återuppta beräknings resurser. En naturlig följd av denna arkitektur är att [faktureringen](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) för beräkning och lagring är separat. Om du inte behöver använda ditt informations lager en stund kan du spara beräknings kostnader genom att pausa beräkningarna. 

## <a name="scaling-compute"></a>Skala beräkning

Du kan skala ut eller skala upp beräkningen genom att justera inställningen för [data lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) för SQL-poolen. Att läsa in och fråga prestanda kan öka linjärt när du lägger till fler informations lager enheter. 

Instruktioner för att skala ut finns i snabb starterna för [Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)eller [T-SQL](quickstart-scale-compute-tsql.md) . Du kan också utföra skalnings åtgärder med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

För att utföra en skalnings åtgärd omsorg SQL-poolen först alla inkommande frågor och återställer sedan transaktionerna för att säkerställa ett konsekvent tillstånd. Skalning sker bara när transaktions återställningen har slutförts. Vid en skalnings åtgärd kopplar systemet bort lagrings lagret från Compute-noderna, lägger till datornoderna och kopplar sedan om lagrings skiktet till beräknings skiktet. Varje SQL-pool lagras som 60-distributioner, som är jämnt distribuerade till datornoderna. Om du lägger till fler Compute-noder ökar beräknings kraften. Vartefter antalet beräknade noder ökar minskar antalet distributioner per Compute-nod och ger mer data bearbetnings kraft för dina frågor. På samma sätt minskar antalet beräknade data lager enheter antalet datornoder, vilket minskar beräknings resurserna för frågor.

Följande tabell visar hur antalet distributioner per beräknings nod ändras när data lagrets enheter ändras.  DW30000c tillhandahåller 60 Compute-noder och ger mycket högre frågeresultat än DW100c. 

| Informationslagerenheter  | \# av datornoder | \# av distributioner per nod |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Hitta rätt storlek på informations lager enheter

Om du vill se prestanda fördelarna med att skala ut, särskilt för större informations lager enheter, vill du använda minst en data uppsättning på 1 TB. Prova att skala upp och ned för att hitta det bästa antalet informations lager enheter för SQL-poolen. Kör några frågor med olika antal informations lager enheter när du har läst in dina data. Eftersom skalning är snabbt kan du prova olika prestanda nivåer på en timme eller mindre. 

Rekommendationer för att hitta det bästa antalet informations lager enheter:

- Börja med att välja ett mindre antal informations lager enheter för en SQL-pool i utvecklingen.  En lämplig start punkt är DW400c eller DW200c.
- Övervaka programmets prestanda och se hur många data lager enheter som valts jämfört med den prestanda du har.
- Anta en linjär skala och fastställ hur mycket du behöver för att öka eller minska data lager enheterna. 
- Fortsätt att göra justeringar tills du når en optimal prestanda nivå för dina affärs behov.

## <a name="when-to-scale-out"></a>När du ska skala ut

Att skala ut data lager enheter påverkar dessa aspekter av prestanda:

- Förbättrar systemets prestanda linjärt för genomsökningar, agg regeringar och CTAS-uttryck.
- Ökar antalet läsare och skribenter för att läsa in data.
- Maximalt antal samtidiga frågor och samtidiga platser.

Rekommendationer för när du ska skala ut data lager enheter:

- Innan du utför en tung data inläsnings-eller omvandlings åtgärd kan du skala ut för att göra data tillgängliga snabbare.
- Vid hög arbets tid kan du skala ut så att det tar upp fler mängder samtidiga frågor. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Vad händer om skala ut inte förbättrar prestandan?

Genom att lägga till informations lager enheter ökar du parallellt. Om arbetet jämnt delas mellan Compute-noderna, förbättrar den ytterligare parallellen frågans prestanda. Om skala ut inte ändrar prestandan finns det några orsaker till varför detta kan inträffa. Dina data kan skevas över distributionerna eller också kan frågor introducera en stor mängd data förflyttningar. Information om hur du undersöker frågor om prestanda finns i [fel sökning av prestanda](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pausa och återuppta beräkning

Om du pausar beräkning blir lagrings lagret frånkopplat från Compute-noderna. Beräknings resurserna släpps från ditt konto. Du debiteras inte för Compute medan Compute har pausats. Om du återupptar beräknings-och återaktiverar det lagrings utrymmet till datornoderna och återupptar avgifter för beräkning. När du pausar en SQL-pool:

* Beräknings-och minnes resurser returneras till poolen med tillgängliga resurser i data centret
* Kostnaderna för data lagrets enheter är noll under pausens varaktighet.
* Data lagringen påverkas inte och dina data förblir intakta. 
* Alla pågående eller köade åtgärder avbryts.

När du återupptar en SQL-pool:

* SQL-poolen hämtar beräknings-och minnes resurser för inställningen för data lager enheter.
* Beräknings avgifter för dina data lager enheter återupptas.
* Dina data blir tillgängliga.
* När SQL-poolen är online måste du starta om dina arbets belastnings frågor.

Om du alltid vill att din SQL-pool ska vara tillgänglig kan du skala ned den till den minsta storleken i stället för att pausa. 

Anvisningar för att pausa och återuppta finns i [Azure Portal](pause-and-resume-compute-portal.md)eller [PowerShell](pause-and-resume-compute-powershell.md) snabb starter. Du kan också använda [paus REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) eller [återuppta REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tömma transaktioner före pausning eller skalning

Vi rekommenderar att du låter befintliga transaktioner slutföras innan du startar en paus-eller skalnings åtgärd.

När du pausar eller skalar SQL-poolen avbryter du frågorna i bakgrunden när du startar paus-eller skalnings förfrågan. Att avbryta en enkel SELECT-fråga är en snabb åtgärd och påverkar nästan inte alls den tid det tar att pausa eller skala instansen.  Transaktionsfrågor, som ändrar data eller datastrukturen, kan däremot ta längre tid att stoppa. **Transaktionsfrågor måste per definition slutföras i sin helhet eller så måste ändringarna återställas.** Det kan ta lång tid att återställa arbetet som en transaktionsfråga har utfört, till och med längre tid än den ursprungliga ändringen som frågan tillämpade. Om du till exempel avbryter en fråga som tog bort rader och som redan har körts i en timme, kan det ta en timme för systemet att lägga till de borttagna raderna igen. Om du pausar eller skalar under pågående transaktioner kan det verka som åtgärden tar lång tid eftersom pausningen och skalningen måste vänta tills återställningen har slutförts innan de kan fortsätta.

Se även [förstå transaktioner](sql-data-warehouse-develop-transactions.md)och [optimera transaktioner](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisera beräknings hantering

Information om hur du automatiserar beräknings hanterings åtgärderna finns i [Hantera beräkning med Azure Functions](manage-compute-with-azure-functions.md).

Var och en av åtgärderna för att skala ut, pausa och återuppta kan ta flera minuter att slutföra. Om du skalar, pausar eller återupptar automatiskt rekommenderar vi att du implementerar logik för att säkerställa att vissa åtgärder har slutförts innan du fortsätter med en annan åtgärd. Genom att kontrol lera statusen för SQL-poolen via olika slut punkter kan du implementera automatisering av sådana åtgärder på ett korrekt sätt. 

Om du vill kontrol lera status för SQL-poolen läser du snabb starten för [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) eller [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) . Du kan också kontrol lera status för SQL-poolen med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Behörigheter

Skalning av SQL-poolen kräver behörighet som beskrivs i [Alter Database](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pausa och återuppta kräver behörigheten [SQL DB-deltagare](../role-based-access-control/built-in-roles.md#sql-db-contributor) , särskilt Microsoft. SQL/Servers/databaser/åtgärd.


## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar beräknings resurser för att [Hantera](manage-compute-with-azure-functions.md) beräknings resurser är att allokera olika beräknings resurser för enskilda frågor. Mer information finns i [resurs klasser för hantering av arbets belastning](resource-classes-for-workload-management.md).
