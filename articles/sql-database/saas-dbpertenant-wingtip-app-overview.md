---
title: Exempel på Azure SQL Database fleranvändarapp – Wingtip SaaS | Microsoft Docs
description: Lär dig med hjälp av ett exempelprogram för flera innehavare som använder Azure SQL Database, Wingtip SaaS-exempel
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: c26f042cb749f9ab023e7033ea015b74d87ca92a
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214120"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introduktion till en multitenant SaaS-app som använder mönstret databas-per-klient med SQL Database

Wingtip SaaS-program är en exempelapp för flera innehavare. Appen använder databas-per-klient SaaS-programmönster för att hantera flera klienter. Appen visar funktionerna i Azure SQL Database som möjliggör SaaS-scenarier med hjälp av flera SaaS-design och -hanteringsmönster. För att snabbt komma igång, distribuerar Wingtip SaaS-appen på mindre än fem minuter.

Programmet källa kod och hantering av skript är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Innan du börjar bör du se den [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg att ladda ned och avblockera management Wingtip biljettskripten.

## <a name="application-architecture"></a>Programarkitektur

Wingtip SaaS-appen använder modellen databas-per-klient. Elastiska SQL-pooler används för att maximera effektiviteten. För etablering och mappning av klienter till sina data används en katalogdatabas. Core Wingtip SaaS-program använder en pool med tre exempelklienter, plus katalogdatabasen. Katalog- och klient-servrar har etablerats med DNS-alias. Dessa alias används för att underhålla en referens till de aktiva resurser som används av Wingtip-programmet. Dessa alias har uppdaterats för att peka på recovery resurser i disaster recovery självstudier. Du har slutfört många av Wingtip SaaS-självstudier resulterar i tillägg till den första distributionen. Tillägg till exempel analytiska databaser och databasöverskridande schemahantering införs.


![Wingtip SaaS-arkitekturen](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


När du går igenom självstudierna och arbeta med appen behöver fokusera på SaaS-mönstren som är relaterade till datanivån. Med andra ord kan du fokusera på datanivån och inte overanalyze själva appen. Förstå implementeringen av dessa SaaS mönster är nyckeln till att implementera dem i dina program. Överväg också att alla ändringar som krävs för dina specifika affärsbehov.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS-självstudier

När du har distribuerat appen, utforska följande självstudier som bygger på den första distributionen. De här självstudierna utforska vanliga SaaS-mönster som utnyttjar de inbyggda funktionerna i SQL Database, Azure SQL Data Warehouse och andra Azure-tjänster. Självstudier innehåller PowerShell-skript med detaljerade förklaringar. Förklaringarna förenklar förståelsen och implementeringen av samma SaaS-hanteringsmönster i dina program.


| Självstudier | Beskrivning |
|:--|:--|
| [Vägledning och tips för SQL Database-principexempel för flera innehavare SaaS](saas-tenancy-wingtip-app-guidance-tips.md) | Hämta och kör PowerShell-skript för att förbereda delar av programmet. |
|[Distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)|  Distribuera och utforska Wingtip SaaS-program med din Azure-prenumeration. |
|[Etablera och katalogisera klienter](saas-dbpertenant-provision-and-catalog.md)| Lär dig hur programmet ansluter till klienter med hjälp av en katalogdatabas och hur klienter mappas till sina data i katalogen. |
|[Övervaka och hantera prestanda](saas-dbpertenant-performance-monitoring.md)| Lär dig hur du använder övervakningsfunktionerna i SQL-databas och ställa in aviseringar om prestanda tröskelvärden överskrids. |
|[Övervaka med Azure Monitor-loggar](saas-dbpertenant-log-analytics.md) | Lär dig hur du använder [Azure Monitor loggar](../log-analytics/log-analytics-overview.md) övervaka stora mängder resurser över flera pooler. |
|[Återställa en enskild klientorganisation](saas-dbpertenant-restore-single-tenant.md)| Lär dig mer om att återställa en klientdatabas till en tidigare tidpunkt. Lär dig också att återställa till en parallell databas, vilket lämnar befintliga klientdatabasen online. |
|[Hantera klientorganisationsschema för databasen](saas-tenancy-schema-management.md)| Lär dig hur du uppdaterar schemat och uppdaterar referensdata för alla klientdatabaser. |
|[Köra distribuerade frågor för flera klienter](saas-tenancy-cross-tenant-reporting.md) | Skapa en ad hoc-analysdatabas och kör i realtid distribuerade frågor över alla klienter.  |
|[Köra analyser på extraherade klientdata](saas-tenancy-tenant-analytics.md) | Extrahera klientdata till en analytics-databasen eller datalagret för offline analysfrågor. |


## <a name="next-steps"></a>Nästa steg

- [Allmänna riktlinjer och tips när du distribuerar och använder exemplet för Wingtip biljetter SaaS-app](saas-tenancy-wingtip-app-guidance-tips.md)
- [Distribuera Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)
