---
title: Azure SQL Database skala resurser | Microsoft Docs
description: Den här artikeln förklarar hur du skalar din databas genom att lägga till eller ta bort tilldelade resurser.
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: a6b987d9815cfabed6dd986a0d9842a97f5b5868
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092060"
---
# <a name="scale-database-resources"></a>Skala databasresurser

Azure SQL Database kan du dynamiskt lägga till fler resurser till din databas med minimal avbrottstid.

## <a name="overview"></a>Översikt

När efterfrågan på din app växer från ett fåtal enheter och kunder till miljontals, skalar Azure SQL Database direkt med minimal avbrottstid. Skalbarhet är en av de viktigaste egenskaperna av PaaS som kan du dynamiskt lägga till fler resurser till din tjänst vid behov. Azure SQL Database kan du enkelt ändra resurser (processorkraft, minne, i/o-dataflöde och lagring) som allokerats till databasen.  
Du kan minimera prestandaproblem på grund av ökad användning av ditt program som inte kan åtgärdas med indexering eller fråga efter omarbetning metoder. Lägga till fler resurser kan du snabbt reagera när din databas kommer de aktuella resursbegränsningar och behöver mer kraft att hantera inkommande arbetsbelastningen. Azure SQL Database kan du skala ned resurserna när de inte behövs sänker kostnaderna.
Du behöver inte bekymra dig om att köpa maskinvara och ändra underliggande infrastruktur. Skala databasen kan enkelt utföras via Azure-portalen med ett skjutreglage.

![Skala databasens prestanda](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database erbjuder en [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md). 
-   Den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) erbjuder en kombination av beräkning, minne och IO-resurser i tre tjänstnivåer för att stödja lätta till tunga arbetsbelastningar: Basic, Standard och Premium. Det finns prestandanivåer inom varje nivå med en blandning av dessa resurser, och du kan lägga till ytterligare lagringsresurser till dessa.
-   Den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) kan du välja antal virtuella kärnor, hur mycket eller minne, och mycket och snabbt lagringsutrymme.
Du kan skapa din första app på en liten, enkel databas för en låg månadskostnad och sedan ändra dess tjänstnivå manuellt eller programmässigt när som helst för att uppfylla behoven i din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.


> [!NOTE]
> Dynamisk skalbarhet skiljer sig från autoskalning. Med Autoskala avses när en tjänst skalar automatiskt utifrån olika kriterier, medan dynamisk skalbarhet möjliggör manuell skalning utan avbrott.
>


Enskilda Azure SQL-databaser stöder manuell dynamisk skalbarhet, men inte Autoskala. Om du vill ha en mer *automatisk* upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov.
Det finns dock skript som kan hjälpa dig att automatisera skalbarhet för en Azure SQL-databas. Mer information finns i [Använd PowerShell till att övervaka och skala en enskild SQL-databas](scripts/sql-database-monitor-and-scale-database-powershell.md).


Alla tre varianter av Azure SQL Database erbjuder vissa möjlighet att dynamiskt skala dina databaser:
-   I [Azure SQL-databas](sql-database-single-database-scale.md), du kan använda antingen [DTU](sql-database-dtu-resource-limits-single-databases.md) eller [vCore](sql-database-vcore-resource-limits-single-databases.md) modeller för att definiera maximal mängd resurser som ska tilldelas varje databas.
-   [Azure SQL Managed Instance](sql-database-managed-instance.md) använder [vCores](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) läge och du kan definiera högsta CPU-kärnor och maximalt lagringsutrymme som allokerats till din instans. Alla databaser i instansen kommer att dela resurser allokeras till instansen.
-   [Azure SQL-elastiska pooler](sql-database-elastic-pool-scale.md) kan du definiera högsta gräns för varje grupp med databaser i poolen.

## <a name="alternative-scale-methods"></a>Skala alternativa metoder
Skalning av resurser är den enklaste och det mest effektiva sättet att förbättra databasens prestanda utan att ändra koden för databasen eller programmet.
I vissa fall kan kan även högsta prestandanivåer och prestandaoptimering inte hantera din arbetsbelastning på lyckad och kostnadseffektivt sätt. I de fall har du andra alternativ för att skala din databas:
-   [Lässkalning](sql-database-read-scale-out.md) är en funktion som är tillgängliga i där du får en skrivskyddad replik av dina data där du kan köra krävande skrivskyddade frågor, till exempel rapporter. Repliken i enbart Red hanterar din skrivskyddad arbetsbelastning utan att påverka resursanvändning på den primära databasen.
-   [Database sharding](sql-database-elastic-scale-introduction.md) är en uppsättning tekniker som gör det möjligt att dela dina data i flera databaser och skalas oberoende av varandra.

## <a name="next-steps"></a>Nästa steg
- Information om att förbättra databasens prestanda genom att ändra databasen koden finns i [hitta och tillämpa prestandarekommendationer](sql-database-advisor-portal.md).
- Information om att låta inbyggd databas intelligence Optimera databasen finns i [automatisk justering](sql-database-automatic-tuning.md).
- Information om lässkalning i Azure SQL Database-tjänsten finns i så här [använda skrivskyddade repliker att läsa in saldo skrivskyddad frågearbetsbelastningar](sql-database-read-scale-out.md).
- Information om en databas horisontell partitionering finns i [skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).

