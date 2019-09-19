---
title: Azure SQL Database-automatisk justering | Microsoft Docs
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
ms.date: 03/06/2019
ms.openlocfilehash: d782c2d9dff45dc152cab2246c95dda063bfd900
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130393"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatisk justering i Azure SQL Database

Azure SQL Database automatisk justering ger högsta prestanda och stabila arbets belastningar genom kontinuerlig prestanda justering baserat på AI och maskin inlärning.

Automatisk justering är en helt hanterad intelligent prestanda tjänst som använder inbyggd intelligens för att kontinuerligt övervaka frågor som utförs på en databas och den förbättrar prestandan automatiskt. Detta uppnås genom dynamisk anpassning av databasen till förändringar av arbets belastningarna och att tillämpa justerings rekommendationer. Automatisk justering lär sig vågrätt från alla databaser i Azure via AI och förbättrar justerings åtgärderna dynamiskt. Den längre en Azure SQL Database körs med automatisk justering på, desto bättre utförs.

Azure SQL Database automatisk justering kan vara en av de viktigaste funktionerna som du kan aktivera för att tillhandahålla stabila och högsta databas arbets belastningar.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vad kan automatisk justering utföra?

- Automatisk prestanda justering av Azure SQL-databaser
- Automatiserad verifiering av prestanda vinster
- Automatisk återställning och själv korrigering
- Justeringshistorik
- Justerings åtgärd T-SQL-skript för manuella distributioner
- Övervakning av proaktiva arbets belastnings prestanda
- Skala ut kapacitet på hundratals tusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägande kostnaden

## <a name="safe-reliable-and-proven"></a>Säkert, tillförlitligt och beprövat

Justerings åtgärder som tillämpas på Azure SQL-databaser är fullständigt säkra för prestanda för dina mest intensiva arbets belastningar. Systemet har utformats med bryr sig om att inte störa användar arbets belastningarna. Automatiska justerings rekommendationer tillämpas bara vid tidpunkten för en låg belastning. Systemet kan också tillfälligt inaktivera automatiska justerings åtgärder för att skydda arbets belastnings prestandan. I sådana fall visas meddelandet "inaktiverat av systemet" i Azure Portal. Automatisk justering avser arbets belastningar med den högsta resurs prioriteten.

Automatiska justerings mekanismer är mogna och har blivit utmärkta för flera miljoner databaser som körs på Azure. Automatiska justerings åtgärder som tillämpas verifieras automatiskt för att se till att det finns en positiv förbättring av arbets belastnings prestandan. Prestanda rekommendationerna för försämrat identifieras dynamiskt och snabbt. Genom den registrerade justerings historiken finns det en tydlig spårning av de justerings förbättringar som gjorts för varje Azure SQL Database. 

![Hur fungerar automatisk justering?](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatisk justering delar sin kärn logik med SQL Server automatisk justerings motor. Ytterligare teknisk information om den inbyggda intelligens mekanismen finns [SQL Server automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Använd automatisk justering

Automatisk justering måste vara aktiverat för din prenumeration. Om du vill aktivera automatisk justering med hjälp av Azure Portal, se [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md).

Automatisk justering kan användas självständigt genom att automatiskt tillämpa justerings rekommendationer, inklusive automatiserad verifiering av prestanda vinster. 

Om du vill ha mer kontroll kan du stänga av automatisk användning av justerings rekommendationer och justera rekommendationer kan tillämpas manuellt via Azure Portal. Du kan också använda lösningen för att Visa automatiska justerings rekommendationer och manuellt tillämpa dem via skript och verktyg som du själv väljer. 

En översikt över hur automatisk justering fungerar och för vanliga användnings scenarier finns i den inbäddade videon:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Alternativ för automatisk justering

Alternativen för automatisk justering är tillgängliga i Azure SQL Database:

| Alternativ för automatisk justering | Stöd för enkel databas och poolad databas | Stöd för instans databas |
| :----------------------------- | ----- | ----- |
| **Skapa index** – identifierar index som kan förbättra prestandan för din arbets belastning, skapar index och automatiskt verifierar att prestandan för frågor har förbättrats. | Ja | Nej | 
| **Drop index** – identifierar redundanta och duplicerade index dagligen, förutom unika index och index som inte har använts under en längre tid (> 90 dagar). Observera att det här alternativet inte är kompatibelt med program som använder partition växlings-och index tips. | Ja | Nej |
| **FRAMTVINGA senaste effektiva plan** (automatisk plan korrigering) – identifierar SQL-frågor med hjälp av en körnings plan som är långsammare än den tidigare fungerande planen och frågor som använder det senaste fungerande schemat i stället för försämrat-planen. | Ja | Ja |

Automatisk justering identifierar **skapa index**, **Drop index**och **Framtvinga senaste lämpliga plan** rekommendationer som kan optimera databas prestanda och visa dem i [Azure Portal](sql-database-advisor-portal.md)och exponera dem via [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) och [ REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning). Om du vill veta mer om den senaste effektiva planen och konfigurera alternativ för automatisk justering via T-SQL, se [Automatisk justering inför automatisk plan korrigering](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).

Du kan antingen manuellt tillämpa justerings rekommendationer med hjälp av portalen, eller så kan du låta automatisk justering tillämpa justerings rekommendationer åt dig. Fördelarna med att låta systemet tillämpa justerings rekommendationer för dig är att det automatiskt validerar att det finns en positiv vinst för arbets belastnings prestandan, och om det inte finns någon betydande prestanda förbättring påträffad kommer den att Återställ automatiskt justerings rekommendationen. Observera att om frågor påverkas av justerings rekommendationer som inte körs ofta kan validerings fasen ta upp till 72 timmar efter design.

Om du använder justerings rekommendationer via T-SQL, är den automatiska prestanda valideringen och återförings metoderna inte tillgängliga. Rekommendationer som tillämpas på detta sätt förblir aktiva och visas i listan över justerings rekommendationer för 24-48 timmar. innan systemet återkallar dem automatiskt. Om du vill ta bort en rekommendation tidigare kan du ta bort den från Azure Portal.

Automatiska justerings alternativ kan aktive ras oberoende eller inaktive ras per databas, eller så kan de konfigureras på SQL Database servrar och tillämpas på alla databaser som ärver inställningar från servern. SQL Database servrar kan ärva Azure-standardvärden för automatiska justerings inställningar. Azure-standardvärden för tillfället är inställt på FORCE_LAST_GOOD_PLAN är aktiverat, CREATE_INDEX är aktiverat och DROP_INDEX är inaktive rad.

Att konfigurera alternativ för automatisk justering på en server och ärva inställningar för databaser som tillhör den överordnade servern är en rekommenderad metod för att konfigurera automatisk justering eftersom det fören klar hanteringen av automatiska justerings alternativ för ett stort antal databaser.

## <a name="next-steps"></a>Nästa steg

- Om du vill aktivera automatisk justering i Azure SQL Database för att hantera din arbets belastning, se [Aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Information om hur du manuellt granskar och använder automatiska justerings rekommendationer finns i [hitta och tillämpa prestanda rekommendationer](sql-database-advisor-portal.md).
- Information om hur du använder T-SQL för att tillämpa och Visa rekommendationer för automatisk justering finns i [hantera automatisk justering via T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Information om hur du skapar e-postaviseringar för automatiska justerings rekommendationer finns i [e-postaviseringar för automatisk justering](sql-database-automatic-tuning-email-notifications.md).
- Om du vill lära dig mer om inbyggd intelligens som används i automatisk justering, se [artificiell intelligens justerar Azure SQL-databaser](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Information om hur automatisk justering fungerar i Azure SQL Database och SQL Server 2017 finns i [SQL Server automatisk justering](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
