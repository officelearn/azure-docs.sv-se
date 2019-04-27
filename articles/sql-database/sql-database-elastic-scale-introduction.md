---
title: Skala ut med Azure SQL Database | Microsoft Docs
description: Programvara som en tjänst (SaaS)-utvecklare kan enkelt skapa elastiska, skalbara databaser i molnet med dessa verktyg
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 9f61748a489987bf6c3f38e8ebfdab660198e10a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585480"
---
# <a name="scaling-out-with-azure-sql-database"></a>Skala ut med Azure SQL Database
Du kan enkelt skala ut Azure SQL-databaser med hjälp av den **Elastic Database** verktyg. Dessa verktyg och funktioner kan du använda databasresurser av **Azure SQL Database** att skapa lösningar för transaktionsbelastningar och särskilt programvara som en tjänst (SaaS)-program. Elastic Database-funktioner består av den:

* [Klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md): Klientbiblioteket är en funktion som gör det möjligt att skapa och hantera shardade databaser.  Se [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).
* [Verktyg för elastisk databas dela / sammanslå](sql-database-elastic-scale-overview-split-and-merge.md): flyttar data mellan shardade databaser. Det här verktyget är användbart för att flytta data från en databas för flera innehavare till en enda klient databas (eller vice versa). Se [elastiska databaser dela och slå samman verktyget självstudien](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Elastic Database-jobb](sql-database-elastic-jobs-overview.md) (förhandsversion): Du kan använda jobb för att hantera stora mängder Azure SQL-databaser. Enkelt utföra administrativa åtgärder, till exempel schemaändringar, hantering av autentiseringsuppgifter, uppdateringar av referensdata, insamling av prestandadata eller klient (kund) telemetriinsamling med jobb.
* [Elastisk databasfråga](sql-database-elastic-query-overview.md) (förhandsversion): Kan du köra en Transact-SQL-fråga som sträcker sig över flera databaser. På så sätt kan anslutningen till verktyg som Excel, Power BI, Tableau, osv.
* [Elastiska transaktioner](sql-database-elastic-transactions-overview.md): Den här funktionen kan du köra transaktioner som sträcker sig över flera databaser i Azure SQL Database. Transaktioner elastiska databaser är tillgängliga för .NET-program med hjälp av ADO .NET och integrera med en bekant programmering upplevelse med hjälp av den [System.Transaction klasser](https://msdn.microsoft.com/library/system.transactions.aspx).

Följande bild visar en arkitektur som innehåller den **elastiska databasfunktionerna** i förhållande till en uppsättning databaser.

I den här bilden representerar färger för databasen scheman. Databaserna med samma färg delar samma schema.

1. En uppsättning **Azure SQL-databaser** finns på Azure med horisontell partitionering arkitektur.
2. Den **Elastic Database-klientbiblioteket** används för att hantera en uppsättning fragment.
3. En delmängd av databaserna placeras i en **elastisk pool**. (Se [vad är en pool?](sql-database-elastic-pool.md)).
4. En **Elastic Database-jobb** körs schemalagd eller ad hoc-T-SQL-skript mot alla databaser.
5. Den **dela / sammanslå verktyget** används för att flytta data från en shard till en annan.
6. Den **elastisk databasfråga** kan du skriva en fråga som sträcker sig över alla databaser i fragment uppsättningen.
7. **Elastiska transaktioner** kan du köra transaktioner som sträcker sig över flera databaser. 

![Elastic Database-verktyg][1]

## <a name="why-use-the-tools"></a>Varför ska man använda verktygen?
Uppnå elasticitet och skala för molnprogram har enkelt för virtuella datorer och blob-lagring – helt enkelt lägga till eller ta bort enheter eller öka power. Men det har varit en utmaning för tillståndskänsliga databearbetning i relationsdatabaser. Det här utmaningarna i dessa scenarier:

* Växer och krymper kapacitet för relationsdatabas som en del av din arbetsbelastning.
* Hantera anslutningar som kan uppstå som påverkar en viss delmängd data – till exempel en upptagen end-kund (klient).

Scenarier som dessa har traditionellt har utförts genom att investera i större skala databasservrar programmet. Det här alternativet är dock begränsad i molnet där alla bearbetningen sker på fördefinierade vanlig maskinvara. (I stället distribuera data och bearbetning över flera identiskt strukturerade databaser en skalbar mönstret kallas ”horisontell partitionering”) ger ett alternativ till traditionella sätt skala upp både vad gäller kostnaden och elasticitet.

## <a name="horizontal-and-vertical-scaling"></a>Vågrät och lodrät skalning
Följande bild visar vågräta och lodräta dimensioner för skalning, vilka är de grundläggande sätt som de elastiska databaserna kan skalas.

![Vågrät jämfört med vertikal skalbarhet][2]

Horisontell skalning syftar till att lägga till eller ta bort databaser för att justera kapacitet eller övergripande prestanda, kallas även ”skalning”. Horisontell partitionering, där data är partitionerad över en uppsättning identiskt strukturerade databaser är ett vanligt sätt att implementera horisontell skalning.  

Vertikal skalning refererar till öka eller minska beräkningsstorleken på en enskild databas, även kallat ”skala upp”.

De flesta databasprogram för molnskala använder en kombination av följande två metoder. En programvara som en tjänst kan till exempel använda horisontell skalning för att etablera nya slutkunder och vertikal skalning så att varje slutanvändarnas databas kan öka eller minska resurser efter behov av arbetsbelastningen.

* Horisontell skalning hanteras med hjälp av den [Elastic Database-klientbiblioteket](sql-database-elastic-database-client-library.md).
* Vertikal skalning sker med hjälp av Azure PowerShell-cmdletar för att ändra tjänstnivån, eller genom att placera databaser i en elastisk pool.

## <a name="sharding"></a>Horisontell partitionering
*Horisontell partitionering* är en teknik för att distribuera stora mängder identiskt strukturerade data över ett antal oberoende databaser. Det används särskilt ofta med cloud-utvecklare som skapar programvara som en tjänst (SAAS)-erbjudanden för slutkunder eller företag. Dessa slutkunder kallas ofta ”klientorganisationer”. Horisontell partitionering kan krävas för valfritt antal orsaker:  

* Den totala mängden data är för stor för att rymmas inom begränsningarna för en enskild databas
* Transaktionsdataflöde av den övergripande arbetsbelastningen överskrider kapaciteterna för en enskild databas
* Klienter kan kräva fysiskt isolerade från varandra, så separata databaser behövs för varje klient
* Olika delar i en databas kan behöva finnas i olika geografiska områden för efterlevnad, prestanda eller geopolitiska orsaker.

I andra scenarier, t.ex inmatning av data från distribuerade enheter, kan horisontell partitionering användas för att fylla en uppsättning databaser som är ordnade tillfälligt. En separat databas kan till exempel vara dedikerad till varje dag eller en vecka. I så fall nyckeln för horisontell partitionering kan vara ett heltal som representerar den (finns i alla rader i tabellerna shardade) och frågor som hämtar information för ett datumintervall skickas av programmet till delmängden av databaser som täcker intervallet i fråga.

Horisontell partitionering fungerar bäst när varje transaktion i ett program kan vara begränsad till ett enstaka värde för nyckeln för horisontell partitionering. Detta säkerställer att alla transaktioner är lokala för en viss databas.

## <a name="multi-tenant-and-single-tenant"></a>Flera innehavare och en enda klient
Vissa program använder den enklaste metoden för att skapa en separat databas för varje klient. Den här metoden är den **mönster för horisontell partitionering av enskild klient** som ger isolering, möjlighet för säkerhetskopiering/återställning och resursgrupp skalning med precisionen för klienten. Med enskild klient partitionering har varje databas är associerad med en specifik klient-ID-värdet (eller kunden nyckelvärde), men nyckeln behöver inte alltid i själva informationen. Det är programmets ansvar att dirigera varje begäran till lämplig databas - och klientbiblioteket kan förenkla detta.

![Enskild klient jämfört med flera innehavare][4]

Andra scenarier packa ihop flera klienter i databaser i stället för att isolera dem i separata databaser. Det här mönstret är en typisk **mönster för flera innehavare horisontell partitionering** - och den kan styras av det faktum att ett program hanterar stort antal små klienter. I flera innehavare partitionering har är raderna i databastabellerna utformade att utföra en nyckel som identifierar de klient-ID eller shardingnyckel. Igen, appnivån ansvarar för routning av begäran om en klient till rätt databas och det stöds av klientbiblioteket för elastiska databaser. Dessutom säkerhet på radnivå kan användas för att filtrera vilka rader som varje klient kan komma åt - mer information finns i [program för flera innehavare med elastic database-verktyg och säkerhet på radnivå](sql-database-elastic-tools-multi-tenant-row-level-security.md). Distribuera data mellan databaser kan behövas med mönster för flera innehavare horisontell partitionering och möjliggörs med hjälp av verktyget Dela och slå samman för elastic database. Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Flytta data från flera till enskild innehavare databaser
När du skapar ett SaaS-program, är det vanligt att erbjuda en utvärderingsversion av programvaran för potentiella kunder. I det här fallet är det kostnadseffektivt att använda en databas för flera innehavare för data. När en potentiell kund blir en kund, är en enda klient-databas dock bättre eftersom det ger bättre prestanda. Om kunden har skapat data under utvärderingsperioden, använder du den [dela / sammanslå verktyget](sql-database-elastic-scale-overview-split-and-merge.md) att flytta data från flera innehavare till den nya databasen för enstaka klientorganisationer.

## <a name="next-steps"></a>Nästa steg
En exempelapp som visar klientbiblioteket finns [Kom igång med elastiska Databasverktyg](sql-database-elastic-scale-get-started.md).

Om du vill konvertera befintliga databaser för att använda verktyg, se [migrera befintliga databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md).

För att visa egenskaperna för den elastiska poolen, se [pris- och prestandaöverväganden för en elastisk pool](sql-database-elastic-pool.md), eller skapa en ny pool med [elastiska pooler](sql-database-elastic-pool-manage-portal.md).  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

