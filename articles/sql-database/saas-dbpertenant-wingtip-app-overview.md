---
title: Azure SQL Database multitenant app exempel – Wingtip SaaS | Microsoft Docs
description: Lär dig mer med hjälp av en multitenant exempelprogram som använder Azure SQL Database, Wingtip SaaS-exempel
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: cf54c789d766c4bd3d353028e75e34c961470070
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introduktion till en multitenant SaaS-appar som använder databasen per klient-mönstret med SQL-databas

Wingtip SaaS-program är en exempelapp för multitenant. Appen använder databasen per klient SaaS-programmönster för att betjäna flera innehavare. Appen demonstreras funktioner i Azure SQL Database som aktiverar SaaS scenarier med flera SaaS design- och mönster. För att snabbt komma igång, distribuerar Wingtip SaaS-appen på mindre än fem minuter.

Programmet källa koden och hantering av skript är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Innan du börjar finns det [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera hanteringsskript Wingtip biljetter.

## <a name="application-architecture"></a>Programarkitektur

Wingtip SaaS-appen använder databasen per klient-modellen. SQL elastiska pooler används för att maximera effektiviteten. En katalog databas används för etablering och mappningen klienter till deras data. Core Wingtip SaaS-program använder en pool med tre exempel innehavare plus katalogdatabasen. Katalog och klient-servrar har etablerats med DNS-alias. Dessa alias används för att underhålla en referens till de aktiva resurser som används av programmet Wingtip. Dessa alias uppdateras för att peka till recovery resurser i disaster recovery självstudiekurser. Slutför många Wingtip SaaS självstudier resultaten i tillägg till den första distributionen. Tillägg som analytiska databaser och schemahantering av flera databaser införs.


![Wingtip SaaS-arkitekturen](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


När du går igenom självstudierna och arbeta med appen fokusera på SaaS-mönster i samband med datanivå. Med andra ord kan du fokusera på datanivå och inte overanalyze själva appen. Implementeringen av dessa SaaS mönster är att förstå för att implementera dessa mönster i dina program. Överväg också att alla nödvändiga ändringar för dina specifika affärsbehov.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL-databas Wingtip SaaS-självstudier

När du har distribuerat appen utforska följande kurser som bygger på den första distributionen. Dessa självstudiekurser utforska vanliga SaaS-mönster som utnyttjar inbyggda funktioner i SQL-databas, Azure SQL Data Warehouse och andra Azure-tjänster. Självstudier innehåller PowerShell-skript med förklaringar. Förklaringarna förenkla förståelse och implementering av samma SaaS management mönster i dina program.


| Självstudier | Beskrivning |
|:--|:--|
| [Vägledning och tips för SQL-databas multitenant SaaS app-exempel](saas-tenancy-wingtip-app-guidance-tips.md) | Hämta och köra PowerShell-skript för att förbereda delar av programmet. |
|[Distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)|  Distribuera och utforska Wingtip SaaS-program med Azure-prenumeration. |
|[Etablera och katalogen klienter](saas-dbpertenant-provision-and-catalog.md)| Lär dig hur programmet ansluter till klienter med hjälp av en katalog-databas och hur klienter mappas till deras data i katalogen. |
|[Övervaka och hantera prestanda](saas-dbpertenant-performance-monitoring.md)| Lär dig hur du använder övervakningsfunktionerna i SQL-databasen och Ställ in varningar när tröskelvärden för prestanda har överskridits. |
|[Övervakare med Azure logganalys](saas-dbpertenant-log-analytics.md) | Lär dig hur du använder [logganalys](../log-analytics/log-analytics-overview.md) att övervaka stora mängder resurser över flera pooler. |
|[Återställa en enskild klient](saas-dbpertenant-restore-single-tenant.md)| Lär dig mer om att återställa en databas för klient till en tidigare tidpunkt. Också lära dig hur du återställer till en parallell databas, vilket lämnar den befintliga databasen klient online. |
|[Hantera databasschemat för klient](saas-tenancy-schema-management.md)| Lär dig mer om att uppdatera schema och uppdatera referensdata över alla klient-databaser. |
|[Kör mellan klient distribuerade frågor](saas-tenancy-cross-tenant-reporting.md) | Skapa en ad hoc-analytics databas och kör realtid distribuerade frågor över alla klienter.  |
|[Göra analyser på extraherade klientdata](saas-tenancy-tenant-analytics.md) | Extrahera klientdata till en analytics databasen eller data warehouse för offline analytics-frågor. |


## <a name="next-steps"></a>Nästa steg

- [Allmänna råd och tips när du distribuerar och använder app-exempel Wingtip biljetter SaaS](saas-tenancy-wingtip-app-guidance-tips.md)
- [Distribuera Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)
