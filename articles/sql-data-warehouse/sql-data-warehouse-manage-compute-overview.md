---
title: Hantera beräkningsresurs i Azure SQL Data Warehouse | Microsoft Docs
description: Läs mer om prestanda skalbarhet i Azure SQL Data Warehouse. Skala ut genom att justera Informationslagerenheter eller lägre kostnader genom att pausa informationslagret.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 47be738a4e5dcec144d482c28e39cbe950bba3e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748942"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Hantera beräkning i Azure SQL Data Warehouse
Lär dig mer om hur du hanterar beräkningsresurser i Azure SQL Data Warehouse. Lägre kostnader genom att pausa informationslagret, eller skala i datalagret för att uppfylla krav på prestanda. 

## <a name="what-is-compute-management"></a>Vad är compute management?
Arkitekturen för SQL Data Warehouse håller isär lagring och beräkning, vilket gör att de kan skalas oberoende av varandra. Därför kan du skala beräkning för att uppfylla prestandakraven som är oberoende av datalagring. Du kan också pausa och återuppta beräkningsresurser. En naturlig följd av den här arkitekturen är att [fakturering](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) för beräkning och lagring är separat. Om du inte behöver använda ditt informationslager ett tag, kan du spara beräkningskostnader genom att pausa databearbetning. 

## <a name="scaling-compute"></a>Skala beräkning
Du kan skala ut eller skala tillbaka databearbetning genom att justera den [data informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) för ditt informationslager. Läser in och frågeprestanda ökar linjärt när du lägger till flera informationslagerenheter. 

Skala ut anvisningar finns i den [Azure-portalen](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), eller [T-SQL](quickstart-scale-compute-tsql.md) snabbstarter. Du kan också utföra skalbar åtgärder med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Om du vill utföra en skalningsåtgärd SQL Data Warehouse först stoppar alla inkommande frågor och sedan återställer transaktioner för att säkerställa ett konsekvent tillstånd. Skalning sker bara när transaktionen återställningen är klar. För en skalningsåtgärd systemet kopplas lagringsskikt från beräkningsnoderna, lägger till Compute-noder och återansluts sedan lagringsskikt i Compute-lagret. Varje datalager lagras som 60 distributioner som är jämnt distribuerade till beräkningsnoderna. Att lägga till flera beräkningsnoder lägger du till mer datorkraft. När antalet beräkningsnoder ökar, minskar antalet distributioner per beräkningsnod att tillhandahålla mer beräkningskapacitet för dina frågor. På samma sätt minskar minska informationslagerenheter antalet beräkningsnoder, vilket minskar beräkningsresurserna för frågor.

I följande tabell visar hur antalet distributioner per beräkning noden ändras allteftersom informationslagerenheter ändras.  DWU6000 ger 60 Compute-noder och ger mycket högre frågeprestanda än DWU100. 

| Informationslagerenheter  | \# med beräkningsnoder | \# per nod-distributioner |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Hitta rätt storlek för informationslagerenheter

Om du vill se prestandafördelarna med utskalning, särskilt för större informationslagerenheter, som du vill använda minst en datauppsättning för 1 TB. Om du vill hitta bästa antalet informationslagerenheter för informationslagret, försök att skala upp och ned. Köra några frågor med olika antal informationslagerenheter när du har läst in dina data. Eftersom skalningen är snabb kan prova du olika prestandanivåer på en timme eller mindre. 

Rekommendationer för att hitta bästa antalet informationslagerenheter:

- Börja genom att välja ett mindre antal informationslagerenheter för ett informationslager i utveckling.  En bra utgångspunkt är DW400 eller DW200.
- Övervaka programprestanda, bakgrund av antalet informationslagerenheter valt jämfört med den prestanda du se.
- Anta att en linjär skala och avgöra hur mycket du behöva öka eller minska informationslagerenheter. 
- Fortsätt att göra justeringar tills du når nivån optimala prestanda för dina affärsbehov.

## <a name="when-to-scale-out"></a>När du vill skala ut
Skala ut informationslagerenheter påverkar följande aspekter av prestanda:

- Linjärt förbättrar prestanda för genomsökningar och aggregeringar CTAS-uttryck.
- Ökar antalet läsare och skrivare för inläsning av data.
- Maximalt antal samtidiga frågor och samtidighetsfack.

Rekommendationer för när du vill skala ut data warehouse-enheter:

- Innan du utför en tung datainläsnings- eller omvandlingsåtgärd åtgärd kan du skala ut för att göra data tillgängliga snabbare.
- Under belastning, skalas för att hantera större antal samtidiga frågor. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Vad händer om skala ut förbättrar inte prestandan?

Lägger till informationslagerenheter ökar parallellitet. Om arbetet fördelas jämnt mellan Compute-noder, förbättrar ytterligare parallellitet frågeprestanda. Om din prestanda inte ändras skala ut, finns det några orsaker till varför detta kan inträffa. Dina data kan vara förvrängd över distributioner eller frågor introducerar en stor del av dataförflyttning. Om du vill undersöka prestandaproblem för fråga, se [prestandafelsökning](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pausa och återuppta beräkning
Pausa databearbetning orsakar lagringsskikt kopplar du bort från beräkningsnoderna. Beräkningsresurser blir tillgängliga från ditt konto. Du debiteras inte för beräkning när beräkningen pausas. Återupptagande av beräkning måldisken lagring till Compute-noder och återupptar avgifter för beräkning. När du pausar ett informationslager:

* Beräknings-och minnesresurser returneras till poolen med tillgängliga resurser i datacentret
* Data warehouse unit kostnaderna är noll för varaktighet för pausen.
* Datalagring påverkas inte och dina data förblir intakta. 
* SQL Data Warehouse avbryter alla åtgärder för som körs eller står i kö.

När du återuppta ett informationslager:

* SQL Data Warehouse skaffar beräknings-och minnesresurser för din informationslagerenheter inställningen.
* -Avgifter för dina data warehouse units återupptagning.
* Dina data blir tillgängliga.
* När datalagret är online kan behöva du starta om din arbetsbelastningsfrågor.

Om du vill att ditt data warehouse tillgänglig kan du skala ned den minsta storleken det i stället för att pausa. 

För pausa och återuppta stegen kan se den [Azure-portalen](pause-and-resume-compute-portal.md), eller [PowerShell](pause-and-resume-compute-powershell.md) snabbstarter. Du kan också använda den [pausa REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) eller [återuppta REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tömma transaktioner före pausning eller skalning
Vi rekommenderar så att befintliga transaktioner ska slutföras innan du startar en pausa eller skala åtgärd.

När du pausar eller skalar SQL Data Warehouse avbryts dina frågor i bakgrunden när du initierar paus- eller skalningsbegäran.  Att avbryta en enkel SELECT-fråga är en snabb åtgärd och påverkar nästan inte alls den tid det tar att pausa eller skala instansen.  Transaktionsfrågor, som ändrar data eller datastrukturen, kan däremot ta längre tid att stoppa.  **Transaktionsfrågor måste per definition slutföras i sin helhet eller så måste ändringarna återställas.**  Det kan ta lång tid att återställa arbetet som en transaktionsfråga har utfört, till och med längre tid än den ursprungliga ändringen som frågan tillämpade.  Om du till exempel avbryter en fråga som tog bort rader och som redan har körts i en timme, kan det ta en timme för systemet att lägga till de borttagna raderna igen.  Om du pausar eller skalar under pågående transaktioner kan det verka som åtgärden tar lång tid eftersom pausningen och skalningen måste vänta tills återställningen har slutförts innan de kan fortsätta.

Se även [förstå transaktioner](sql-data-warehouse-develop-transactions.md), och [optimera transaktioner](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisera hantering av beräkning
Om du vill automatisera hanteringsåtgärder för beräkning, se [hantera beräkning med Azure functions](manage-compute-with-azure-functions.md).

Var och en av de skalbar, pausa och återuppta åtgärder kan ta flera minuter att slutföra. Om du skalar, pausar, eller återupptar automatiskt, rekommenderar vi att implementera logik för att se till att har vissa åtgärder slutförts innan du fortsätter med en annan åtgärd. Kontrollerar status för data warehouse via olika slutpunkter kan du implementera korrekt automatisering av sådana åtgärder. 

Du kan kontrollera tillstånd för datalagrets den [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) eller [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) Snabbstart. Du kan också kontrollera tillstånd för datalager med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Behörigheter

Skala datalagret kräver behörigheterna som beskrivs i [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pausa och återuppta kräver den [SQL DB-deltagare](../role-based-access-control/built-in-roles.md#sql-db-contributor) behörighet, särskilt Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Nästa steg
En annan aspekt med att hantera beräkningsresurser tilldela olika beräkningsresurser för enskilda frågor. Mer information finns i [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).
