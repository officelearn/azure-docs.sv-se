---
title: Översikt över automatisk justering
description: Azure SQL Database analyserar SQL-frågan och anpassar sig automatiskt till användararbetsbelastningen.
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
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80518227"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatisk justering i Azure SQL Database

Automatisk justering av Azure SQL Database ger hög prestanda och stabila arbetsbelastningar genom kontinuerlig prestandajustering baserat på AI och maskininlärning.

Automatisk justering är en fullständigt hanterad intelligent prestandatjänst som använder inbyggd intelligens för att kontinuerligt övervaka frågor som körs i en databas, och den förbättrar automatiskt deras prestanda. Detta uppnås genom att dynamiskt anpassa databasen till de förändrade arbetsbelastningarna och tillämpa justeringsrekommendationer. Automatisk justering lär sig horisontellt från alla databaser på Azure via AI och det förbättrar dynamiskt dess justeringsåtgärder. Ju längre en databas körs med automatisk justering på, desto bättre presterar den.

Automatisk justering av Azure SQL Database kan vara en av de viktigaste funktionerna som du kan aktivera för att tillhandahålla stabila och högpresterande databasarbetsbelastningar.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vad kan automatisk justering göra för dig

- Automatisk prestandajustering av Azure SQL-databaser
- Automatisk verifiering av prestandavinster
- Automatisk återställning och självkorrigering
- Justeringshistorik
- Justera åtgärd T-SQL-skript för manuella distributioner
- Proaktiv övervakning av arbetsbelastningsprestanda
- Skala ut kapacitet på hundratusentals databaser
- Positiv inverkan på DevOps-resurser och den totala ägandekostnaden

## <a name="safe-reliable-and-proven"></a>Säker, tillförlitlig och beprövad

Justeringsåtgärder som tillämpas på databaser i Azure SQL Database är helt säkra för prestanda för dina mest intensiva arbetsbelastningar. Systemet har utformats varsamt för att inte störa användarens arbetsbelastningar. Automatiserade justeringsrekommendationer tillämpas endast vid tidpunkter för ett lågt utnyttjande. Systemet kan också tillfälligt inaktivera automatiska justeringsåtgärder för att skydda arbetsbelastningens prestanda. I sådana fall visas meddelandet "Inaktiverad av systemet" i Azure-portalen. Automatisk justering gäller arbetsbelastningar med högst resursprioritet.

Automatiska justeringsmekanismer är mogna och har fulländats på flera miljoner databaser som körs på Azure. Automatiserade justeringsåtgärder verifieras automatiskt för att säkerställa att arbetsbelastningens prestanda förbättras positivt. Regresserade prestandarekommendationer identifieras dynamiskt och återställs snabbt. Genom den inspelade justeringshistoriken finns det en tydlig spårning av justeringsförbättringar som gjorts i varje Azure SQL-databas.

![Hur fungerar automatisk justering](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Automatisk justering av Azure SQL Database delar sin kärnlogik med den automatiska inställningsmotorn för SQL Server. Mer teknisk information om den inbyggda intelligensmekanismen finns i [automatisk inställning för SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

En översikt över hur automatisk justering fungerar och för typiska användningsscenarier finns i den inbäddade videon:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>Aktivera automatisk inställning

Du kan [aktivera automatisk justering för enskilda och poolade databaser i Azure-portalen](sql-database-automatic-tuning-enable.md) eller använda ALTER [DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) T-SQL-uttrycket. Du aktiverar automatisk justering för instansdatabaser i en hanterad instansdistribution med hjälp av [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL-uttrycket.

## <a name="automatic-tuning-options"></a>Alternativ för automatisk justering

Automatiska justeringsalternativ som är tillgängliga i Azure SQL Database är:

| Alternativ för automatisk justering | Stöd för en enda databas och poolad databas | Stöd för instansdatabas |
| :----------------------------- | ----- | ----- |
| **SKAPA INDEX** - Identifierar index som kan förbättra prestanda för din arbetsbelastning, skapar index och verifierar automatiskt att prestanda för frågor har förbättrats. | Ja | Inga |
| **DROP INDEX** - Identifierar redundanta och duplicerade index dagligen, med undantag för unika index och index som inte användes på länge (>90 dagar). Observera att det här alternativet inte är kompatibelt med program som använder partitionsväxling och indextips. Det går inte att släppa oanvända index för premium- och affärskritiska tjänstnivåer. | Ja | Inga |
| **TVINGA SISTA BRA PLAN** (automatisk plankorrigering) - Identifierar SQL-frågor med körningsplan som är långsammare än den tidigare bra planen och frågor som använder den senast fungerande planen i stället för den regresserade planen. | Ja | Ja |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>Automatisk justering för enstaka och poolade databaser

Automatisk justering för enskilda och poolade databaser använder rekommendationerna **SKAPA INDEX,** **DROP INDEX**och FORCE LAST **GOOD PLAN** för att optimera databasens prestanda. Mer information finns [i rekommendationer för databasrådgivare i Azure-portalen,](sql-database-advisor-portal.md)i [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)och i [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

Du kan antingen manuellt tillämpa justeringsrekommendationer med hjälp av Azure-portalen eller så kan du låta automatisk justering autonomt tillämpa justeringsrekommendationer för dig. Fördelarna med att låta systemet självständigt tillämpa justeringsrekommendationer för dig är att det automatiskt validerar det finns en positiv vinst för arbetsbelastningens prestanda, och om det inte finns någon betydande prestandaförbättring upptäckt, kommer det automatiskt att återställa justeringsrekommendationen. Observera att om frågor som påverkas av justeringsrekommendationer som inte körs ofta, kan valideringsfasen ta upp till 72 timmar avsiktligt.

Om du tillämpar justeringsrekommendationer via T-SQL är automatisk prestandavalidering och återföringsmekanismer inte tillgängliga. Rekommendationer som tillämpas på ett sådant sätt kommer att förbli aktiva och visas i listan över tuning rekommendationer för 24-48 timmar. innan systemet automatiskt drar tillbaka dem. Om du vill ta bort en rekommendation tidigare kan du ignorera den från Azure-portalen.

Automatiska justeringsalternativ kan aktiveras eller inaktiveras oberoende av dem per databas, eller så kan de konfigureras på SQL Database-servrar och tillämpas på alla databaser som ärver inställningar från servern. SQL Database-servrar kan ärva Azure-standardinställningar för automatiska justeringsinställningar. Azure-standardvärden för närvarande är inställda på FORCE_LAST_GOOD_PLAN är aktiverat, CREATE_INDEX är aktiverat och DROP_INDEX är inaktiverat.

> [!IMPORTANT]
> Från och med mars börjar 2020 ändringar av Azure-standardvärden för automatisk justering gälla på följande sätt:
>
> - Nya Azure-standardvärden kommer att vara FORCE_LAST_GOOD_PLAN = aktiverade, CREATE_INDEX = inaktiverade och DROP_INDEX = inaktiverade.
> - Befintliga servrar utan automatiska justeringsinställningar konfigureras automatiskt för att ÄRVA de nya Azure-standardinställningarna. Detta gäller alla kunder som för närvarande har serverinställningar för automatisk justering i ett odefinierat tillstånd.
> - Nya servrar som skapas konfigureras automatiskt för att ÄRVA de nya Azure-standardvärdena (till skillnad från tidigare när automatisk justeringskonfiguration var i ett odefinierat tillstånd när den nya servern skapades).

Att konfigurera automatiska justeringsalternativ på en server och ärva inställningar för databaser som tillhör den överordnade servern är en rekommenderad metod för att konfigurera automatisk justering eftersom det förenklar hanteringen av automatiska justeringsalternativ för ett stort antal databaser.

Mer information om hur du skapar e-postmeddelanden för automatisk justeringsrekommendationer finns i [E-postmeddelanden för automatisk justering](sql-database-automatic-tuning-email-notifications.md).

### <a name="automatic-tuning-for-instance-databases"></a>Automatisk justering för databaser till exempel

Automatisk justering för instansdatabaser i en hanterad instansdistribution stöder endast **FORCE LAST GOOD PLAN**. Mer information om hur du konfigurerar automatiska justeringsalternativ via T-SQL finns i [Automatisk justering inför automatisk plankorrigering](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) och Automatisk [korrigering av plan.](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)

## <a name="next-steps"></a>Nästa steg

- Mer information om inbyggd intelligens som används i automatisk justering finns i [Artificiell intelligens låtar Azure SQL-databaser](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Mer information om hur automatisk justering fungerar under huven finns i [Indexera flera databaser automatiskt i Microsoft Azure SQL Database](https://www.microsoft.com/en-us/research/uploads/prod/2019/02/autoindexing_azuredb.pdf).
