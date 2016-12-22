---
title: "Vad är SQL Database? Introduktion till SQL Database | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "introduktion till sql, sql-introduktion, vad är sql-databas"
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
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 37534ff6366bd519cec50bc033b2bcd8f8bda5c7


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Vad är SQL Database? Introduktion till SQL Database
SQL Database är en relationsdatabastjänst i molnet som är baserad på den marknadsledande Microsoft SQL Server-motorn med verksamhetskritiska funktioner. SQL Database levererar förutsägbar prestanda, skalbarhet utan avbrott, verksamhetskontinuitet och dataskydd. Allt detta med nästan obefintlig administration. Du kan fokusera på snabb apputveckling och att accelerera din tid till marknad, istället för att hantera virtuella datorer och infrastruktur. Eftersom den är baserad på [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx)-motorn, stöder SQL Database befintliga SQL Server-verktyg, bibliotek och API:er, vilket gör det lättare för dig att flytta och utöka till molnet.

Den här artikeln ger en introduktion till nyckelkoncept och funktioner för SQL Database som relaterar till prestanda, skalbarhet och hanterbarhet, med länkar till mer detaljerad information. Om du är redo att komma igång så kan du [skapa din första SQL-databas](sql-database-get-started.md) eller [skapa en elastisk pool](sql-database-elastic-pool-create-portal.md) på bara några minuter. Om du vill få en mer grundlig genomgång, kan du titta på den här 30-minuters videon.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON326/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Justera prestanda och skalning utan avbrott
SQL-databaser finns i *tjänstnivåerna* Basic, Standard och Premium. Varje tjänstnivå erbjuder [olika prestandanivåer och funktioner](sql-database-service-tiers.md) för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app på en liten databas för några kronor i månaden och sedan [ändra tjänstnivå](sql-database-scale-up.md) manuellt eller programmässigt varje gång som din app får viral spridning globalt, utan att några avbrott för vare sig din app eller dina kunder.

För många företag och appar räcker det att kunna skapa fristående databaser och reglera prestanda upp eller ner efter behov, speciellt där användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell.

[Elastiska pooler](sql-database-elastic-pool.md) i SQL Database löser det här problemet. Konceptet är enkelt. Du allokerar prestanda till en pool och betalar för poolens samlade prestanda i stället för prestanda för en fristående databas. Du behöver inte reglera databasprestanda hela tiden. Databaserna i poolen, som kallas *elastiska databaser*, skalar automatiskt upp eller ned för att möta efterfrågan. Elastiska databaser förbrukar, men överskrider inte begränsningarna i poolen. Dina kostnader förblir förutsägbara även om databasanvändningen inte är det. Dessutom kan du [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md) och därmed skala din app från några få databaser till tusentals, inom en budget som du själv styr över. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Vilket du än väljer, enskild eller elastisk, så är du inte låst. Du kan blanda fristående databaser med elastiska pooler och snabbt och enkelt ändra tjänstnivåerna för fristående databaser och pooler efter behov. Med den kraft och räckvidd som Azure har, kan du dessutom blanda andra Azure-tjänster med SQL Database, allt för att möta designbehoven för just din unika app, samt styra kostnader och upptäcka nya affärsmöjligheter.

Men hur kan man jämföra den relativa prestandan för databaser och databaspooler? Hur vet man rätt värden när man reglerar upp eller ner? Svaret finns i de inbyggda prestandaövervaknings- och aviseringsverktygen, kombinerat med prestandaklassificeringarna som baseras på databastransaktionsenheter (DTU:er) för fristående databaser och elastiska DTU:er (eDTU:er) för elastiska databaser och databaspooler, som gör att du snabbt kan utvärdera effekten av en upp- eller nedskalning utifrån dina aktuella behov eller baserat på prestandabehoven för projektet. Se [SQL Database, alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder](sql-database-service-tiers.md) för mer information.

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal [(SLA)](http://azure.microsoft.com/support/legal/sla/) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. Med varje SQL-databas drar du nytta av inbyggd säkerhet, feltolerans och dataskydd som du annars skulle behöva köpa, utforma, utveckla och hantera. Men det är möjligt, beroende på dina verksamhetskrav, att du ändå behöver ytterligare skyddslager för att se till att din app och din verksamhet kan återhämta sig snabbt vid en katastrof, ett fel eller ett annat avbrott. Med SQL Database erbjuder varje tjänstnivå en omfattande uppsättning funktioner för affärskontinuitet och alternativ som du kan använda för att komma igång. Du kan använda point-in-time-återställning för att återställa en databas till ett tidigare skede, så långt tillbaka som 35 dagar. Om det uppstår ett avbrott i datacentret som är värd för dina databasmiljöer kan du återställa databaser från geo-redundanta kopior av säkerhetskopior som nyligen skapats, eller växla över till databasrepliker i en annan region. Du kan också använda repliker för uppgraderingar eller flytt till andra regioner.

![SQL Database Geo-replikering](./media/sql-database-technical-overview/azure_sqldb_map.png)

Se [verksamhetskontinuitet](sql-database-business-continuity.md) för mer information om de olika funktionerna för verksamhetskontinuitet som finns för de olika tjänstnivåerna.

## <a name="secure-your-data"></a>Skydda dina data
SQL Server bygger på en tradition av stark datasäkerhet som SQL Database upprätthåller med funktioner som begränsar åtkomst, skyddar data och hjälper dig att övervaka aktiviteten. Se [Skydda din SQL-databas](sql-database-security.md) för en snabb genomgång av de säkerhetsalternativ som finns i SQL Database. Se [Security Center för SQL Server Database Engine och SQL Database](https://msdn.microsoft.com/library/bb510589) för en mer genomgående insyn i säkerhetsfunktionerna. Och besök [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/security/) för information om Azures plattformssäkerhet.

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till SQL Database och besvarat frågan "Vad är SQL Database?", är du redo att:

* En prisjämförelse och kalkylatorer för fristående databaser och elastiska pooler finns på [prissidan](https://azure.microsoft.com/pricing/details/sql-database/).
* Läs mer om [elastiska pooler](sql-database-elastic-pool.md).
* Kom igång genom att [skapa din första databas](sql-database-get-started.md).
* [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
* Skapa din första app i C#, Java, Node.js, PHP, Python eller Ruby: [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)



<!--HONumber=Dec16_HO2-->


