---
title: Azure SQL Database skala resurser | Microsoft Docs
description: Den här artikeln förklarar hur du skalar din databas genom att lägga till eller ta bort tilldelade resurser.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 49743130966589cceedb7756540c723a6f3276ff
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55471675"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamiskt skala databasresurser med minimal avbrottstid

Azure SQL Database kan du dynamiskt lägga till fler resurser till din databas med minimal avbrottstid.

## <a name="overview"></a>Översikt

När efterfrågan på din app växer från ett fåtal enheter och kunder till miljontals, skalar Azure SQL Database direkt med minimal avbrottstid. Skalbarhet är en av de viktigaste egenskaperna av PaaS som kan du dynamiskt lägga till fler resurser till din tjänst vid behov. Azure SQL Database kan du enkelt ändra resurser (processorkraft, minne, i/o-dataflöde och lagring) som allokerats till databasen.

Du kan minimera prestandaproblem på grund av ökad användning av ditt program som inte kan åtgärdas med indexering eller fråga efter omarbetning metoder. Lägga till fler resurser kan du snabbt reagera när din databas kommer de aktuella resursbegränsningar och behöver mer kraft att hantera inkommande arbetsbelastningen. Azure SQL Database kan du skala ned resurserna när de inte behövs sänker kostnaderna.

Du behöver inte bekymra dig om att köpa maskinvara och ändra underliggande infrastruktur. Skala databasen kan enkelt utföras via Azure-portalen med ett skjutreglage.

![Skala databasens prestanda](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database erbjuder den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md).

- Den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) erbjuder en kombination av beräkning, minne och IO-resurser i tre tjänstnivåer för att stödja lätta till tunga arbetsbelastningar: Basic, Standard och Premium. Det finns prestandanivåer inom varje nivå med en blandning av dessa resurser, och du kan lägga till ytterligare lagringsresurser till dessa.
- Den [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) kan du välja antal virtuella kärnor, hur mycket eller minne, och mycket och snabbt lagringsutrymme. Den här inköpsmodellen erbjuder tre tjänstnivåer: Allmänt syfte, affärskritisk och hyperskala (förhandsversion).

Du kan skapa din första app på en liten, enkel databas till en låg kostnad per månad på tjänstnivån Basic, Standard- eller generell användning och sedan ändra dess tjänstnivå manuellt eller programmässigt när som helst på Premium- eller affärskritiska tjänstnivån att uppfylla ne EDS i lösningen. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

> [!IMPORTANT]
> Hyperskala tjänstnivå är för närvarande i offentlig förhandsversion och är tillgängliga i begränsade Azure-regioner. Du kan inte uppdatera en storskalig databas till andra tjänstnivåer. För testning syfte rekommenderar vi du gör en kopia av den aktuella databasen och uppdatera kopian hyperskala tjänstnivån.
> [!NOTE]
> Dynamisk skalbarhet skiljer sig från autoskalning. Med Autoskala avses när en tjänst skalar automatiskt utifrån olika kriterier, medan dynamisk skalbarhet möjliggör manuell skalning utan avbrott.

Enskilda Azure SQL-databaser stöder manuell dynamisk skalbarhet, men inte Autoskala. Om du vill ha en mer *automatisk* upplevelse bör du använda elastiska pooler, vilka tillåter databaser att dela resurser i en pool utifrån enskilda databasbehov.
Det finns dock skript som kan hjälpa dig att automatisera skalbarhet för en Azure SQL-databas. Mer information finns i [Använd PowerShell till att övervaka och skala en enskild SQL-databas](scripts/sql-database-monitor-and-scale-database-powershell.md).

Du kan ändra [DTU tjänstnivåerna](sql-database-service-tiers-dtu.md) eller [vCore egenskaper](sql-database-vcore-resource-limits-single-databases.md) när som helst med minimalt avbrott för ditt program (normalt mindre än fyra sekunder). För många företag och appar räcker det att kunna skapa databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. Det här scenariot kan använda du en elastisk pool med ett visst antal edtu: er som delas mellan flera databaser i poolen.

![Introduktion till SQL Database: Enkel databas dtu: er efter nivå](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Alla tre varianter av Azure SQL Database erbjuder vissa möjlighet att dynamiskt skala dina databaser:

- Med en [enkel databas](sql-database-single-database-scale.md), du kan använda antingen [DTU](sql-database-dtu-resource-limits-single-databases.md) eller [vCore](sql-database-vcore-resource-limits-single-databases.md) modeller för att definiera maximal mängd resurser som ska tilldelas varje databas.
- En [Managed Instance](sql-database-managed-instance.md) använder [vCores](sql-database-managed-instance.md#vcore-based-purchasing-model) läge och du kan definiera högsta CPU-kärnor och maximalt lagringsutrymme som allokerats till din instans. Alla databaser i instansen kommer att dela resurser allokeras till instansen.
- [Elastiska pooler](sql-database-elastic-pool-scale.md) kan du definiera högsta gräns för varje grupp med databaser i poolen.

## <a name="alternative-scale-methods"></a>Skala alternativa metoder

Skalning av resurser är den enklaste och det mest effektiva sättet att förbättra databasens prestanda utan att ändra koden för databasen eller programmet. I vissa fall kan kan även de högsta tjänstnivåer, storlekar och prestandaoptimering inte hantera din arbetsbelastning på lyckad och kostnadseffektivt sätt. I de fall har dessa ytterligare alternativ för att skala din databas:

- [Lässkalning](sql-database-read-scale-out.md) är en funktion som är tillgängliga i där du får en skrivskyddad replik av dina data där du kan köra krävande skrivskyddade frågor, till exempel rapporter. Skrivskyddad replik hanterar din skrivskyddad arbetsbelastning utan att påverka resursanvändning på den primära databasen.
- [Database sharding](sql-database-elastic-scale-introduction.md) är en uppsättning tekniker som gör det möjligt att dela dina data i flera databaser och skalas oberoende av varandra.

## <a name="next-steps"></a>Nästa steg

- Information om att förbättra databasens prestanda genom att ändra databasen koden finns i [hitta och tillämpa prestandarekommendationer](sql-database-advisor-portal.md).
- Information om att låta inbyggd databas intelligence Optimera databasen finns i [automatisk justering](sql-database-automatic-tuning.md).
- Information om lässkalning i Azure SQL Database-tjänsten finns i så här [använda skrivskyddade repliker att läsa in saldo skrivskyddad frågearbetsbelastningar](sql-database-read-scale-out.md).
- Information om en databas horisontell partitionering finns i [skala ut med Azure SQL Database](sql-database-elastic-scale-introduction.md).
