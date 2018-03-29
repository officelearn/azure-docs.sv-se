---
title: Hantera beräkningsresurser i Azure SQL Data Warehouse | Microsoft Docs
description: 'Mer information om prestanda skala ut funktioner i Azure SQL Data Warehouse. Skala ut genom att justera dwu: er eller lägre kostnader genom att pausa datalagret.'
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: ''
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/20/2018
ms.author: elbutter
ms.openlocfilehash: c34e37f0c6393c65d4b60705012769608bb7395b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Hantera beräkning i Azure SQL Data Warehouse
Lär dig mer om hur du hanterar beräkningsresurser i Azure SQL Data Warehouse. Lägre kostnader genom att pausa datalagret eller skala datalagret för att uppfylla krav på prestanda. 

## <a name="what-is-compute-management"></a>Vad är beräkning management?
Arkitekturen för SQL Data Warehouse separerar lagring och beräkning, så att varje del kan skalas oberoende av varandra. Därför kan du skala beräknings för att uppfylla prestandakrav oberoende av datalagring. Du kan också pausa och återuppta beräkningsresurser. En naturlig följd av den här arkitekturen är att [fakturering](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) för beräkning och lagring är desamma. Om du inte behöver använda ditt data warehouse på ett tag, kan du spara kostnader för beräkning av pausa beräkning. 

## <a name="scaling-compute"></a>Skala beräkning
Du kan skala upp eller skala tillbaka beräkning genom att justera det [datalager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) för ditt informationslager. Läser in och frågeprestanda kan öka linjärt när du lägger till flera informationslagerenheter. SQL Data Warehouse erbjuder [servicenivåer](performance-tiers.md#service-levels) för data warehouse-enheter som garanterar en tydlig förändring i prestanda när du skalar ut eller tillbaka. 

Skalbar anvisningar finns i [Azure-portalen](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), eller [T-SQL](quickstart-scale-compute-tsql.md) Snabbstart. Du kan också utföra skalbar åtgärder med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Om du vill utföra en skalningsåtgärden SQL Data Warehouse först stoppar alla inkommande förfrågningar och sedan återställer transaktioner för att säkerställa ett konsekvent tillstånd. Skalning inträffar bara när transaktionen återställningen är klar. För en skalningsåtgärden systemet kopplas från lagringsskikt från datornoderna, lägger till Compute-noderna och reattaches lagringsskikt beräknings-lagret. Varje datalagret lagras som 60-distributioner fördelas jämnt till Compute-noder. Om du lägger till flera datornoderna läggs mer datorkraft. När antalet datornoderna ökar, minskar antalet distributioner per beräkningsnod tillhandahåller mer datorkraft för dina frågor. På samma sätt minskar minskar informationslagerenheter antalet Compute-noder, vilket minskar beräkningsresurser för frågor.

Följande tabell visar hur många distributioner per Compute-nod ändringar som data warehouse enheter ändras.  DWU6000 ger 60 datornoder och ger mycket högre frågeprestanda än DWU100. 

| Informationslagerenheter  | \# med beräkningsnoder | \# för distributioner per nod |
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

Om du vill se prestandafördelarna med skalning, särskilt för större informationslagerenheter som du vill använda minst 1 TB data. Försök för att hitta bästa antalet informationslagerenheter för ditt informationslager, skala upp och ner. Köra några frågor med olika antal informationslagerenheter när data har lästs in. Eftersom det går snabbt att skala, kan du försöka olika prestandanivåer i en timme eller mindre. 

Rekommendationer för att hitta det rekommenderade antalet data warehouse enheter:

- Börja genom att välja ett mindre antal informationslagerenheter för ett datalager under utveckling.  En bra utgångspunkt är DW400 eller DW200.
- Övervaka dina programprestanda sett antalet informationslagerenheter valt jämfört med prestanda du se.
- Anta en linjär skala och avgöra hur mycket du behöver öka eller minska data warehouse-enheter. 
- Fortsätt att göra justeringar tills du når nivån optimal prestanda för dina affärsbehov.

## <a name="when-to-scale-out"></a>När du ska skalas ut
Skala ut informationslagerenheter påverkar dessa aspekter av prestanda:

- Linjärt förbättrar prestanda för sökningar, aggregeringar och CTAS-uttryck.
- Ökar antalet läsare och skrivare för att läsa in data.
- Maximalt antal samtidiga frågor och samtidighet platser.

Rekommendationer när du vill skala ut data warehouse enheter:

- Innan du utför en tung datainläsnings- eller omvandlingsåtgärd åtgärd kan du skala upp så att data blir tillgängliga snabbare.
- Skala ut för ett större antal samtidiga frågor under belastning kontorstid. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Vad händer om skala ut förbättrar inte prestandan?

Lägger till informationslagerenheter öka parallellitet. Om arbetet delas jämnt mellan datornoderna, förbättrar ytterligare parallellitet frågeprestanda. Om skala ut inte ändras prestandan, finns det några orsaker till varför detta kan inträffa. Data kan vara förvrängd på distributioner eller frågor om stora mängder data flyttas. För att undersöka prestandaproblem för frågan finns [prestanda felsökning](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pausa och återuppta beräkning
Pausa beräkning gör lagringsskikt frånkoppling från Compute-noder. Beräkningsresurserna släpps från ditt konto. Du debiteras inte för beräkning medan beräkning har pausats. Återuppta beräkningen reattaches lagring till Compute-noderna och återupptar avgifter för beräkning. När du pausar ett datalager:

* Beräknings-och minnesresurser återgår till poolen med tillgängliga resurser i datacentret
* Data warehouse kostnader är noll för pausen varaktighet.
* Datalagring påverkas inte och dina data förblir intakt. 
* SQL Data Warehouse avbryter alla åtgärder som körs eller står i kö.

När du återupptar ett datalager:

* SQL Data Warehouse hämtar beräknings-och minnesresurser för din informationslagerenheter inställningen.
* Beräkna avgifter för dina data warehouse enheter återupptagning.
* Dina data blir tillgängliga.
* När datalagret är online, måste du starta om din arbetsbelastning frågor.

Om du vill att ditt data warehouse tillgänglig kan du skala ned till den minsta storleken i stället för att pausa. 

För pausa och återuppta steg, finns det [Azure-portalen](pause-and-resume-compute-portal.md), eller [PowerShell](pause-and-resume-compute-powershell.md) Snabbstart. Du kan också använda den [pausa REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) eller [återuppta REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Tömma transaktioner före pausning eller skalning
Vi rekommenderar att tillåta att befintliga transaktioner ska slutföras innan du startar en paus eller skala åtgärd.

När du pausar eller skalar SQL Data Warehouse avbryts dina frågor i bakgrunden när du initierar paus- eller skalningsbegäran.  Att avbryta en enkel SELECT-fråga är en snabb åtgärd och påverkar nästan inte alls den tid det tar att pausa eller skala instansen.  Transaktionsfrågor, som ändrar data eller datastrukturen, kan däremot ta längre tid att stoppa.  **Transaktionsfrågor måste per definition slutföras i sin helhet eller så måste ändringarna återställas.**  Det kan ta lång tid att återställa arbetet som en transaktionsfråga har utfört, till och med längre tid än den ursprungliga ändringen som frågan tillämpade.  Om du till exempel avbryter en fråga som tog bort rader och som redan har körts i en timme, kan det ta en timme för systemet att lägga till de borttagna raderna igen.  Om du pausar eller skalar under pågående transaktioner kan det verka som åtgärden tar lång tid eftersom pausningen och skalningen måste vänta tills återställningen har slutförts innan de kan fortsätta.

Se även [förstå transaktioner](sql-data-warehouse-develop-transactions.md), och [optimera transaktioner][optimera transaktioner](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisera hantering av beräkning
För att automatisera hanteringen beräkning finns [hantera beräkning med Azure functions](manage-compute-with-azure-functions.md).

Var och en av skalbara, pausa och återuppta åtgärder kan ta flera minuter att slutföra. Om du skalning, pausa, eller återupptar automatiskt, rekommenderar vi att implementera logik för att säkerställa att har vissa åtgärder slutförts innan du fortsätter med en annan åtgärd. Kontrollera tillstånd för datalager via olika slutpunkter kan du implementera korrekt automatisering av dessa åtgärder. 

Att kontrollera tillståndet för data warehouse, se den [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) eller [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) Snabbstart. Du kan också kontrollera tillstånd för datalager med en [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Behörigheter

Skalning datalagret kräver behörigheterna som beskrivs i [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse.md).  Pausa och återuppta kräver den [SQL DB-deltagare](../active-directory/role-based-access-built-in-roles.md#sql-db-contributor) behörighet, särskilt Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Nästa steg
En annan aspekt av hantering av beräkningsresurser allokerar olika beräkningsresurser för enskilda frågor. Mer information finns i [resursklasser för hantering av arbetsbelastning](resource-classes-for-workload-management.md).
