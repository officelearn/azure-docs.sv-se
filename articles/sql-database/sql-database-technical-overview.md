---
title: "Vad är tjänsten Azure SQL Database? | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "introduktion till sql, sql-introduktion, vad är sql database"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: c505844cc2b7c745a1106b3c446833fb206ca98a
ms.lasthandoff: 03/17/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Vad är SQL Database? Introduktion till SQL Database
SQL Database är en relationsdatabastjänst i Microsoft Cloud som är baserad på den marknadsledande Microsoft SQL Server-motorn och som kan hantera verksamhetskritiska arbetsbelastningar. SQL Database levererar förutsägbar prestanda på flera tjänstenivåer, dynamisk skalbarhet utan avbrott, verksamhetskontinuitet och dataskydd – allt med nästan obefintlig administration. Dessa funktioner gör att du kan fokusera på snabb apputveckling och att accelerera din tid till marknaden, istället för att ägna värdefull tid och resurser åt att hantera virtuella datorer och infrastruktur. Eftersom SQL Database är baserad på [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx)-motorn stöder SQL Database befintliga SQL Server-verktyg, bibliotek och API:er. Därför är det enkelt att utveckla nya lösningar, att flytta dina befintliga SQL Server-lösningar och att utöka dina befintliga SQL Server-lösningar till Microsoft Cloud utan att lära dig nya färdigheter.

Den här artikeln ger en introduktion till nyckelkoncept och funktioner för SQL Database som relaterar till prestanda, skalbarhet och hanterbarhet, med länkar till mer detaljerad information. Läs följande snabbstarter innan du börjar:
 - [Skapa en SQL-databas i Azure Portal](sql-database-get-started-portal.md)  
 - [Skapa en SQL-databas med Azure CLI](sql-database-get-started-cli.md)
 - [Skapa en SQL Database med PowerShell](sql-database-get-started-powershell.md)

En uppsättning Azure CLI- och PowerShell-exempel finns här:
 - [Azure CLI-exempel för Azure SQL Database](sql-database-cli-samples.md)
 - [Azure PowerShell-exempel för Azure SQL Database](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>Justera prestanda och skalning utan avbrott
SQL Database-tjänsten erbjuder tre tjänstnivåer: Basic, Standard och Premium. Varje tjänstnivå erbjuder [olika prestandanivåer och funktioner](sql-database-service-tiers.md) för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app på en liten databas för några kronor i månaden och sedan [ändra dess tjänstnivå](sql-database-service-tiers.md) manuellt eller programmässigt när som helst för att uppfylla behoven i din lösning. Du kan göra detta utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastiska pooler som maximerar resursutnyttjandet
För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. [Elastiska pooler](sql-database-elastic-pool.md) är utformade för att lösa detta problem. Konceptet är enkelt. Du allokerar prestandaresurser till en pool snarare än till en individuell databas, och betalar för de samlade prestandaresurserna för poolen istället för en enskild databas prestanda. Med elastiska pooler behöver du inte fokusera på att reglera databasprestanda upp och ner när behovet av resurser varierar. Databaserna i poolen förbrukar den elastiska poolens prestandaresurser efter behov. Databaser i poolen förbrukar, men överskrider inte begränsningarna i poolen. Dina kostnader förblir förutsägbara även om den individuella databasanvändningen inte är det. Dessutom kan du [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md) och därmed skala din app från några få databaser till tusentals, inom en budget som du själv styr över. Slutligen kan du också styra de minimalt och maximalt tillgängliga resurser som är tillgängliga för databaser i poolen, för att säkerställa att ingen databas i poolen använder alla poolresurser och att varje databas i poolen har garanterade resurser. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="blend-single-databases-with-pooled-databases"></a>Blanda enskilda databaser med databaser i en pool
Vilken väg du än väljer – enskilda databaser eller elastiska pooler – är du inte låst. Du kan blanda enskilda databaser med elastiska pooler och snabbt och enkelt ändra tjänstnivåerna för enskilda databaser och elastiska pooler efter behov. Med den kraft och räckvidd som Azure har, kan du dessutom blanda andra Azure-tjänster med SQL Database, allt för att möta designbehoven för just din unika app, samt styra kostnader och upptäcka nya affärsmöjligheter.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering
Men hur kan man jämföra den relativa prestandan för enskilda databaser och elastiska pooler? Hur vet man rätt värden när man reglerar upp eller ner? Du använder den [inbyggda prestandaövervakningen](sql-database-performance.md) och [aviserings](sql-database-insights-alerts-portal.md)verktyg, i kombination med prestandaklassificeringarna baserade på [databastransaktionsenheter (DTU:er) för enskilda databaser och elastiska DTU:er (eDTU:er) för elastiska pooler](sql-database-what-is-a-dtu.md). Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner baserat på dina aktuella eller projekterade prestandakrav. Se [SQL Database, alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder](sql-database-service-tiers.md) för mer information.

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal [(SLA)](http://azure.microsoft.com/support/legal/sla/) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. Med varje SQL-databas drar du nytta av inbyggd säkerhet, feltolerans och [dataskydd](sql-database-automated-backups.md) som du annars skulle behöva köpa, utforma, utveckla och hantera. Med SQL Database erbjuder varje tjänstnivå en omfattande uppsättning funktioner för affärskontinuitet och alternativ som du kan använda för att komma igång. Du kan använda [point-in-time-återställning](sql-database-recovery-using-backups.md) för att återställa en databas till ett tidigare skede, så långt tillbaka som 35 dagar. Du kan konfigurera [långsiktig kvarhållning av säkerhetskopior](sql-database-long-term-retention.md) där säkerhetskopiorna lagras i ett säkert valv i upp till 10 år. Om det uppstår ett avbrott i datacentret som är värd för dina databasmiljöer kan du återställa databaser från [geo-redundanta kopior av säkerhetskopior som nyligen skapats](sql-database-recovery-using-backups.md). Om det behövs kan du också konfigurera [geo-redundanta läsbara repliker](sql-database-geo-replication-overview.md) i en eller flera regioner för snabb redundans vid avbrott i datacentret. Du kan också använda dessa repliker för snabbare inläsningsprestanda i olika geografiska regioner eller för [programuppgraderingar utan driftavbrott](sql-database-manage-application-rolling-upgrade.md). 

![SQL Database Geo-replikering](./media/sql-database-technical-overview/azure_sqldb_map.png)

Se [verksamhetskontinuitet](sql-database-business-continuity.md) för mer information om de olika funktionerna för verksamhetskontinuitet som finns för de olika tjänstnivåerna.

## <a name="secure-your-data"></a>Skydda dina data
SQL Server bygger på en tradition av datasäkerhet som SQL Database upprätthåller med funktioner som begränsar åtkomst, skyddar data och hjälper dig att övervaka aktiviteten. Se [Skydda din SQL-databas](sql-database-security-overview.md) för en snabb genomgång av de säkerhetsalternativ som finns i SQL Database. Se [Security Center för SQL Server Database Engine och SQL Database](https://msdn.microsoft.com/library/bb510589) för en mer genomgående insyn i säkerhetsfunktionerna. Och besök [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/security/) för information om Azures plattformssäkerhet.

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till SQL Database och besvarat frågan "Vad är SQL Database?", är du redo att:

* Se [Prissidan](https://azure.microsoft.com/pricing/details/sql-database/) för en kostnadsjämförelse och kostnadsberäknare för enskilda databaser och elastiska pooler.
* Läs mer om [elastiska pooler](sql-database-elastic-pool.md).
* Kom igång genom att [skapa din första databas](sql-database-get-started.md).
* Skapa din första app i C#, Java, Node.js, PHP, Python eller Ruby: [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)

