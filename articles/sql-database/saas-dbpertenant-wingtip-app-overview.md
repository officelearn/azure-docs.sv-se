---
title: "Azure SQL-databas med flera innehavare app exempel – Wingtip SaaS | Microsoft Docs"
description: "Lär dig mer med hjälp av ett exempelprogram för flera innehavare som använder Azure SQL Database, Wingtip SaaS-exempel"
keywords: sql database tutorial
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 451e1fc87fc5f626e78760d8cd5c4115ea02bb0d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introduktion till en flera innehavare SaaS-appar som använder databasen per klient mönster med SQL-databas

Den *Wingtip SaaS* programmet är en exempelapp för flera innehavare. Appen använder databasen per klient, mönster för SaaS-program för att betjäna flera innehavare. Appen demonstreras funktioner i Azure SQL Database som möjliggör SaaS scenarier med flera SaaS design- och mönster. För att snabbt komma igång, distribuerar Wingtip SaaS-appen på mindre än fem minuter!

Programmet källa koden och hantering av skript är tillgängliga i den [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-lagringsplatsen. Innan du börjar ta en titt på [allmänna riktlinjer](saas-tenancy-wingtip-app-guidance-tips.md) steg för att ladda ned och avblockera hanteringsskript Wingtip biljetter.

## <a name="application-architecture"></a>Programarkitektur

Wingtip SaaS-appen använder databasen per klient modellen och använder SQL elastiska pooler för att maximera effektiviteten. En katalog databas används för etablering och mappningen klienter till deras data. Core Wingtip SaaS-program använder en pool med tre exempel innehavare plus katalogdatabasen. Slutför många av de Wingtip SaaS självstudier resultera i tillägg till den första distributionen genom att introducera analytiska databaser flera databaser schemahantering, osv.


![Wingtip SaaS-arkitekturen](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


När du går igenom självstudierna och arbetar med appen är det viktigt att fokusera på SaaS-mönster i samband med datanivå. Med andra ord: fokusera på datanivån och överanalysera inte själva appen. Implementeringen av dessa SaaS mönster är att förstå för att implementera dessa mönster i dina program när du funderar på att alla nödvändiga ändringar för dina specifika affärsbehov.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL-databas Wingtip SaaS-självstudier

När appen har distribuerats, utforska följande kurser som bygger på den första distributionen. Dessa självstudiekurser utforska vanliga SaaS-mönster som utnyttjar inbyggda funktioner för SQL Database, SQL Data Warehouse och andra Azure-tjänster. Självstudier innehåller PowerShell-skript med förklaringar som förenklar förstå och implementera samma SaaS management mönster i dina program.


| Självstudier | Beskrivning |
|:--|:--|
| [Vägledning och tips för Azure SQL Database med flera innehavare SaaS app exempel](saas-tenancy-wingtip-app-guidance-tips.md) | **BÖRJA HÄR!** Hämta och köra PowerShell-skript för att förbereda delar av programmet. |
|[Distribuera och utforska Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)|  Distribuera och utforska Wingtip SaaS-program på Azure-prenumerationen. |
|[Etablera och katalogen klienter](saas-dbpertenant-provision-and-catalog.md)| Lär dig hur programmet ansluter till klienter som använder en katalog-databas och hur klienter mappas till deras data i katalogen. |
|[Övervaka och hantera prestanda](saas-dbpertenant-performance-monitoring.md)| Lär dig hur du använder övervakningsfunktionerna i SQL-databas och hur du ställer in meddelanden när tröskelvärden för prestanda har överskridits. |
|[Övervakare med logganalys (OMS)](saas-dbpertenant-log-analytics.md) | Lär dig mer om hur du använder [logganalys](../log-analytics/log-analytics-overview.md) att övervaka stora mängder resurser över flera pooler. |
|[Återställa en enskild klient](saas-dbpertenant-restore-single-tenant.md)| Lär dig mer om att återställa en databas för klient till en tidigare tidpunkt. Steg för att återställa till en parallell databas, lämnar den befintliga databasen klient online, ingår också. |
|[Hantera databasschemat för klient](saas-tenancy-schema-management.md)| Lär dig mer om att uppdatera schema och uppdatera referensdata, över alla klient-databaser. |
|[Kör mellan klient distribuerade frågor](saas-tenancy-cross-tenant-reporting.md) | Skapa en ad hoc-analytics databas och kör realtid distribuerade frågor över alla klienter.  |
|[Göra analyser på extraherade klientdata](saas-tenancy-tenant-analytics.md) | Extrahera klientdata till en analytics databasen eller data warehouse för offline analytics-frågor. |


## <a name="next-steps"></a>Nästa steg

- [Allmänna råd och tips när du distribuerar och använder app-exempel Wingtip biljetter SaaS](saas-tenancy-wingtip-app-guidance-tips.md)

- [Distribuera Wingtip SaaS-program](saas-dbpertenant-get-started-deploy.md)
