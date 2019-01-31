---
title: Azure SQL-databas – automatisk justering | Microsoft Docs
description: Azure SQL Database analyserar SQL-fråga och anpassas efter automatiskt arbetsbelastning per användare.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b13becf8530f478a5e58b46a1b422593051c95cf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478176"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatisk justering i Azure SQL Database

Azure SQL Database automatisk justering ger högsta prestanda och stabil arbetsbelastningar genom kontinuerlig prestandajustering baserat på AI och machine learning.

Automatisk justering är en fullständigt hanterad intelligent prestanda-tjänst som använder inbyggd intelligens för att kontinuerligt övervaka frågor som körs på en databas och förbättrar automatiskt deras prestanda. Detta uppnås med dynamiskt anpassning av databasen för att ändra arbetsbelastningar och tillämpa justeringsrekommendationer. Automatisk justering vågrätt lär sig från alla databaser på Azure via AI och det förbättrar dynamiskt prestandajusteringsåtgärderna. Ju längre en Azure SQL Database körs med automatisk justering på, desto bättre utförs.

Azure SQL Database automatisk justering kan vara en av de viktigaste funktionerna som du kan aktivera för att tillhandahålla stabila och prestandaoptimerade arbetsbelastningar.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vad kan automatisk justering göra för dig?

- Automatisk prestandajustering för Azure SQL-databaser
- Automatisk kontroll av prestandavinster
- Automatisk återställning och självhantering korrigering
- Justeringshistorik
- Justera åtgärden T-SQL-skript för manuell distribution
- Proaktiv arbetsbelastning prestandaövervakning
- Skala ut-funktionen på hundratusentals databaser
- Positiv påverkan på DevOps-resurser och den totala ägandekostnaden

## <a name="safe-reliable-and-proven"></a>Säker, pålitlig och beprövad

Justeringsåtgärder som tillämpas på Azure SQL är-databaser helt säkra för prestanda för dina mest intensiva arbetsbelastningar. Systemet har utformats med försiktighet inte stör användaren arbetsbelastningar. Automatiska justeringsrekommendationer tillämpas endast vid tidpunkter med låg belastning. Systemet kan också tillfälligt inaktivera automatisk justering åtgärder för att skydda arbetsbelastningsprestandan. I detta fall, kommer ”inaktiverad av systemet” meddelandet att visas i Azure-portalen. Automatisk justering betraktar arbetsbelastningar med högst prioritet för resursen.

Mekanismer för automatisk justering är mogen och har varit högerklicksmeny på flera miljoner databaser som körs på Azure. Automatisk justering åtgärder som tillämpas verifieras automatiskt att se till att det finns en positiv förbättring för arbetsbelastningsprestandan. Försämrad prestandarekommendationer dynamiskt upptäcks och utan dröjsmål har återställts. Det finns en tydlig spårning av justering av förbättringarna för varje Azure SQL-databas via justering historiken registreras. 

![Hur fungerar automatisk justering](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatisk justering delar logiken core med SQL Server-automatisk justering motorn. Ytterligare teknisk information om mekanismen för inbyggd intelligens, finns i [automatisk justering i SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Använda automatisk justering

Automatisk justering måste aktiveras manuellt på din prenumeration. För att aktivera automatisk justering med hjälp av Azure portal, se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).

Automatisk justering kan arbeta självständigt genom att automatiskt tillämpa justeringsrekommendationer, inklusive automatisk kontroll av prestandavinster. 

Automatisk tillämpning av justeringsrekommendationer kan stängas av för mer kontroll och justeringsrekommendationer kan tillämpas manuellt via Azure-portalen. Du kan också använda lösningen för att visa automatiska justeringsrekommendationer endast och tillämpa dem manuellt via skript och verktyg du väljer. 

Finns det inbäddade videoklippet för en översikt över hur automatisk justering fungerar och för vanliga Användningsscenarier:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Alternativen för automatisk justering

Alternativen för automatisk justering i Azure SQL Database är:

 1. **CREATE INDEX** -identifierar index som kan förbättra prestandan för din arbetsbelastning, skapar index och automatiskt verifierar att prestanda för frågor har förbättrats.
 2. **DROP INDEX** -identifierar redundant och duplicerade index dagligen, förutom unika index och index som inte använts under en längre tid (> 90 dagar). Observera att just nu alternativet inte är kompatibel med program som använder partition växla och index-tips.
 3. **Tvinga fram den senaste bra PLANERINGEN** -identifierar SQL-frågor med Körningsplan som är lägre än den tidigare bra planering och frågor med senaste kända planen i stället för försämrad planen.

Automatisk justering identifierar **CREATE INDEX**, **DROP INDEX**, och **kraft senaste bra planera** rekommendationer som kan optimera din databasprestanda och visar dem i [Azure-portalen](sql-database-advisor-portal.md), och visar dem via [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) och [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Du kan antingen manuellt tillämpa justeringsrekommendationer med hjälp av portalen eller du kan låta automatisk justering självständigt gäller justeringsrekommendationer för dig. Fördelarna med att låta systemet självständigt gäller justeringsrekommendationer för dig är att den verifierar automatiskt det finns en positiv vinst arbetsbelastningsprestandan och om det finns inga betydande prestandaförbättringar har identifierats, kommer det att automatiskt återgå justering rekommendationen. Observera att valideringsfasen vid frågor som påverkas av justeringsrekommendationer som inte utförs ofta, kan ta upp till 72 timmar avsiktligt. I de fall du tillämpar manuellt justering finns rekommendationer, automatisk prestanda verifiering och återföring mekanismer inte.

Alternativen för automatisk justering kan aktiveras eller inaktiveras per databas oberoende av varandra, eller de kan konfigureras på SQL Database-servrar och tillämpas på alla databaser som ärver inställningar från servern. SQL Database-servrar kan ärva Azure-standardinställningar för inställningar för automatisk justering. Azure-standardinställningar just nu är inställda på att FORCE_LAST_GOOD_PLAN är aktiverad, CREATE_INDEX är aktiverad och DROP_INDEX är inaktiverad.

Konfigurera automatisk justering alternativ på en server och ärver inställningar för databaser som tillhör den överordnade servern är en rekommenderad metod för att konfigurera automatisk justering som förenklar hanteringen av automatiska justeringsalternativ för ett stort antal databaser.

## <a name="next-steps"></a>Nästa steg

- För att aktivera automatisk justering i Azure SQL Database för att hantera din arbetsbelastning, se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill granska och tillämpa automatiska justeringsrekommendationer manuellt finns i [hitta och tillämpa prestandarekommendationer](sql-database-advisor-portal.md).
- Läs hur du använder T-SQL för att tillämpa och visa automatiska justeringsrekommendationer i [hantera automatisk justering via T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/).
- Läs om hur du skapar e-postmeddelanden för automatiska justeringsrekommendationer i [e-postmeddelanden för automatisk justering](sql-database-automatic-tuning-email-notifications.md).
- Läs om inbyggd intelligens som används i automatisk justering i [artificiell intelligens justerar Azure SQL-databaser](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Läs om hur automatisk justering fungerar i Azure SQL Database och SQL server 2017 i [automatisk justering i SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
