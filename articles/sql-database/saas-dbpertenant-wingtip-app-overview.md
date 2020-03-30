---
title: Multitenant app exempel - Wingtip SaaS
description: Lär dig genom att använda ett exempel på program med flera innehavare som använder Azure SQL Database, Wingtip SaaS-exemplet
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 46cdcd5f768278dbc729f48e450c68a63be604be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256502"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Introduktion till en SaaS-app med flera innehavare som använder mönstret databas per klient med SQL Database

Wingtip SaaS-programmet är en app för multitenantprov. Appen använder saas-programmönstret för databas per klient för att betjäna flera klienter. Appen visar upp funktioner i Azure SQL Database som aktiverar SaaS-scenarier med hjälp av flera SaaS-design- och hanteringsmönster. För att snabbt komma igång distribueras Wingtip SaaS-appen på mindre än fem minuter.

Program källkod och skript hantering finns i [WingtipTicketsSSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Innan du börjar kan du se den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att hämta och häva blockeringen av wingtip-biljetternas hanteringsskript.

## <a name="application-architecture"></a>Programarkitektur

Wingtip SaaS-appen använder modellen databas per klient. Den använder SQL elastiska pooler för att maximera effektiviteten. För etablering och mappning av klienter till sina data används en katalogdatabas. Core Wingtip SaaS-programmet använder en pool med tre exempelklienter, plus katalogdatabasen. Katalogen och klientservrarna har etablerats med DNS-alias. Dessa alias används för att behålla en referens till de aktiva resurser som används av Wingtip-programmet. Dessa alias uppdateras för att peka på återställningsresurser i självstudierna för haveriberedskap. Slutföra många av Wingtip SaaS tutorials resulterar i tillägg till den första distributionen. Tillägg som analytiska databaser och schemahantering över flera databaser introduceras.


![Wingtip SaaS arkitektur](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


När du går igenom självstudierna och arbetar med appen fokuserar du på SaaS-mönstren när de relaterar till datanivån. Med andra ord, fokusera på datanivån och överanalysera inte själva appen. Att förstå implementeringen av dessa SaaS-mönster är nyckeln till att implementera dessa mönster i dina program. Tänk också på nödvändiga ändringar för dina specifika affärskrav.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS-självstudier

När du har distribuerat appen kan du utforska följande självstudier som bygger på den första distributionen. Dessa självstudier utforskar vanliga SaaS-mönster som utnyttjar inbyggda funktioner i SQL Database, Azure SQL Data Warehouse och andra Azure-tjänster. Självstudier inkluderar PowerShell-skript med detaljerade förklaringar. Förklaringarna förenklar förståelsen och implementeringen av samma SaaS-hanteringsmönster i dina program.


| Självstudier | Beskrivning |
|:--|:--|
| [Vägledning och tips för exemplet med SQL Database-multitenant SaaS-appen](saas-tenancy-wingtip-app-guidance-tips.md) | Hämta och kör PowerShell-skript för att förbereda delar av programmet. |
|[Distribuera och utforska Wingtip SaaS-programmet](saas-dbpertenant-get-started-deploy.md)|  Distribuera och utforska Wingtip SaaS-programmet med din Azure-prenumeration. |
|[Etablera och kataloghyresgäster](saas-dbpertenant-provision-and-catalog.md)| Lär dig hur programmet ansluter till klienter med hjälp av en katalogdatabas och hur katalogen mappar klienter till sina data. |
|[Övervaka och hantera prestanda](saas-dbpertenant-performance-monitoring.md)| Lär dig hur du använder övervakningsfunktioner i SQL Database och anger aviseringar när prestandatrösklar överskrids. |
|[Övervaka med Azure Monitor-loggar](saas-dbpertenant-log-analytics.md) | Lär dig hur du använder [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md) för att övervaka stora mängder resurser över flera pooler. |
|[Återställa en enskild klientorganisation](saas-dbpertenant-restore-single-tenant.md)| Lär dig hur du återställer en klientdatabas till en tidigare tidpunkt. Lär dig också hur du återställer till en parallell databas, som lämnar den befintliga klientdatabasen online. |
|[Hantera klientdatabasschema](saas-tenancy-schema-management.md)| Lär dig hur du uppdaterar schema- och uppdateringsreferensdata i alla klientdatabaser. |
|[Köra distribuerade frågor mellan klienter](saas-tenancy-cross-tenant-reporting.md) | Skapa en ad hoc-analysdatabas och kör distribuerade frågor i realtid för alla klienter.  |
|[Kör analyser på extraherade klientdata](saas-tenancy-tenant-analytics.md) | Extrahera klientdata till en analysdatabas eller ett informationslager för offlineanalysfrågor. |


## <a name="next-steps"></a>Nästa steg

- [Allmän vägledning och tips när du distribuerar och använder exemplet med Wingtip Tickets SaaS-appen](saas-tenancy-wingtip-app-guidance-tips.md)
- [Distribuera Wingtip SaaS-programmet](saas-dbpertenant-get-started-deploy.md)
