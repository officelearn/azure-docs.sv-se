---
title: Översikt över automatisk justering
description: Azure SQL Database analyserar SQL-fråga och anpassar automatiskt efter användarens arbets belastning.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/30/2020
ms.openlocfilehash: 7488fd5f8a572788933856f03bb0ad4351885704
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80518227"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatisk justering i Azure SQL Database

Azure SQL Database automatisk justering ger högsta prestanda och stabila arbets belastningar genom kontinuerlig prestanda justering baserat på AI och maskin inlärning.

Automatisk justering är en helt hanterad intelligent prestanda tjänst som använder inbyggd intelligens för att kontinuerligt övervaka frågor som utförs på en databas och den förbättrar prestandan automatiskt. Detta uppnås genom dynamisk anpassning av databasen till förändringar av arbets belastningarna och att tillämpa justerings rekommendationer. Automatisk justering lär sig vågrätt från alla databaser i Azure via AI och förbättrar justerings åtgärderna dynamiskt. Den längre en databas körs med automatisk justering på, desto bättre utförs.

Azure SQL Database automatisk justering kan vara en av de viktigaste funktionerna som du kan aktivera för att tillhandahålla stabila och högsta databas arbets belastningar.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vad kan automatisk justering göras för

- Automatisk prestanda justering av Azure SQL-databaser
- Automatiserad verifiering av prestanda vinster
- Automatisk återställning och själv korrigering
- Justerings historik
- Justerings åtgärd T-SQL-skript för manuella distributioner
- Övervakning av proaktiva arbets belastnings prestanda
- Skala ut kapacitet på hundratals tusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägande kostnaden

## <a name="safe-reliable-and-proven"></a>Säkert, tillförlitligt och beprövat

Justerings åtgärder som tillämpas på databaser i Azure SQL Database är helt säkra för prestanda för dina mest intensiva arbets belastningar. Systemet har utformats med bryr sig om att inte störa användar arbets belastningarna. Automatiska justerings rekommendationer tillämpas bara vid tidpunkten för en låg belastning. Systemet kan också tillfälligt inaktivera automatiska justerings åtgärder för att skydda arbets belastnings prestandan. I sådana fall visas meddelandet "inaktiverat av systemet" i Azure Portal. Automatisk justering avser arbets belastningar med den högsta resurs prioriteten.

Automatiska justerings mekanismer är mogna och har blivit utmärkta för flera miljoner databaser som körs på Azure. Automatiska justerings åtgärder som tillämpas verifieras automatiskt för att se till att det finns en positiv förbättring av arbets belastnings prestandan. Prestanda rekommendationerna för försämrat identifieras dynamiskt och snabbt. Genom den registrerade justerings historiken finns det en tydlig spårning av de justerings förbättringar som gjorts för varje Azure SQL Database.

![Hur fungerar automatisk justering?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatisk justering delar sin kärn logik med SQL Server automatisk justerings motor. Ytterligare teknisk information om den inbyggda intelligens mekanismen finns [SQL Server automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

En översikt över hur automatisk justering fungerar och för vanliga användnings scenarier finns i den inbäddade videon:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

Du kan [Aktivera automatisk justering för enskilda databaser och grupperade databaser i Azure Portal](sql-database-automatic-tuning-enable.md) eller använda instruktionen [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL. Du aktiverar automatisk justering för instans databaser i en hanterad instans distribution med instruktionen [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL.

## <a name="automatic-tuning-options"></a>Alternativ för automatisk justering

Alternativen för automatisk justering är tillgängliga i Azure SQL Database:

| Alternativ för automatisk justering | Stöd för enkel databas och poolad databas | Stöd för instans databas |
| :----------------------------- | ----- | ----- |
| **Skapa index** – identifierar index som kan förbättra prestandan för din arbets belastning, skapar index och automatiskt verifierar att prestandan för frågor har förbättrats. | Ja | Nej |
| **Drop index** – identifierar redundanta och duplicerade index dagligen, förutom unika index och index som inte har använts under en längre tid (>90 dagar). Observera att det här alternativet inte är kompatibelt med program som använder partitions växlings-och index tips. Det går inte att släppa oanvända index för Premium-och Affärskritisk tjänst nivåer. | Ja | Nej |
| **FRAMTVINGA senaste fungerande plan** (automatisk plan korrigering) – identifierar SQL-frågor med hjälp av en körnings plan som är långsammare än den tidigare fungerande planen, och frågor som använder det senaste fungerande schemat i stället för försämrat-planen. | Ja | Ja |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Automatisk justering för enskilda databaser och databaser i pooler

Automatisk justering för enskilda databaser och grupperade databaser använder rekommendationerna **skapa index**, **Drop index**och **tvinga senaste schema** för databas rådgivare för att optimera databasens prestanda. Mer information finns i [rekommendationer för databas rådgivare i Azure Portal](sql-database-advisor-portal.md), i [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)och i [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Du kan antingen manuellt tillämpa justerings rekommendationer med hjälp av Azure Portal eller så kan du låta automatisk justering tillämpa justerings rekommendationer åt dig. Fördelarna med att låta systemet tillämpa justerings rekommendationer för dig är att den automatiskt validerar att det finns en positiv vinst för arbets belastnings prestandan, och om det inte finns någon betydande prestanda förbättring, återställs justerings rekommendationen automatiskt. Observera att om frågor påverkas av justerings rekommendationer som inte körs ofta kan validerings fasen ta upp till 72 timmar efter design.

Om du använder justerings rekommendationer via T-SQL, är den automatiska prestanda valideringen och återförings metoderna inte tillgängliga. Rekommendationer som tillämpas på detta sätt förblir aktiva och visas i listan över justerings rekommendationer för 24-48 timmar. innan systemet återkallar dem automatiskt. Om du vill ta bort en rekommendation tidigare kan du ta bort den från Azure Portal.

Automatiska justerings alternativ kan aktive ras oberoende eller inaktive ras per databas, eller så kan de konfigureras på SQL Database servrar och tillämpas på alla databaser som ärver inställningar från servern. SQL Database servrar kan ärva Azure-standardvärden för automatiska justerings inställningar. Azure-standardvärden för tillfället är inställt på FORCE_LAST_GOOD_PLAN är aktiverat, CREATE_INDEX är aktiverat och DROP_INDEX har inaktiverats.

> [!IMPORTANT]
> Från och med mars kommer 2020 ändringar i Azure-standardvärden för automatisk justering att gälla enligt följande:
>
> - Nya Azure-standardvärden är FORCE_LAST_GOOD_PLAN = Enabled, CREATE_INDEX = disabled och DROP_INDEX = inaktive rad.
> - Befintliga servrar utan inställningar för automatisk justering konfigureras automatiskt för att ärva nya Azure-standardvärden. Detta gäller för alla kunder som för närvarande har Server inställningar för automatisk justering i ett odefinierat tillstånd.
> - Nya servrar som skapas konfigureras automatiskt för att ärva nya Azure-standardvärden (till skillnad från tidigare när konfigurationen för automatisk justering var i ett odefinierat tillstånd när en ny server skapas).

Att konfigurera alternativ för automatisk justering på en server och ärva inställningar för databaser som tillhör den överordnade servern är en rekommenderad metod för att konfigurera automatisk justering eftersom det fören klar hanteringen av automatiska justerings alternativ för ett stort antal databaser.

Information om hur du skapar e-postaviseringar för automatiska justerings rekommendationer finns i [e-postaviseringar för automatisk justering](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Automatisk justering för instans databaser

Automatisk justering för instans databaser i en hanterad instans distribution stöder endast **tvångs sista effektiva plan**. Mer information om hur du konfigurerar alternativ för automatisk justering via T-SQL finns i [Automatisk justering inför automatisk plan korrigering](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) och [Automatisk plan korrigering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction).

## <a name="next-steps"></a>Nästa steg

- Om du vill lära dig mer om inbyggd intelligens som används i automatisk justering, se [artificiell intelligens justerar Azure SQL-databaser](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Information om hur automatisk justering fungerar under huven finns i avsnittet [om att indexera miljon tals databaser automatiskt i Microsoft Azure SQL Database](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).
