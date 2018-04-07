---
title: Skala ut med Azure SQL Database | Microsoft Docs
description: Programvara som en tjänst (SaaS)-utvecklare kan enkelt skapa elastiska och skalbara databaser i molnet genom att använda dessa verktyg
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4944c1c017dbb59b7880a73bce7d0a9b0d972b3f
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="scaling-out-with-azure-sql-database"></a>Skala ut med Azure SQL Database
Du kan enkelt skala ut Azure SQL-databaser med hjälp av den **elastisk databas** verktyg. Dessa verktyg och funktioner kan du använda databasresurser av **Azure SQL Database** att skapa lösningar för transaktionell arbetsbelastningar och särskilt programvara som en tjänst (SaaS)-program. Elastiska databasfunktioner består av den:

* [Klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md): klientbiblioteket är en funktion som gör att du kan skapa och underhålla delat databaser.  Se [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).
* [Elastisk databas dela merge tool](sql-database-elastic-scale-overview-split-and-merge.md): flyttar data mellan delat. Detta är användbart för att flytta data från en databas med flera innehavare till en enskild klient-databas (eller vice versa). Se [elastisk databas delade dokument verktyget kursen](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Den elastiska databasen jobb](sql-database-elastic-jobs-overview.md) (förhandsversion): använder jobb för att hantera ett stort antal Azure SQL-databaser. Enkelt utföra administrativa åtgärder som till exempel schemaändringar, hantering av autentiseringsuppgifter, referens uppdateringar, insamling av prestandadata eller klient (kunden) telemetri samlingen med hjälp av jobben.
* [Elastisk databasfrågan](sql-database-elastic-query-overview.md) (förhandsversion): gör att du kan köra en Transact-SQL-fråga som sträcker sig över flera databaser. Detta gör anslutningen till reporting verktyg som Excel, PowerBI, Tableau, osv.
* [Elastisk transaktioner](sql-database-elastic-transactions-overview.md): den här funktionen kan du köra transaktioner som sträcker sig över flera databaser i Azure SQL Database. Elastisk databastransaktioner är tillgängliga för .NET-program med hjälp av ADO .NET och integrera med den gamla programmering upplevelse med den [System.Transaction klasser](https://msdn.microsoft.com/library/system.transactions.aspx).

Följande bild visar en arkitektur som innehåller den **elastiska databasfunktioner** i förhållande till en samling av databaser.

I den här bilden representerar färgerna i databasen scheman. Databaser med samma färg delar samma schema.

1. En uppsättning **Azure SQL-databaser** finns på Azure med hjälp av arkitekturen för horisontell partitionering.
2. Den **klientbibliotek för elastisk databas** används för att hantera Fragmentera.
3. En delmängd av databaserna som placeras i en **elastisk pool**. (Se [vad är en pool?](sql-database-elastic-pool.md)).
4. En **elastisk databas jobbet** körs schemalagda eller ad hoc-T-SQL-skript mot alla databaser.
5. Den **för delade sökvägssammanslagning** används för att flytta data från en Fragmentera till en annan.
6. Den **elastisk databasfrågan** gör att du kan skriva en fråga som omfattar alla databaser i uppsättningen Fragmentera.
7. **Elastisk transaktioner** gör att du kan köra transaktioner som sträcker sig över flera databaser. 

![Elastic Database-verktyg][1]

## <a name="why-use-the-tools"></a>Varför använda verktygen?
Uppnå elasticitet och skala för molnprogram har enkla för virtuella datorer och blob-lagring – helt enkelt lägga till eller subtrahera enheter, eller öka power. Men det har varit en utmaning för tillståndskänsliga databearbetning i relationsdatabaser. Det här utmaningarna i följande scenarier:

* Växande och minska kapacitet för relationsdatabas en del av din arbetsbelastning.
* Hantera anslutningar som kan uppstå som påverkar en delmängd av data -, till exempel en upptagen end-kund (klient).

Traditionellt berörs scenarier som dessa av investera i större skala databasservrar att programmet. Det här alternativet är dock begränsad i molnet där all bearbetning som händer på fördefinierade vanlig hårdvara. Distribuera data och bearbeta över flera identiskt strukturerade databaser i stället tillhandahåller ett alternativ till traditionella metoder skala upp både vad gäller kostnad och elasticitet (en skalbar mönstret kallas ”delning”).

## <a name="horizontal-and-vertical-scaling"></a>Vågräta och lodräta skalning
Följande bild visar vågräta och lodräta dimensioner för skalning, som de olika sätt som de elastiska databaserna kan skalas.

![Vågräta och lodräta skala ut][2]

Teckenbredden avser att lägga till eller ta bort databaser för att justera kapacitet eller övergripande prestanda. Detta kallas även ”skalning”. Horisontell partitionering, där data är partitionerad över en mängd identiskt strukturerade databaser är ett vanligt sätt att implementera teckenbredden.  

Lodrät skalning refererar till öka eller minska prestandanivåerna för en individuell databas – kallas även ”skala upp”.

De flesta databasprogram i molnskala använda en kombination av följande två metoder. En programvara som en tjänst kan till exempel använda teckenbredden för att etablera nya end-kunder och lodrät skalning så att varje end-kund databas kan öka eller minska resurser som krävs av arbetsbelastningen.

* Teckenbredden hanteras med hjälp av den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md).
* Lodrät skalning åstadkoms med hjälp av Azure PowerShell-cmdlets för att ändra tjänstnivån, eller placera databaser i en elastisk pool.

## <a name="sharding"></a>Horisontell partitionering
*Horisontell partitionering* är en teknik som du distribuerar stora mängder identiskt strukturerade data över ett antal oberoende databaser. Det är särskilt populära med molnet utvecklare som skapar programvara som en tjänst (SAAS)-erbjudanden för slutanvändare och kunder eller företag. Dessa slutkunder kallas ofta ”innehavare”. Horisontell partitionering kan krävas av olika orsaker:  

* Den totala mängden data är för stort för att rymmas inom begränsningarna för en enskild databas
* Transaktionen genomströmning av övergripande belastningen överskrider kapaciteterna för en enskild databas
* Klienter kan kräva fysiska isolerade från varandra, så separata databaser krävs för varje klient
* Olika avsnitt i en databas behöver finnas i olika geografiska områden för kompatibilitet, prestanda eller geopolitiska orsaker.

I andra scenarier, till exempel införandet av data från distribuerade enheter kan delning användas för att fylla en uppsättning databaser som är ordnade närvarande. En separat databas kan till exempel vara dedikerade till varje dag och vecka. I så fall horisontell partitionering nyckeln kan vara ett heltal som representerar den (finns i alla rader i tabellerna delat) och frågor som hämtar information för ett datum måste dirigeras av programmet till delmängden av databaser som täcker intervallet i fråga.

Delning fungerar bäst när varje transaktion i ett program kan vara begränsad till ett värde för en nyckel för horisontell partitionering. Detta säkerställer att alla transaktioner är lokala för en viss databas.

## <a name="multi-tenant-and-single-tenant"></a>Flera innehavare och single-klient
Vissa program använder det enklaste sättet att skapa en separat databas för varje klient. Det här är den **mönster för enstaka klient horisontell partitionering** som ger isolering, möjlighet för säkerhetskopiering/återställning och resurs skalning på Granulariteten för innehavaren. Varje databas är kopplad till en viss klient ID-värde (eller kunden nyckelvärde) med enda innehavare delning, men nyckeln behöver inte alltid finns i själva informationen. Det är programmets ansvar för att dirigera varje förfrågan till rätt databas - och klientbiblioteket kan förenkla detta.

![En organisation jämfört med flera innehavare][4]

Andra scenarier packa ihop flera innehavare i databaser i stället för att isolera dem i separata databaser. Det här är en typisk **mönster för flera innehavare horisontell partitionering** - och den styrs av det faktum att ett program hanterar stora mängder små innehavare. I flera innehavare delning är raderna i databastabellerna alla utformade för att utföra en nyckel som identifierar de klient-ID eller en nyckel för horisontell partitionering. Återigen nivån programmet ansvarar för omdirigering av en klient-begäran till rätt databas och det stöds av klientbiblioteket för elastisk databas. Dessutom säkerhet på radnivå kan användas för att filtrera vilka rader som varje klient kan komma åt - mer information finns i [program med flera klienter med elastiska Databasverktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md). Distribuera data mellan databaser kan behövas med flera innehavare horisontell partitionering mönster och detta möjliggörs med hjälp av verktyget för elastisk databas delade dokument. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Flytta data från flera till single-innehavare databaser
När du skapar ett SaaS-program, är det vanligt att erbjuda en utvärderingsversion av programvaran för potentiella kunder. I det här fallet är det kostnadseffektivt att använda en databas med flera innehavare för data. När en potentiell kund blir en kund, är en enskild klient-databas dock bättre eftersom det ger bättre prestanda. Om kunden har skapat data under utvärderingsperioden, använder du den [för delade sökvägssammanslagning](sql-database-elastic-scale-overview-split-and-merge.md) att flytta data från flera innehavare till den nya databasen för en klient.

## <a name="next-steps"></a>Nästa steg
En exempelapp som visar klientbiblioteket finns [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).

Om du vill konvertera befintliga databaser för att använda verktygen finns [migrera befintliga databaser att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md).

Egenskaperna för den elastiska poolen finns [pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool.md), eller skapa en ny pool med [elastiska pooler](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

