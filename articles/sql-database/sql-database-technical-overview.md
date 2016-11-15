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
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 23d68a523fa9a79d2b5154eef04a5f6e706bdeb2


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>Vad är SQL Database? Introduktion till SQL Database
SQL Database är en relationsdatabastjänst i molnet som är baserad på den marknadsledande Microsoft SQL Server-motorn med verksamhetskritiska funktioner. SQL Database levererar förutsägbar prestanda, skalbarhet utan avbrott, verksamhetskontinuitet och dataskydd. Allt detta med nästan obefintlig administration. Du kan fokusera på snabb apputveckling och att accelerera din tid till marknad, istället för att hantera virtuella datorer och infrastruktur. Eftersom den är baserad på [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx)-motorn, stöder SQL Database befintliga SQL Server-verktyg, bibliotek och API:er, vilket gör det lättare för dig att flytta och utöka till molnet.

Den här artikeln ger en introduktion till nyckelkoncept och funktioner för SQL Database som relaterar till prestanda, skalbarhet och hanterbarhet, med länkar till mer detaljerad information. Om du är redo att komma igång så kan du [Skapa din första SQL-databas](sql-database-get-started.md) eller [Skapar en elastisk databaspool](sql-database-elastic-pool-create-portal.md) på några minuter. Om du vill få en mer grundlig genomgång, kan du titta på den här 30-minuters videon.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON326/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>Justera prestanda och skalning utan avbrott
SQL-databaser finns i *tjänstnivåerna* Basic, Standard och Premium. Varje tjänstnivå erbjuder [olika prestandanivåer och funktioner](sql-database-service-tiers.md) för att stödja lätta till tunga arbetsbelastningar för databaser. Du kan skapa din första app på en liten databas för några kronor i månaden och sedan [ändra tjänstnivå](sql-database-scale-up.md) manuellt eller programmässigt varje gång som din app får viral spridning globalt, utan att några avbrott för vare sig din app eller dina kunder.

För många företag och appar, räcker det att kunna skapa databaser och reglera prestandan för enskilda databaser upp eller ner efter behov, speciellt där användningsmönster är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell.

[Elastiska pooler](sql-database-elastic-pool.md) i SQL Database löser det här problemet. Konceptet är enkelt. Du allokerar prestanda till en pool och betalar för den samlade prestandan för poolen istället för prestandan för en enskild databas. Du behöver inte reglera databasprestanda hela tiden. Databaserna i poolen, som kallas *elastiska databaser*, skalar automatiskt upp eller ned för att möta efterfrågan. Elastiska databaser förbrukar, men överskrider inte begränsningarna i poolen. Dina kostnader förblir förutsägbara även om databasanvändningen inte är det. Dessutom kan du [lägga till och ta bort databaser i poolen](sql-database-elastic-pool-manage-portal.md) och därmed skala din app från några få databaser till tusentals, inom en budget som du själv styr över. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Vilket du än väljer, enskild eller elastisk, så är du inte låst. Du kan blanda enskilda databaser med elastiska databaspooler och ändra tjänstnivåer för enskilda databaser och pooler för att skapa innovativa upplägg. Med den kraft och räckvidd som Azure har, kan du dessutom blanda Azure-tjänster med SQL Database, allt för att möta designbehoven för just din unika, moderna app, samt styra kostnader och upptäcka nya affärsmöjligheter.

Men hur kan man jämföra den relativa prestandan för databaser och databaspooler? Hur vet man rätt värden när man reglerar upp eller ner? Svaret är Database Transaction Units (DTU:er) för enskilda databaser och elastiska DTU:er (eDTU:er) för elastiska databaser och databaspooler. Se [SQL Database, alternativ och prestanda: Förstå vad varje tjänstnivå erbjuder](sql-database-service-tiers.md) för mer information.

## <a name="keep-your-app-and-business-running"></a>Håll igång din app och din verksamhet
Azures branschledande serviceavtal [(SLA)](http://azure.microsoft.com/support/legal/sla/) med 99,99 % tillgänglighet, drivs av ett globalt nätverk med Microsoft-hanterade datacenter som gör att din app är igång 24/7. Med varje SQL-databas, drar du nytta av inbyggt dataskydd och feltolerans som du annars skulle behöva utforma, köpa, bygga och hantera. Men det är möjligt, beroende på dina verksamhetskrav, att du ändå behöver ytterligare skyddslager för att tillse att din app och din verksamhet kan återhämta sig snabbt vid en katastrof, ett fel eller något annat. Med SQL Database, erbjuder varje tjänstnivå en meny med funktioner som du kan använda för att komma igång och fortsätta vara igång. Du kan använda point-in-time-återställning för att återställa en databas till ett tidigare skede, så långt tillbaka som 35 dagar. Om datacentret som är värd för dina databaser skulle få ett avbrott, kan du dessutom växla över till databasrepliker i en annan region. Eller så kan du använda repliker för uppgraderingar eller flytt till andra regioner.

![SQL Database Geo-replikering](./media/sql-database-technical-overview/azure_sqldb_map.png)

Se [verksamhetskontinuitet](sql-database-business-continuity.md) för mer information om de olika funktionerna för verksamhetskontinuitet som finns för de olika tjänstnivåerna.

## <a name="secure-your-data"></a>Skydda dina data
SQL Server bygger på en tradition av stark datasäkerhet som SQL Database upprätthåller med funktioner som begränsar åtkomst, skyddar data och hjälper dig att övervaka aktiviteten. Se [Skydda din SQL-databas](sql-database-security.md) för en snabb genomgång av de säkerhetsalternativ som finns i SQL Database. Se [Security Center för SQL Server Database Engine och SQL Database](https://msdn.microsoft.com/library/bb510589) för en mer genomgående insyn i säkerhetsfunktionerna. Och besök [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/security/) för information om Azures plattformssäkerhet.

## <a name="next-steps"></a>Nästa steg
Nu när du har läst en introduktion till SQL Database och besvarat frågan "Vad är SQL Database?", är du redo att:

* Se [Prissidan](https://azure.microsoft.com/pricing/details/sql-database/) för en kostnadsjämförelse och kostnadsberäknare för enskilda databaser och elastiska databaser.
* Läs mer om [elastiska pooler](sql-database-elastic-pool.md).
* Kom igång genom att [skapa din första databas](sql-database-get-started.md).
* [Anslut och fråga med SSMS](sql-database-connect-query-ssms.md)
* Skapa din första app i C#, Java, Node.js, PHP, Python eller Ruby: [Anslutningsbibliotek för SQL Database och SQL Server](sql-database-libraries.md)
* Se ett index med rubriker och beskrivningar för [Alla ämnen för Azure SQL Database-tjänsten](sql-database-index-all-articles.md).




<!--HONumber=Nov16_HO2-->


