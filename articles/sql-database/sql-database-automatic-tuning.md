---
title: Azure SQL Database - automatisk justering | Microsoft Docs
description: "Azure SQL Database analyserar SQL-fråga och anpassas automatiskt till användare arbetsbelastning."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 11/08/2017
ms.author: jovanpop
ms.openlocfilehash: 34aa035368a0516f9fa1c71c1bda491daf5ab370
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Automatisk justering i Azure SQL Database

Azure SQL Database automatisk justering ger högsta prestanda och stabil arbetsbelastningar genom kontinuerlig prestandajustering använder styrs av datorn.

Automatisk justering är en helt hanterad tjänst som använder inbyggd intelligens att ständigt övervaka frågor som körs på en databas och deras prestanda förbättras automatiskt. Detta uppnås genom att dynamiskt anpassning av databasen för att ändra arbetsbelastningar och tillämpa prestandajustering rekommendationer. Automatisk justering vågrätt lär sig från alla databaser på Azure via styrs av datorn och den förbättrar dynamiskt dess prestandajustering åtgärder. Ju längre en Azure SQL Database körs med automatisk justering på, desto bättre utförs.

Azure SQL Database automatisk inställning kan vara en av de viktigaste funktionerna som du kan aktivera för att tillhandahålla stabilt och belastning som utför arbetsbelastningar.

## <a name="what-can-automatic-tuning-do-for-you"></a>Vad kan automatisk justering du göra?

- Automatisk prestandajustering för Azure SQL-databaser
- Automatisk kontroll av prestandavinster
- Automatisk återställning och egna korrigering
- Justera historikloggen
- Justera åtgärd T-SQL-skript för manuell distribution
- Proaktiv arbetsbelastning prestandaövervakning
- Skala ut kapaciteten på hundratals tusentals databaser
- Positivt DevOps-resurser och den totala ägandekostnaden

## <a name="safe-reliable-and-proven"></a>Säkert, tillförlitligt och beprövade

Prestandajustering åtgärder som tillämpas på Azure SQL-databaser är helt säker för prestanda för dina mest intensiva arbetsbelastningar. Systemet är utformad med försiktighet inte stör användaren arbetsbelastningar. Automatisk justering rekommendationer tillämpas endast vid tiden för en låg belastning. Systemet kan också tillfälligt inaktivera automatisk justering åtgärder för att skydda arbetsbelastningens prestanda. I så fall visas ”inaktiverat av systemet” meddelande i Azure-portalen. Automatisk justering gäller arbetsbelastningar med den högsta prioriteten för resursen.

Mekanismer för automatisk justering är mogen och har varit perfected på hundratals tusentals databaser som körs på Azure. Automatisk justering operations tillämpas verifieras automatiskt så det finns en positiv förbättring arbetsbelastning prestanda. Regressed rekommendationer dynamiskt identifieras och återställs så fort som möjligt. Det finns en tydlig spårning av justera förbättringar i varje Azure SQL Database via prestandajustering historikloggen. 

![Hur fungerar automatisk justering arbete](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database automatisk justering delar sin kärnlogik med SQL Server automatisk justering motorn. Ytterligare teknisk information om mekanismen inbyggd intelligens finns [automatisk justering av SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Använda automatisk inställning

Automatisk justering måste aktiveras manuellt på din prenumeration. Om du vill aktivera automatisk justering med hjälp av Azure portal finns [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).

Automatisk justering fungerar självständigt genom att automatiskt tillämpa prestandajustering rekommendationer, inklusive automatisk kontroll av prestandavinster. 

Automatisk tillämpning av justera rekommendationer kan stängas av för mer kontroll och justera rekommendationer kan tillämpas manuellt via Azure-portalen. Det är också möjligt att använda lösningen för att visa endast automatisk justering rekommendationer och tillämpa dem manuellt via skript och verktyg du väljer. 

En översikt över hur automatisk justering fungerar och vanliga Användningsscenarier finns inbäddad video:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Alternativ för automatisk justering

Automatisk justering alternativ i Azure SQL Database är:
 1. **CREATE INDEX** som identifierar de index som kan förbättra prestandan för din arbetsbelastning, skapar index och verifierar de förbättra prestanda för frågor.
 2. **DROP INDEX** som identifierar redundant och dubbla index och index som inte använts under en längre tidsperiod.
 3. **SENASTE bra PLAN för TVINGAD** som identifierar SQL-frågor som använder åtgärdsplan som är långsammare än tidigare bra plan och använder den senaste kända bra planen i stället för regressed planen.

Azure SQL-databas identifierar **CREATE INDEX**, **DROP INDEX**, och **kraft senaste bra planera** rekommendationer som kan optimera din databas och visar dem i Azure-portalen. Mer information om identifiering av index som ska ändras på [hitta index-rekommendationer i Azure-portalen](sql-database-advisor-portal.md). Du kan antingen tillämpa rekommendationer med hjälp av portalen manuellt eller du kan låta Azure SQL Database för att automatiskt tillämpa rekommendationer övervaka arbetsbelastning när ändringen och verifiera att rekommendationen bättre prestanda för din arbetsbelastning.

Automatisk justering alternativ kan vara oberoende aktiverat eller inaktiverat per databas eller de kan konfigureras på logisk server och tillämpas på alla databaser som ärver inställningarna från servern. Konfigurera automatisk justering alternativ på servern och arv inställningar på databaserna på servern är rekommenderad metod för att konfigurera automatisk justering eftersom det förenklar hanteringen av alternativ för automatisk justering på ett stort antal databaser.

## <a name="next-steps"></a>Nästa steg

- För att aktivera automatisk justering i Azure SQL Database för att hantera din arbetsbelastning, se [aktivera automatisk justering](sql-database-automatic-tuning-enable.md).
- Om du vill granska och Använd automatisk justering rekommendationer manuellt finns [söka efter och tillämpa rekommendationer](sql-database-advisor-portal.md).
- Läs mer om inbyggd intelligens som används i automatisk justering i [artificiell Intelligence justerar Azure SQL-databaser](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Mer information om hur automatisk justering fungerar i Azure SQL Database och SQL server 2017 finns [automatisk justering av SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/automatic-tuning/automatic-tuning).
