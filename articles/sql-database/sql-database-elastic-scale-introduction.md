---
title: Skala ut
description: SaaS-utvecklare (Software as a Service) kan enkelt skapa elastiska, skalbara databaser i molnet med hjälp av dessa verktyg
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061652"
---
# <a name="scaling-out-with-azure-sql-database"></a>Skala ut med Azure SQL Database
Du kan enkelt skala ut Azure SQL-databaser med hjälp av verktygen **för elastisk databas.** Med de här verktygen och funktionerna kan du använda databasresurserna i **Azure SQL Database** för att skapa lösningar för transaktionsarbetsbelastningar, och särskilt SaaS-program (Software as a Service). Elastiska databasfunktioner består av:

* [Elastisk databasklientbibliotek:](sql-database-elastic-database-client-library.md)Klientbiblioteket är en funktion som gör att du kan skapa och underhålla fragmenterade databaser.  Se [Komma igång med elastiska databasverktyg](sql-database-elastic-scale-get-started.md).
* [Elastiskt databasdelningskopplingsverktyg:](sql-database-elastic-scale-overview-split-and-merge.md)flyttar data mellan fragmenterade databaser. Det här verktyget är användbart för att flytta data från en databas med flera innehavare till en databas med en enda klientorganisation (eller vice versa). Se [Självstudiekurs för split-merge-verktyg för elastisk databas](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Elastiska databasjobb:](elastic-jobs-overview.md)Använd jobb för att hantera ett stort antal Azure SQL-databaser. Utför enkelt administrativa åtgärder som schemaändringar, autentiseringsuppgifter, referensdatauppdateringar, prestandadatainsamling eller klient-(kund) telemetrisamling med jobb.
* [Elastisk databasfråga](sql-database-elastic-query-overview.md) (förhandsgranskning): Gör att du kan köra en Transact-SQL-fråga som sträcker sig över flera databaser. Detta möjliggör anslutning till rapporteringsverktyg som Excel, Power BI, Tableau, etc.
* [Elastiska transaktioner:](sql-database-elastic-transactions-overview.md)Med den här funktionen kan du köra transaktioner som sträcker sig över flera databaser i Azure SQL Database. Elastiska databastransaktioner är tillgängliga för .NET-program som använder ADO .NET och integreras med den välbekanta programmeringsupplevelsen med hjälp av [klasserna System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

Följande bild visar en arkitektur som innehåller **elastiska databasfunktioner** i förhållande till en samling databaser.

I den här bilden representerar färgerna i databasen scheman. Databaser med samma färg delar samma schema.

1. En uppsättning **Azure SQL-databaser** finns på Azure med hjälp av fragmenteringsarkitektur.
2. **Klientbiblioteket för elastisk databas** används för att hantera en fragmentuppsättning.
3. En delmängd av databaserna läggs i en **elastisk pool**. (Se [Vad är en pool?](sql-database-elastic-pool.md)).
4. Ett **elastiskt databasjobb** körs schemalagda eller ad hoc-T-SQL-skript mot alla databaser.
5. Verktyget **för delad koppling** används för att flytta data från en fragment till en annan.
6. **Med frågan Elastisk databas** kan du skriva en fråga som sträcker sig över alla databaser i fragmentuppsättningen.
7. **Med elastiska transaktioner** kan du köra transaktioner som sträcker sig över flera databaser. 

![Elastic Database-verktyg][1]

## <a name="why-use-the-tools"></a>Varför använda verktygen?
Att uppnå elasticitet och skala för molnprogram har varit enkelt för virtuella datorer och bloblagring - helt enkelt lägga till eller subtrahera enheter, eller öka kraften. Men det har varit en utmaning för tillståndskänslig databehandling i relationsdatabaser. Utmaningar uppstod i dessa scenarier:

* Växande och krympande kapacitet för relationsdatabasdelen av din arbetsbelastning.
* Hantera aktiveringspunkter som kan uppstå som påverkar en viss delmängd av data , till exempel en upptagen slutkund (klient).

Traditionellt har scenarier som dessa åtgärdats genom att investera i storskaliga databasservrar för att stödja programmet. Det här alternativet är dock begränsat i molnet där all bearbetning sker på fördefinierad varumaskinvara. Distribuera data och bearbetning över många identiskt strukturerade databaser (ett skalningsmönster som kallas "sharding") är istället ett alternativ till traditionella uppskalningsmetoder både när det gäller kostnad och elasticitet.

## <a name="horizontal-and-vertical-scaling"></a>Vågrät och lodrät skalning
Följande bild visar skalningens vågräta och vertikala dimensioner, vilket är de grundläggande sätten att skala elastiska databaser.

![Vågrätt kontra lodrät utskalning][2]

Med vågrät skalning avses att lägga till eller ta bort databaser för att justera kapacitet eller övergripande prestanda, även kallad "skalning ut". Sharding, där data är partitionerad över en samling identiskt strukturerade databaser, är ett vanligt sätt att implementera horisontell skalning.  

Vertikal skalning avser att öka eller minska beräkningsstorleken för en enskild databas, även känd som "skala upp".

De flesta molnskala databasprogram använder en kombination av dessa två strategier. Ett program för programvara som en tjänst kan till exempel använda horisontell skalning för att etablera nya slutkunder och vertikal skalning så att varje slutkunds databas kan växa eller krympa resurser efter behov av arbetsbelastningen.

* Vågrät skalning hanteras med hjälp av [klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md).
* Vertikal skalning utförs med Azure PowerShell-cmdletar för att ändra tjänstnivån eller genom att placera databaser i en elastisk pool.

## <a name="sharding"></a>Horisontell partitionering
*Horisontell partitionering* är en teknik som används för att distribuera stora mängder identiskt strukturerade data över ett antal oberoende databaser. Det är särskilt populärt med molnutvecklare som skapar SAAS-erbjudanden (Software as a Service) för slutkunder eller företag. Dessa slutkunder kallas ofta "hyresgäster". Skärva kan krävas av flera olika skäl:  

* Den totala mängden data är för stor för att passa in i begränsningarna i en enskild databas
* Transaktionsflödet för den totala arbetsbelastningen överskrider funktionerna i en enskild databas
* Klienter kan kräva fysisk isolering från varandra, så separata databaser behövs för varje klient
* Olika delar av en databas kan behöva finnas i olika geografiska områden av efterlevnads-, prestanda- eller geopolitiska skäl.

I andra scenarier, till exempel inmatning av data från distribuerade enheter, kan sharding användas för att fylla en uppsättning databaser som är ordnade tidsmässigt. En separat databas kan till exempel ägnas åt varje dag eller vecka. I så fall kan sharding-nyckeln vara ett heltal som representerar datumet (som finns på alla rader i de fragmenterade tabellerna) och frågor som hämtar information för ett datumintervall måste dirigeras av programmet till den delmängd av databaser som täcker intervallet i fråga.

Sharding fungerar bäst när varje transaktion i ett program kan begränsas till ett enda värde för en sharding-nyckel. Det säkerställer att alla transaktioner är lokala för en viss databas.

## <a name="multi-tenant-and-single-tenant"></a>Flera innehavare och enklöver
Vissa program använder den enklaste metoden för att skapa en separat databas för varje klient. Den här metoden är det **enda klientshardingmönstret** som ger isolering, säkerhetskopiering/återställningsförmåga och resursskalning vid klientens kornighet. Med en enda klientshardring associeras varje databas med ett specifikt klient-ID-värde (eller kundnyckelvärde), men den nyckeln behöver inte alltid finnas i själva data. Det är programmets ansvar att dirigera varje begäran till lämplig databas - och klientbiblioteket kan förenkla detta.

![En klient kontra flera innehavare][4]

Andra scenarier packar flera klienter tillsammans i databaser, i stället för att isolera dem i separata databaser. Det här mönstret är ett typiskt **shardingmönster för flera innehavare** - och det kan drivas av det faktum att ett program hanterar ett stort antal små klienter. I sharding med flera innehavare är raderna i databastabellerna alla utformade för att ha en nyckel som identifierar klient-ID eller sharding-nyckeln. Återigen är programnivån ansvarig för att dirigera en klientbegäran till lämplig databas, och detta kan stödjas av klientbiblioteket för elastiska databaser. Dessutom kan säkerhet på radnivå användas för att filtrera vilka rader varje klient kan komma åt – mer information finns i Program för [flera innehavare med elastiska databasverktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md). Omfördelning av data mellan databaser kan behövas med sharding-mönstret för flera innehavare och underlättas av det elastiska verktyget för delad sammanfogning av databaser. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Flytta data från flera till enarberättigade databaser
När du skapar en SaaS-applikation är det typiskt att erbjuda potentiella kunder en testversion av programvaran. I det här fallet är det kostnadseffektivt att använda en databas med flera innehavare för data. Men när en potentiell kund blir kund är en databas med en klient bättre eftersom den ger bättre prestanda. Om kunden hade skapat data under utvärderingsperioden använder du [verktyget för delad koppling](sql-database-elastic-scale-overview-split-and-merge.md) för att flytta data från flera innehavare till den nya databasen med en klient.

## <a name="next-steps"></a>Nästa steg
En exempelapp som demonstrerar klientbiblioteket finns i [Komma igång med elastiska databasverktyg](sql-database-elastic-scale-get-started.md).

Information om hur du konverterar befintliga databaser för att använda verktygen finns i [Migrera befintliga databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md).

Information om hur du ser detaljerna i den elastiska poolen finns i [Pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool.md)eller skapa en ny pool med [elastiska pooler](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

